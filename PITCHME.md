#HSLIDE
WIREMOCK -More than just a Stub

![Press Down Key](assets/wiremock-logo.png)
#HSLIDE

## What is WIRE MOCK?

### Advanced HTTP API simulator

#VSLIDE

## How WIRE MOCK?

#### Not Gradle
#### Not Spring
#### Not javascript
#### Not java (?)

a jar and a bunch of JSONs

#HSLIDE

## Mapping

#### request-response pair

#VSLIDE

## Mapping - example

```
{
  "priority": 1,
  "request" : {
    "urlPattern" : "/APITest/",
    "method" : "GET"
  },
  "response" : {
    "status" : 200,
    "jsonBody" : {
        "status" : "success",
    },
    "headers" : {
      "Content-Type": "application/json"
    }
  }
}
```

@[2]
@[3-6]
@[7-15]
@[8]
@[9-11]
@[12-14]