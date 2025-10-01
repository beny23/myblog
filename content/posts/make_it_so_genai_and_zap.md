---
title: "Make it so: GenAI, OpenAPI and ZAP"
date: 2025-10-01
tags: ["appsec","genai","openapi"]
featured_image: "/images/make_it_so_genai_and_zap_title.png"
---

Have you ever wondered how hard it is to make an AI talk to an API? Wouldn't it 
be great if I could talk to a machine like Captain Picard does to his computer?

![Tea. Early Grey. Hot](/images/make_it_so_genai_and_zap_picard.png)

"Tea. Earl Grey. Hot!" would have to sent to the replicator subsystem with the correct instructions. But how would that
actually work? I suppose, the Starship Enterprise-D made its first appearance on the airwaves in 1987. So at the time
I guess we could expect a replicator to be programmed in C. Though to be fair as a vessel with military capacity, 
probably Ada. And probably a monolith. So we probably would have to expect the replicator software to have a voice
recognition subsystem.

But we're not in the 80s anymore, we've got modern software. We've got APIs and we've got GenAI. So what would a modern
replicator architecture look like? Like on the Enterprise G (first aired 2023).

![USS Enterprise G](/images/make_it_so_genai_and_zap_enterprise_g.png)

First of all, I think we'd probably write it in Rust or Go, or maybe Typescript. There's bound to be a kubernetes 
cluster somewhere. And there's going to be APIs!

![APIs](/images/make_it_so_genai_and_zap_apis.png)

We'd probably decompose the voice subsystem into its own. Then we'd have a something that translates the 
voice command into actual commands. And these days that's obviously going to be an LLM.

# Make it So

As I've got an AppSec hat, I what could I use to demonstrate this idea? Why not [ZAP](https://www.zaproxy.org). 
After all, the ZAP UI feels a little bit on the dated side:

![ZAP UI](/images/make_it_so_genai_and_zap_ui.png)

Luckily ZAP has got an API. The default interface is... erm, how do I say this without throwing shade... let's just leave 
this here:

![API UI](/images/make_it_so_genai_and_zap_api_ui.png)

# Just use AI

