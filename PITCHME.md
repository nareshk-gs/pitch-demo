#HSLIDE
## WIREMOCK 
### More than just a Stub

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

@[2] (Order in which WireMock picks up mappings. 1 - Highest)
@[3-6] (Request to be matched against)
@[7-15] (Response)
@[8] (HTTP Response Status Code)
@[9-11] (Response JSON Body)
@[12-14] (Response Headers)

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

@[3-11] (Request pattern match)
@[12-26] (Response)
@[13] (Response HTTP Status Code)
@[14-24] (Response JSON Body)

#HSLIDE

## Proxying


#VSLIDE

## Proxying - example

```
{
  "priority": 2000,
  "request" : {
    "urlPattern" : "/click-list-master-order/(.*)"
  },
  "response" : {
    "proxyBaseUrl" : "https://dc1-stage.kroger.com"
  }
}
```

@[7] (base URL for Master Order)

#HSLIDE

## Timeouts


#VSLIDE

## Timeout - example

```
{
  "priority": 1,
  "request" : {
    "urlPattern" : "/click-list-master-order/draft-order/(.*)/getByProfileId",
    "method" : "GET",
    "headers": {
      "X-Correlation-Id": {
        "equalTo": "do_timeout"
      }
    }
  },
  "response" : {
    "fixedDelayMilliseconds": 5000,
    "transformers": ["response-template"],
    "status" : 500,
    "jsonBody" : {
        "transactionId": "bdec63f5-6c34-4553-a735-b02ce18d99dc",
        "correlationId": "11725530685341029",
        "httpStatus": 500
    }
  }
}
```

@[3-11] (base URL for Master Order)
@[13] (Delay 5 secs before sending response)