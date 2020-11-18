---
title: "Fixing Oracle DB Link Certificate Errors in RDS"
date: 2020-11-18T01:19:35Z
category: ["oracle"]
---

![](/images/rds_tcps_db_link_title.jpg)

After upgrading my AWS RDS instance from Oracle 11g
Oracle 19c, my DB Link stopped working with a certificate error.  Uh-oh.

The symptoms of the problem that I encountered are best summed up by this SQL*Plus snippet:

```oraclesqlplus
SQL> select * from dual@test1;
select * from dual@test1
                   *
ERROR at line 1:
ORA-29024: Certificate validation failure
```

A bit of context: 

The above is testing an Oracle Database Link "test1" which was created using the TCPS 
protocol (i.e. an Oracle SQL*Net connection via TLS).

As such the DB link would have looked like so:

```
(DESCRIPTION=(ADDRESS=(PROTOCOL=TCPS)(HOST=other-oracle-database)(PORT=2484))
  (CONNECT_DATA=(SERVER=DEDICATED)(SERVICE_NAME=serviceName))
  (SECURITY=(SSL_SERVER_CERT_DN="c=GB,st=Somewhere,l=Loc,o=Org,ou=XXX,cn=other-oracle-database.com")))
``` 

And this was working when RDS was Oracle 11g (and that target database is 11g).
But with the upgrade to 19c, it stopped working with the aforementioned ORA-29024 error.

# What's happening?

Changing the Oracle RDS instance `sqlnetora.trace_level_server` parameter to `SUPPORT` revealed the following

```
SSL_Alert:write - fatal - unknown certificate authority
SSL_Info:error in SSL3 certificate verify A
nzos_Handshake:Handshake returned failure code -1
nzos_Handshake:Handshake error(cb=0,rc=-1,rer=1,ser=336134278) - error:14090086:SSL routines:SSL3_GET_SERVER_CERTIFICATE:certificate verify failed
nzos_Handshake:exit
ntzdosecneg:SSL handshake failed with error 29024.
```

# Actually finding the problem

Finding the above was not straightforward, as I found I wasn't able to search all the trace files easily (SUPPORT creates
lots of them).  So I wrote the following into my SQL*Plus:

```oraclesqlplus
declare
  c number;
begin
  for i in (select filename FROM TABLE(rdsadmin.rds_file_util.listdir('BDUMP')) where mtime > sysdate - (10/(24*60)) and type = 'file')
  loop
    SELECT count(*) into c 
      FROM TABLE (rdsadmin.rds_file_util.read_text_file( p_directory => 'BDUMP', p_filename  => i.filename)) 
     where text like '%SSL handshake failed%';
    if c > 0 then
      dbms_output.put_line(i.filename || ' -> ' || c);
    end if;
  end loop;
end;
```

This would search through all the files in the BDUMP directory (where the trace files are created) that were modified
in the last 10 minutes and would spit out the filenames containing the string `SSL handshake failed`.  Once I know the
filename, I'd download the file through the RDS console to help diagnose the issue.

# Don't get caught out!

Word of warning - when switching an RDS instance logging to SUPPORT, expect a lot of disk space to
be consumed by `.trc` files even if there is no activity on the database.  Think Gigabytes a day. 
I found this out when the DB stopped working overnight after hitting `Storage Full`.  When that happens,
logging into the RDS instance becomes impossible:

```
SQL*Plus: Release 18.0.0.0.0 - Production on Thu Nov 12 09:36:29 2020
Version 18.3.0.0.0

Copyright (c) 1982, 2018, Oracle.  All rights reserved.

ERROR:
ORA-00257: Archiver error. Connect AS SYSDBA only until resolved.
```

Of course, with RDS you can't login `AS SYSDBA`, so the only option is to add a bit of storage in order
to be able to sign in, and then run

```
EXEC rdsadmin.manage_tracefiles.purge_tracefiles(60);
``` 

to remove all trace files older than 60 minutes.

# Creating the Wallet

But back to the issue. It turns out the problem was that the certificate chain for the TLS certs used by the
remote server, were non-standard and Oracle didn't trust them (my theory is that Oracle 19c tightened up
restrictions a bit where 11g was a bit more forgiving)

Now - how would I add the missing certificates.  If this was Java, I'd just add them to the `cacerts` trust store.
The Oracle DB has wallets but I found that the documentation online was a little bit sparse.

Eventually I found that I would have to create a wallet and add the trusted cert.

