#HSLIDE
WIREMOCK -More than just a Stub

![Press Down Key](assets/wiremock-logo.png)
#HSLIDE

## What is WIRE MOCK?

### Advanced HTTP API simulator

#VSLIDE

## How WIRE MOCK?

- Not Gradle     |
- Not Spring     |
- Not javascript |
- Not java (?)   |
- But a jar and a bunch of JSONs |

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

#HSLIDE

## Simulating Faults / Edge Cases


#VSLIDE

## Simulating Faults - example

```
{
  "priority": 1,
  "request" : {
    "urlPattern" : "/click-list-master-order/draft-order/(.*)/getByProfileId",
    "method" : "GET",
    "headers": {
      "X-Correlation-Id": {
        "equalTo": "getdraftorder_byprofile_404"
      }
    }
  },
  "response" : {
    "status" : 404,
    "jsonBody" : {
      "transactionId": "80e2a2cb-8eb0-4e55-9286-e74945442cf8",
      "correlationId": "2e303fbe-f307-4e6d-b213-9801fee2cc0e",
      "httpStatus": 404,
      "appErrorCode": "DraftOrderNotFound",
      "detail": {
        "errors": [
          "Draft Order not found"
        ],
        "context": {}
      }
    }
  }
}
```

@[3-11]
@[12-26]
@[13]
@[14-24]
