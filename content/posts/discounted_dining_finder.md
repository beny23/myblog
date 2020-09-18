---
title: "Discounted Dining Finder"
date: 2020-07-29T23:55:11+01:00
category: ["frontend"]
---

![Discounted Dining Finder](/images/discounted_dining_finder_title.png)

This post describes how I developed the [Discount Dining Finder](https://eat-out-to-help-out.co/) a lookup map tool for the Eat Out to Help Out scheme in my spare time.  I currently
work with [Equal Experts](https://www.equalexperts.com/) and HMRC. The aim of this writing is to provide an insight
into how problems of scaling services can be solved by having no servers and not using "serverless services" either.

# Aperitif

A really nice side effect in [working in a high functioning environment]({{< ref "making_software_quickly.md" >}})
is that sometimes you're involved in bouncing ideas off each other. The delivery teams at HMRC were working on releasing
yet another service to the public in less time than it takes you to say "Agile". This scheme was called 
[Eat Out to Help Out](https://www.gov.uk/guidance/get-a-discount-with-the-eat-out-to-help-out-scheme) - internally known
as Discounted Dining because the powers that be did not want to risk leaking the name prior to the Chancellor's announcement.

The scheme would consist of different journeys:

- registering a restaurant, 
- search for registered establishments for the public and 
- claims for payment.    

Out of these three, the biggest unknown of in terms of expected volume was the "search journey" to be used by the 
general public. In this journey, a user would enter a postcode, and registered establishments inside an X mile radius
would be displayed. There was a big number of unknowns in terms of how much traffic was to be expected on 
[the HMRC service](https://www.tax.service.gov.uk/eat-out-to-help-out/find-a-restaurant).

- Would there be a peak at lunchtime?
- What if Martin Lewis goes on TV, recommends visiting the site and two minutes later 10% of the country want to find
information about their local eateries?
- Could it impact other HMRC services (the "tax platform" hosts a multitude of services)

Now, the "tax platform" is a very scalable and robust platform and I am not for one minute suggesting that there was 
going to be a problem using microservices and geo-location in Mongo at scale, but one of the ideas that I floated 
centred around the fact that the information is fairly static. Sure enough, "Eat Out" businesses 
register their premises with HMRC, but once registered, the bulk of information is not changing.  Postcodes and distances
between them are not that changeable. So that's when I wondered, whether this could be delivered in a static site.

# Starter

I went away and found that [freemaptools](https://www.freemaptools.com/download-uk-postcode-lat-lng.htm) provides me with
a list of UK postcodes and their associated latitude/longitude. In that file, there are 1,767,875 postcodes. Searching 
almost 2 million records sounds like the job for a server and a database, doesn't it? Erm, no.

Looking at the postcode file

```shell script
$ head -10 ukpostcodes.csv 
id,postcode,latitude,longitude
1,AB10 1XG,57.144165160000000,-2.114847768000000
2,AB10 6RN,57.137879760000000,-2.121486688000000
3,AB10 7JB,57.124273770000000,-2.127189644000000
4,AB11 5QN,57.142701090000000,-2.093295000000000
```

Instead of searching a single `ukpostcodes.csv` (95 MB) everytime, I decided to "shard" or "partition" my CSV file into
smaller files:

```shell script
./A/B/AB10.csv
./A/B/AB11.csv
./A/L/AL1.csv
./A/L/AL10.csv
./A/L/AL2.csv
./B/1/B1.csv
./B/1/B10.csv
./B/2/B2.csv
./B/2/B20.csv
```

Each file is split into directories by their first letters.  So if I want to find out about postcode `AB12 4TS`, I'd 
split up the outcode (`AB12`) into `/A/B/AB12.csv`. That file would only have 799 entries, searching them manually
is much more palatable.

So I've got my main page and the user would enter their postcode

![Main page](/images/discounted_dining_finder_title_screen.png)

And I can search for the postcodes simply by using a bit of Javascript inside the user's browser.

```javascript
d3.csv("outcode/" + outcode[0] + "/" + outcode[1] + "/" + outcode + ".csv")
    .then(function(postcodes) {
        result = postcodes.find(d => normalisePostcode(d.postcode) === postcode);
        if (result) {
            d3.select("#status").text("");
            mapid.panTo(new L.LatLng(result.lat, result.lon));
        } else {
            d3.select("#status").text("Postcode not found")
        }
    })
```

[D3](https://d3js.org) is a great library for visualisations, but I also found it very useful for reading and processing
CSVs in Javascript, and the files can be served up by a static web server.

Great! But how do I get my directory structure. I did not fancy manually copy/pasting the file. You think, surely now
it's time to unleash some NoSQL database or at least some Python.  But no, I decided to keep it simple and use a combination
of shell scripts and AWK:

```shell script
awk -F, -f split_outcodes.awk target/ukpostcodes.csv
```

With the `split_outcodes.awk` script doing the hard work of creating new files in the correct directory.

```shell script
$1 != "id" && $3 < 99.9 {
  prev=file;
  split($2, f, " ");
  outcode = f[1]
  outcode1 = substr(outcode, 1, 1)
  outcode2 = substr(outcode, 2, 1)
  file="target/outcode/" outcode1 "/" outcode2 "/" outcode ".csv";
  if (prev!=file) close(prev);
  if (headers[file] != "done") {
    print "id,postcode,lat,lon" >> file;
    headers[file] = "done"
  }
  print $0 >> file;
}
```

This resulted in 2980 files - the biggest of those was 145K which corresponded to 2701 postcodes. Now that's much better
to search than 1.7 million!

# Soup

I didn't mention the [Discounted Dining Finder](https://eat-out-to-help-out.co/) had a map.  A quick diversion on setting that up!

I used [LeafletJS](https://leafletjs.com) - an open source map. Here's how:

```javascript
mapid = L.map('mapid');
L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
}).addTo(mapid);
markerLayer = L.layerGroup().addTo(mapid)
```

And I had a map!

![A Map](/images/discounted_dining_finder_map.png)

# Fish

That map didn't have anything on yet! I am able to convert a postcode into lat/lon though. The next step was to 
lookup the restaurants. I decided to keep running with the idea of doing all my computation on the user's browser 
(desktop or phone).

First of all, I found that the UK postcodes were covering an area of:

```shell script
$ cut -f3 -d, ukpostcodes.csv | awk -F, 'BEGIN { max = -999; min = +999; } /[0-9.-]+/ { if ($1 > max) max = $1; if ($1 < min) min = $1; } END { print min, max; }'
49.181941000000000 60.800793046799900
$ cut -f4 -d, ukpostcodes.csv | awk -F, 'BEGIN { max = -999; min = +999; } /[0-9.-]+/ { if ($1 > max) max = $1; if ($1 < min) min = $1; } END { print min, max; }'
-8.163139000000000 1.760443184261870
```

I calculated that the rectangle (60.80 N/-8.16 W) - (49.18 N/1.76 E) covered about 400 miles from 
West to East and 800 miles North to South.  My aim was to provide a lookup that can find all restaurants in a 5 mile 
radius, so I split my search area up into tiles of roughly 5x5 miles. Here's my translation function:

```javascript
var x = parseInt((+result.lat - 49.0) / (12.0 / 160.0))
var y = parseInt((+result.lon + 9) / (11.0 / 80.0))
```

That would give me a coordinate set for a tile.  So the Buckingham Palace (51.5 N/-0.14 W) would be at coordinates (33/64).
Based on that, I can build another set of files:

```shell script
target/pubgrid
target/pubgrid/0
target/pubgrid/1
target/pubgrid/10
target/pubgrid/100
target/pubgrid/100/100-19.csv
target/pubgrid/100/100-20.csv
target/pubgrid/100/100-21.csv
```

Whereby all the eateries that are in coordinates (33/64) would be in the file `pubgrid/33/33-64.csv`. That file would look 
like this:

```shell script
name,postcode,lat,lon
blue racer and frilled lizard,BR1 1AB,51.406270892812800,0.015176762143898
saltwater crocodile and blue racer,BR1 1LU,51.401706890000000,0.017463449000000
king cobra and Schneider python,BR1 1PQ,51.406421920000000,0.012595296000000
``` 

The javascript can then find the suitable restaurants like so:

```javascript
d3.csv("pubgrid/" + x + "/" + x + "-" + y + ".csv")
    .then(function(pubs) {
        let inRange = pubs
            .map(a => ({ ...a, distance: distance(result, a)}))
            .filter(a => a.distance < (5 * 1609.34))
            .sort((a, b) => a.distance - b.distance)
            .slice(0, 250)

        d3.select("#results").selectAll("tr")
            .data(inRange)
            .join("tr")
            .selectAll("td")
            .data(d => [ d.name, d.postcode, (d.distance / 1609.34).toFixed(2) + " miles away" ])
            .join("td")
            .text(d => d)

        markerLayer.clearLayers();
        inRange.forEach(d => L.marker([d.lat, d.lon], { "title": d.name }).addTo(markerLayer))
    })
```

The above code does a few things:

1. It calculates the distance between the selected lat/lon and the lat/lon for the restaurant
1. It filters out anything that is further away than 5 miles
1. It sorts by distance, so that the closest are first
1. It takes up to 250 results
1. Dynamically create a table that shows the results (IMHO, this is very neat using D3)
1. Clear and recreate all the markers on the map.

The end result looks a little like this:

![A Map with Markers and List](/images/discounted_dining_finder_list.png)

# Meat

Now, the next tricky bit is to ensure, that my coordinate grid system, that simplifies (lat/lon) into coordinates contain
all the relevant information about the closest eating establishments.  As each tile is designed to be about 5x5 miles,
in order to ensure that we find every restaurant that is 5 miles away from each tile, each restaurant goes into the tile it
is in, as well as the surrounding tiles, this is done using trusty AWK:

```shell script
function print_to_file(file) {
  if (headers[file] != "done") {
    print "name,postcode,lat,lon" >> file;
    headers[file] = "done"
  }
  print $0 >> file;
  close(file);
}

{
  x = int(($3 - 49.0) / (12.0 / 160.0))
  y = int(($4 + 9) / (11.0 / 80.0))

  file_tl="target/pubgrid/" (x-1) "/" (x-1) "-" (y-1) ".csv";
  file_tm="target/pubgrid/" x "/" x "-" (y-1) ".csv";
  file_tr="target/pubgrid/" (x+1) "/" (x+1) "-" (y-1) ".csv";
  file_ml="target/pubgrid/" (x-1) "/" (x-1) "-" y ".csv";
  file_mm="target/pubgrid/" x "/" x "-" y ".csv";
  file_mr="target/pubgrid/" (x+1) "/" (x+1) "-" y ".csv";
  file_bl="target/pubgrid/" (x-1) "/" (x-1) "-" (y+1) ".csv";
  file_bm="target/pubgrid/" x "/" x "-" (y+1) ".csv";
  file_br="target/pubgrid/" (x+1) "/" (x+1) "-" (y+1) ".csv";

  print_to_file(file_tl);
  print_to_file(file_tm);
  print_to_file(file_tr);
  print_to_file(file_ml);
  print_to_file(file_mm);
  print_to_file(file_mr);
  print_to_file(file_bl);
  print_to_file(file_bm);
  print_to_file(file_br);
}
```

But wait a minute, that presupposes that I have a list of "pubs" and their coordinates.  That's not the case, all we've 
got is the establishment name and their postcode. Thankfully there's a shell command that I can use to "join" my existing
postcode file and a file of establishments and their postcodes:

```shell script
join -t , -1 2 -2 2 -o 1.1,0,2.3,2.4 \
   <(sort -k 2 -t , target/pub_postcodes.csv) \
   <(sort -k 2 -t , target/ukpostcodes.csv) > target/named_pubs.csv
```

The above does the following

- sort both the `pub_postcode.csv` (containing name and postcode) and
- sort the `ukpostcodes.csv` (containing the postcode and lat/lon) and
- "joins" the two files - creating one whereby the lines are joined by the postcode.

# Palate Cleanser

You will have noticed above that my examples aren't giving real pub or restaurant names.  While HMRC had not yet published
the list of registered restaurants, I used by shell scripting knowlegde (and a lot of google) to create a fairly
neat way of generating random pub/restaurant names.

I took a [list of animal names](https://github.com/beny23/static-distance/blob/master/animal_names.txt) and randomly 
combined them with "and", the aim being to get the "Fox and Badger" and endless variations.

Here's the shell script to allow you to do this:

```shell script
shuf -n 100000 target/ukpostcodes.csv | cut -f2 -d, > target/pub_postcodes.txt

shuf -rn 100000 animal_names.txt > target/1.txt
shuf -rn 100000 animal_names.txt > target/2.txt
yes "and" 2>/dev/null | head -100000 > target/and.txt
paste -d " " target/1.txt target/and.txt target/2.txt > target/pubnames.txt

paste -d "," target/pubnames.txt target/pub_postcodes.txt > target/pub_postcodes.csv
``` 

This creates

- 100000 random postcodes
- 100000 random animal names
- another 100000 random animal names (in a different order)
- 100000 "and"s
- and combines them all, resulting in my randomly generated pub names:

```shell script
$ head pub_postcodes.csv 
leguaan and bushmaster,B79 7SP
anaconda and Moluccan boobook,CM20 2GN
flying lizard and hoop snake,NW4 3LY
Towhee and agamid,LL11 6NN
Puffleg and Gila monster,OX12 0FE
mamba and Chipmunk,UB6 7AH
Eagle and Marsh harrier,FK1 5LE
Jay and chameleon,KA19 7NW
B and Maya,L5 7UB
ringhals and Diving bird,W9 2EH
```

# Dessert

All of the above is very good, but I've still not hosted my tool anywhere, and I don't want to use my own servers.
Thankfully, github.com provides [GitHub Pages](https://pages.github.com) and [GitHub Actions](https://docs.github.com/en/actions)
which can be combined to provide a build pipeline and a hosting solution!

# Cheese

Thanks for reading, I hope you found the [Discounted Dining Finder](https://eat-out-to-help-out.co/) and the above tale interesting.
The source code is available on [github.com/beny23/static-distance/](https://github.com/beny23/static-distance/) and
released using the Apache-2.0 open source licence.