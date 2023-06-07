---
title: "Functional programming in MongoDB"
date: 2023-06-06T21:32:00Z
tags: ["functional", "mongo"]
featured_image: "/images/functional_mongo_aggregation_title.jpg"
---

In this post I am looking at using the mongo aggregation pipeline in non-trivial ways. In fact, once I had done
what I wanted to do I looked back at the result and discovered that a bit of an idea of functional programming
helped me achieve what I wanted to do.

But back to the beginning: I was looking at deployment events which we'd captured in a mongo collection:

```mongodb-json
{
  "environment" : "production",	  
  "serviceName" : "platform-status-frontend",	  
  "version" : "0.26.0", 
  "timestamp" : ISODate("2020-01-22T11:29:54Z")
}
```

The question came up, how could we tell whether there had been problems with a deployment. We were trying to look
at ways of finding [DORA metrics](https://cloud.google.com/blog/products/devops-sre/using-the-four-keys-to-measure-your-devops-performance). 
So as we are using semantic versioning, we thought that a deployment problem would
usually occur when one of two things would happen:

- A version is rolled back
- A hotfix/patch version is deployed

The thought was that if I had the following sequence of deployments

| Version | Comment                                       |
| ------- |-----------------------------------------------|
| 0.26.0  | Initial version deployed                      | 
| 0.27.0  | New version deployed, extra features - breaks |
| 0.26.0  | Version rolled back                           |
| 0.28.0  | Fixed forward                                 |
| 0.28.1  | Bug fix introduced with patch                 |
| 0.29.0  | New version                                   |

In the above example, the second deployment of 0.26.0 and the deployment of the patch version would indicate that
something wasn't quite right, and it would be quite cool to use the deployment events to automatically calculate 
the failures.

# First Idea

My first thought was, how about I just get all the data out of the deployment events collection and then write 
a script - possibly using [jq](https://jqlang.github.io/jq/) - to find the relevant events. So, I'd have
to:

- group the events by service
- then iterate across the events to see whether any versions went backwards ...

And that's where I got stuck. First of all, I had about a million deployment events to look at, so there'd be a little
bit of effort extracting that from the DB. And I wasn't entirely
sure how I'd look at writing a loop - I would have to keep state of the previous version, compare it with the 
current version, move that along and it got quite complex quickly. Too complex, in fact.

# Do it in Mongo?

Then a thought struck me, wasn't it a bit wasteful to extract JSON from mongo to process the JSON, when Mongo works on
... JSON (well, it's [BSON](https://www.mongodb.com/json-and-bson) but near enough). I had used the [Mongo
aggregation pipeline](https://www.mongodb.com/docs/manual/core/aggregation-pipeline/) before, but usually just to
mimick GROUP BY queries.

But that's what I decided to try. So let's start with the easy part, grouping:

```mongodb-json
db.releases.aggregate([
  {"$match":{"environment":"production"}},
  {"$group":{
    "_id":"$serviceName",
    "deploys":{"$push":{"ver":"$version","ti":"$timestamp"}}
  }}
])
```

The above would do the following things:

- The `$match` stage ensure that we're only looking at `production` events
- The `$group` stage would group the records by service name
- The `$push` command would aggregate the `version` and `timestamp` into an array of deployments

so this would return

```mongodb-json
{
  "_id": "platform-status-frontend",
  "deploys": [
    {
      "ver": "0.28.0",
      "ti": ISODate("2021-04-08T12:47:13Z")
    },
    {
      "ver": "0.34.0",
      "ti": ISODate("2021-04-27T10:08:41Z")
    }
  ]
}
```

![Mongo Lambda logo mash](/images/functional_mongo_aggregation_logo_mash.png)

# No reduction in complexity?

At this stage, you might ask how does this help me? I just aggregated the deployment events, but how would
I filter out all the deployments that weren't rollbacks or hotfixes. I was back at the stage were I was thinking
about iterating across versions. I was considering whether I could use [map-reduce](https://www.mongodb.com/docs/manual/core/map-reduce/) 
to keep state. 

Then it hit me, I was going about this the wrong way. My adventures with Haskell during [Advent of Code]({{< ref "advent_of_code_2021_day_4.md" >}}) 
taught me about `zip`. And wouldn't you know, [zip also exists](https://www.mongodb.com/docs/manual/reference/operator/aggregation/zip/)
in the mongo aggregation pipeline, so if I could `$zip` the `deploys` array with itself, just offset by one, I could
have a list of a version and it's predecessor.

After a bit of tinkering (mainly to line up the opening and closing braces), I got to the following:

```mongodb-json
db.releases.aggregate([
  {"$match":{"environment":"production"}},
  {"$group":{
    "_id":"$serviceName",
    "deploys":{"$push":{"ver":"$version","ti":"$timestamp"}}
  }},
  {"$match":{"$expr":{"$gt":[{"$size":"$deploys"},1]}}},
  {"$project":{
    "deploys":1,
    "deploys1":{
      "$slice":[
        "$deploys", 
        1, 
        {"$subtract": [{"$size": "$deploys"}, 1]}
      ]
    }
  }},
  {"$project":{
    "zipped":{
      "$zip":{
        "inputs":["$deploys","$deploys1"]
      }
    }
  }}
])
```

Let's unpack what's happening here:

- The first two stages are the same as before
- Then the `$match` stage ensure that we do not consider services with only a single version (otherwise we'll fall over
  later on)
- Then the `$project` stage creates an additional `deploys1` field which contains every entry from the `deploys` array
  offset by 1, i.e. without the first element
- Finally, the last `$project` stage uses `$zip` to match the two arrays together.

This leaves us with:

```mongodb-json
{ 
  "_id" : "platform-status-frontend", 
  "zipped" : [ 
    [ { "ver" : "0.28.0", "ti" : ISODate("2021-04-08T12:47:13Z") }, 
      { "ver" : "0.34.0", "ti" : ISODate("2021-04-27T10:08:41Z") } ]
  ]
}
```

So now we have an array of tuples (really an array with two elements, but tuple is more feels appropriate). Now if we 
were to look at this functionally, we would filter out any elements where the version in the first element is greater
than in the second element.

# Semver

Of course, it is not as easy as it looks. Because the version is not a number but a string, and using string comparison
here would not give the right result. 

For instance comparing the strings `"0.3.0"` and `"0.11.0"` does not give the correct answer when doing string
comparison. So I changed the first group stage to be:

```mongodb-json
{"$group": {
  "_id":"$serviceName",
  "deploys":{
    "$push":{
      "ver":{
        "$sum":{
          "$map":{
            "input":{
              "$zip":{
                "inputs":[
                  {"$map":{
                    "input":{
                      "$split":["$version","."]},
                      "as":"e",
                      "in":{"$toInt":"$$e"}
                    }
                  },
                  [100000000,10000,1]
                ]
              }
            },
            "as":"j",
            "in":{
              "$multiply":[
                {"$arrayElemAt":["$$j",0]},
                {"$arrayElemAt":["$$j",1]}
              ]
            }
          }
        }
      },
      "ti":"$timestamp",
      "version":"$version"
    }
  }
}}
```

That's a lot to unpack, I'll be going from the inside out. First of all, this little snippet:

```mongodb-json
{"$map":{
  "input":{
    "$split":["$version","."]
  },
  "as":"e",
  "in":{"$toInt":"$$e"}
}}
```

The above takes a string (`$version`) splits it by `.` and then turns the components into numbers (`$toInt`).

For example: `"0.5.1"` would turn into `[0, 5, 1]`.

```mongodb-json
{"$zip":{
  "inputs":[
    {"$map":...},
    [100000000,10000,1]
  ]
}}
```

This next snippet zips together the result of the `$map` with the array `[100000000,10000,1]`, this would give us:

```mongodb-json
[[0,100000000],[5,10000],[1,1]]
```

The next steps multiplies these together:

```mongodb-json
{"$map":{
  "input":{
    "$zip":...
  },
  "as":"j",
  "in":{
    "$multiply":[
      {"$arrayElemAt":["$$j",0]},
      {"$arrayElemAt":["$$j",1]}
    ]
  }
}}
```

So this would take `"0.5.1"` and turn it into `[0, 50000, 1]`. Now we just need to add this together:

```mongodb-json
{"$sum":{
  ...
}}
```

Which would turn the not-easily-comparable `"0.5.1"` into the number `50001`.

The final part is to add a [$filter](https://www.mongodb.com/docs/manual/reference/operator/aggregation/filter/) stage to
remove any elements where the first version is greater than the second version (i.e. a rollback).

That's the whole aggregation pipeline:

```mongodb-json
db.releases.aggregate([
  {"$match":{"environment":"production"}},
  {"$group":{
    "_id":"$serviceName",
    "deploys":{
      "$push":{
        "ver":{
          "$sum":{
            "$map":{
              "input":{
                "$zip":{
                  "inputs":[
                    {"$map":{
                      "input":{
                        "$split":["$version","."]
                      },
                      "as":"e",
                      "in":{"$toInt":"$$e"}
                    }},
                    [100000000,10000,-1]
                  ]
                }
              },
              "as":"j",
              "in":{
                "$multiply":[
                  {"$arrayElemAt":["$$j",0]}, 
                  {"$arrayElemAt":["$$j",1]}
                ]
              }
            }
          }
        },
        "ti":"$timestamp",
        "version":"$version"
      }
    }
  }},
  {"$match":{
    "$expr":{
      "$gt":[
        {"$size":"$deploys"},
        1
      ]
    }
  }},
  {"$project":{
    "deploys":1,
    "deploys1":{
      "$slice":[
        "$deploys", 
        1, 
        {
          "$subtract": [
            {"$size": "$deploys"}, 
            1
          ]
        }
      ]
    }
  }},
  {"$project":{
    "rollbacks":{
      "$filter":{
        "input":{
          "$zip":{
            "inputs":["$deploys","$deploys1"]
          }
        },
        "as":"v",
        "cond":{
          "$let":{
            "vars":{
              "a":{"$arrayElemAt":["$$v",0]},
              "b":{"$arrayElemAt":["$$v",1]}
            },
            "in":{
              "$gt":["$$a.ver","$$b.ver"]
            }
          }
        }
      }
    }
  }},
  {"$match":{
    "$expr":{
      "$gt":[{"$size":"$rollbacks"},0]
    }
  }}
],{"allowDiskUse":true})
```

# Phew!

The eagle-eyed will have spotted that I changed `[100000000,10000,1]` to `[100000000,10000,-1]` - this is so that 
hotfixes are counted in the same way as rollbacks - so if "0.5.1" was preceeded by "0.5.0" it would be counted.

The other addition was the `{"allowDiskUse":true}` option on the aggregation pipeline as the amount of data processed
did not fit into memory. This is also a reason why this aggregation pipeline is probably only suited for batch jobs
or one-off reporting.

# Conclusion

Is the above quite complex? Yes. Did it take me quite a while to figure out? Definitely. But is it worth refreshing
your basic functional knowledge (map, filter, etc) for some scenarios which can be done in mongo rather than requiring
shifting a lot of data between database and application nodes? Absolutely. Does this mean we should use aggregation
pipelines everywhere? Don't be silly!