Given the certificate:

```
-----BEGIN CERTIFICATE-----
MIIDrzCCApegAwIBAgIQC[...]
-----END CERTIFICATE-----
```

I could create a wallet like so:

```
$ orapki wallet create -wallet remote_wallet -pwd password01 -auto_login
Oracle PKI Tool : Version 12.2.1.3.0
Copyright (c) 2004, 2017, Oracle and/or its affiliates. All rights reserved.

Operation is successfully completed.

$ orapki wallet add -wallet remote_wallet -cert my_ca.crt -pwd password01 -trusted_cert
Oracle PKI Tool : Version 12.2.1.3.0
Copyright (c) 2004, 2017, Oracle and/or its affiliates. All rights reserved.

Operation is successfully completed.

$ ls -la remote_wallet/
total 16
drwx------ 2 gerald.benischke gerald.benischke 4096 Nov 17 23:44 .
drwx------ 4 gerald.benischke gerald.benischke 4096 Nov 18 00:21 ..
-rw------- 1 gerald.benischke gerald.benischke 1999 Nov 18 00:21 cwallet.sso
-rw------- 1 gerald.benischke gerald.benischke    0 Nov 17 23:44 cwallet.sso.lck
-rw------- 1 gerald.benischke gerald.benischke 1499 Nov 18 00:21 ewallet.p12
-rw------- 1 gerald.benischke gerald.benischke    0 Nov 17 23:44 ewallet.p12.lck

$ orapki wallet display -wallet onprem_wallet/
Oracle PKI Tool : Version 12.2.1.3.0
Copyright (c) 2004, 2017, Oracle and/or its affiliates. All rights reserved.

Requested Certificates: 
User Certificates:
Trusted Certificates: 
Subject:        CN=My Root CA,OU=XXX,O=Org,C=GB
```

Again, RDS being a managed service, it wasn't possible to just add the files to a directory on my RDS instance, so
I would use RDS commands to create the files.

First, create a wallet directory (see more [in the AWS docs](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.Oracle.CommonDBATasks.Misc.html):

```
exec rdsadmin.rdsadmin_util.create_directory(p_directory_name => 'REMOTE_WALLET');
```

Then I can find out the "real" directory path:

```
SELECT DIRECTORY_PATH 
  FROM DBA_DIRECTORIES 
 WHERE DIRECTORY_NAME='REMOTE_WALLET';
        
DIRECTORY_PATH
----------------------------------------
/rdsdbdata/userdirs/02
```

Now, all that's left to do is to add the wallet files (`cwallet.sso` and `ewallet.p12` above).  Again, this wasn't 
quite as straightforward as I had hoped:

First, I turned the wallet files into Base64:

```
$ base64 -w0 remote_wallet/cwallet.sso
```

And then I would copy/paste that base64 encoded into the following SQL*Plus snippet to create the file:

```
declare
  v_fid utl_file.file_type;
begin
  v_fid := utl_file.fopen('REMOTE_WALLET', 'cwallet.sso', 'wb', 10000);
  utl_file.put_raw(v_fid, utl_encode.base64_decode(utl_raw.cast_to_raw('ofhONgAAAAYAAAAhBh[...]')));
  utl_file.fclose(v_fid);
end;
```

Rinse and repeat for the others, and then you will have a custom Oracle wallet on the RDS instance:

```
SQL> SELECT * FROM TABLE(rdsadmin.rds_file_util.listdir('REMOTE_WALLET'))

FILENAME             TYPE        FILESIZE   MTIME
-------------------- ---------- ---------- ---------
02/                  directory        4096 18-NOV-20
cwallet.sso          file             1999 18-NOV-20
ewallet.p12          file             1499 18-NOV-20
[...]
```

# Using the Wallet

Now that the wallet is there, it can be used by modifying the link as such:

```
(DESCRIPTION=(ADDRESS=(PROTOCOL=TCPS)(HOST=other-oracle-database)(PORT=2484))
  (CONNECT_DATA=(SERVER=DEDICATED)(SERVICE_NAME=serviceName))
  (SECURITY=(MY_WALLET_DIRECTORY=/rdsdbdata/userdirs/02)
    (SSL_SERVER_CERT_DN="c=GB,st=Somewhere,l=Loc,o=Org,ou=XXX,cn=other-oracle-database.com")))
``` 

I hope this helps you avoid a few pitfalls of AWS RDS Oracle DB Links. 