In order for LLMs to talk to tools, one way of doing that is using the [Model Context Protocol (MCP)](https://modelcontextprotocol.io/).
Here's a crude drawing of how it works:

![MCP](/images/make_it_so_genai_and_zap_mcp.png)

The LLM orchestrator - also known as the MCP client - is responsible for telling the LLM about the available tools
and when the model lets the orchestrator know to call the tools, it will invoke them based on the Model
Context Protocol.

That's the theory anyway. So I searched google for a ZAP MCP. And sure enough there was one. But did I want to use 
that?

![Sure grandma](/images/make_it_so_genai_and_zap_sure_grandma.png)

I know some of the young kids these days will enable  [dangerous mode](https://github.com/anthropics/claude-code/issues/2564)
or [yolo mode](https://forum.cursor.com/t/yolo-mode-is-amazing/36262), but I think random MCP servers are quite a
risk at the moment. There's this rush toward AI and as per usual security is an afterthought. 

My go-to rules for using MCP servers at the moment are:

- is it an official one?
- have you built it yourself?
- can you isolate it (e.g. run it in a docker container without access to your machine)

I'm not feeling comfortable downloading Dave from Kentucky's MCP server for accessing my emails. Especially
as email access was just [trivially exploited](https://www.koi.security/blog/postmark-mcp-npm-malicious-backdoor-email-theft).

Luckily, there's quite a mature ecosystem around APIs. With [OpenAPI Specifications](https://www.openapis.org/blog/2017/07/26/the-oai-announces-the-openapi-specification-3-0-0)
there's a powerful way of expressing APIs and lots of tooling to build.

# How hard can it be?

ZAP has an [OpenAPI spec](https://raw.githubusercontent.com/zaproxy/zap-api-docs/main/openapi.yaml), so it should
be quite easy to create an MCP. I mean there's so many MCPs out there, even a GenAI code generator should be able
to do this.

But then I thought...

![Vibecode Drake](/images/make_it_so_genai_and_zap_vibecode_drake.png)

So I did. Some thoughts on vibecoding:

## Complexity

The machine wants everything to be complex, it starts out with automatic retries, circuit breakers. This 
is the wrong way round! We should start simple and then make it more complex later on. When I start, I don't
even know whether retries will be needed!

## Libraries, what libraries?

The machine has blinkers on. When I asked it to code an MCP, it went straight for implementing the protocol from
scratch. I think this is one of the fundamental flaws with vibecoding at the moment. Sure, it might work, but 
it won't be easy to maintain.

## You're absolutely right!

So when I suggested to make our lives easier by using [FastMCP's OpenAPI integration](https://gofastmcp.com/integrations/openapi)
it was all for it. I detest the sycophantic way it keeps telling me that I asked a great question, or made a good
point.

Of course, even after I restarted the whole process from scratch and added "please use that library" to the prompt,
it then proceeded to implement automatic retries again. Argh! We don't need that!

You're absolutely right...

## Is it faster?

If you want to check out my OpenAPI MCP server, it's available here: https://github.com/beny23/openapi-mcp-server. 
You can see [how I've prompted](https://github.com/beny23/openapi-mcp-server/tree/main/prompts) the machine to 
generate prompts for it to then follow in an effort to keep it from losing track. Which was moderately successful.

All in all I found the experience frustrating because I can't shake the feeling that I would have been a lot 
quicker if I had just read the FastMCP documentation myself and implemented it. There certainly would have been some
tests!!!

# Let's Run it

So to put it all together, I've chosen to run my MCP servers in docker containers.

## ZAP

```
$ docker run --rm \
    --name zap-proxy \ 
    --network=mcp \
    -p 8081:8081 \
    ghcr.io/zaproxy/zaproxy:stable zap.sh \ 
    -daemon \
    -host 0.0.0.0 \
    -port 8081 \
    -config "api.key=your-secret-api-key-here" \ 
    -config "api.addrs.addr.name=.*" \
    -config "api.addrs.addr.regex=true" \ 
    -Xmx6g
```

Let's unpack what is happening here:

- We're running a ZAP proxy docker image
- `-Xmx6g`: we increase the memory to ensure ZAP doesn't die when doing more than a few scans
- `-p 8081:8081`: we're exposing the API and proxy port
- `--network=mcp`: we're putting it on a docker network - our MCP server will run in a separate container
- `api.address.addr.*`: by default ZAP only listens on the local interface, with these options we're telling 
  ZAP it is ok to listen on all interfaces (but they're restricted by docker anyway)
- `api.key`: sets an API key - this doesn't need to be particularly secure as we're only playing with this locally

## MCP Server

```
$ docker run --rm \
    -p 8000:8000 \
    --network=mcp \
    openapi-mcp-server \ 
    https://raw.githubusercontent.com/zaproxy/zap-api-docs/main/openapi.yaml \ 
    --host 0.0.0.0 \
    --port 8000 \
    --base-url http://zap-proxy:8081 \ 
    --header "X-ZAP-API-Key: your-secret-api-key-here" \ 
    --include-paths '^/JSON/(spider|ajaxSpider|ascan|pscan|alert|reports|context|users|wappalyzer)/(action|view)/(scan|scanAsUser|results|fullResults|allUrls|scans|progress|enableAllScanners|disableAllScanners|scanners|alerts|numberOfAlerts|alertsSummary|alertCountsByRisk|alertsByRisk|generateReport|newContext|includeInContext|excludeFromContext|newUser|usersList|getAuthenticationSession|setAuthenticationCredentials|setUserEnabled|setUserName|listSite|listAll)' \
    --exclude-paths '.*' \
    --server-type http
```

So here we are using my noddy [openapi-mcp-server](https://github.com/beny23/openapi-mcp-server):

- We pass it the URL of the [ZAP OpenAPI spec](https://raw.githubusercontent.com/zaproxy/zap-api-docs/main/openapi.yaml)
- We configure the `X-ZAP-API-Key` header to be passed along - that's the same value as the API key defined above
- We set the `base-url` to the name of the ZAP container
- And we're setting some include paths. This is a way of restricting the number of tools that are exposed.
  ZAP exposes about 900 different endpoints, we don't want to overwhelm the LLM with available tools. For context,
  when I first wrote this Cursor struggled to deal with more than 40 tools. 

# Ready to engage

So now, we've set up ZAP, and are exposing the API as an MCP server. All that remains is to hook it into our 
LLM.

To use it with Claude or Cursor, it's just a manner of adding it in the requisite config file:

```json
{
  "mcpServers": {
    "zap": {
      "type": "http",
      "url": "http://localhost:8000/mcp"
    }
  }
}
```

And then you can use it with Cursor

![Cursor calling ZAP](/images/make_it_so_genai_and_zap_cursor_calling.png)

and it will control ZAP for you

![Cursor calling ZAP tool details](/images/make_it_so_genai_and_zap_cursor_calling_tool.png)

# You said Talking to It

Well, if you literally want to do that, I just use Apple Dictation:

![Apple Dictation](/images/make_it_so_genai_and_zap_apple_dictation.png)

then you can just talk. In this example, I'm talking to Claude:

![Talk to Claude](/images/make_it_so_genai_and_zap_talk_to_claude.png)

Under the hood, this uses the OS feature to translate my voice into text. That text is then sent to the LLM 
augmented with the list of tools it can call. Then the LLM picks which MCP tool to call, which then calls ZAP.

Now we just need a replicator!

# Made It So?

In closing, a few reflections: This is a great gimmick. It certainly plays well in demos. And it's a bit of fun.
But are MCPs the right choice? I'm not so sure. A big problem is the context window. When the tools reply, they
tend to reply in JSON. And then the LLM has to interpret that. Particularly with large responses that then gets 
to be quite tricky for the LLM.

A fair few times, the ZAP tool would output a lot of JSON, and the LLM would struggle with interpreting that.
I also used the same setup to drive Shodan (which has an API) but it kept returning so much detail, that it was
quite difficult to get the right information out of it.

Possibly a way around this is instead of using MCP tooling, have the LLM write python or shell scripts itself. And
then have it execute those scripts. Kind of like I would myself. If there's too much data, I use bash command
lines to interrogate it.

I guess I'll be writing a follow-up post. Hope you enjoyed this.