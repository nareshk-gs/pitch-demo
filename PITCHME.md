#HSLIDE
## WIREMOCK 
### More than just a Stub

![Press Down Key](assets/wiremock-logo.png)
#HSLIDE

## What is WIRE MOCK?

### Advanced HTTP API simulator

#VSLIDE

## Why WIRE MOCK?

- Test Faults |
- Test unreproducible cases |
- 3rd party limitaions |
- Reliable / Faster |
- Intercept messages |

#VSLIDE

## How WIRE MOCK?

```
java -jar wiremock-standalone-2.6.0.jar
```
- Just a jar and a bunch of JSONs |
- That's it! (No Gradle, No Maven, No artifactory, No coding, No problem!) |

#HSLIDE

## Recording

#VSLIDE

## Recording

```
java -jar wiremock-standalone-2.6.0.jar 
        --proxy-all https://ecsb-test.kroger.com  
        --record-mappings
```

![WiremockRecordings](assets/WireMock_Recording.png)

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

## Simulating Test Data


#VSLIDE

#### Response with specific data

```
{
  "priority": 1,
  "request" : {
    "urlPattern" : "/click-list-adjustment/adjustment",
    "method" : "POST",
    "headers": {
      "X-Correlation-Id": {
        "equalTo": "cladj_differentfee"
      }
    }
  },
  "response" : {
    "status" : 200,
    "jsonBody" : {
      "fee": {
        "fulfillment": "CurbSide",
        "banner": "kroger",
        "division": "014",
        "store": "00383",
        "price": 5.00,
        "plu": "223",
        "itemDesc": "Service Fee PLU - CurbSide"
      }
    }
   }
}
```

@[14-24] (Response JSON Body - Sending a different price)

#VSLIDE

## Simulating Faults

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

@[12-26] (Response)
@[13] (Response HTTP Status Code)
@[14-24] (Response JSON Body)

#VSLIDE

#### Response with partial data

```
{
  "priority": 1,
  "request" : {
    "urlPattern" : "/click-list-adjustment/adjustment",
    "method" : "POST",
    "headers": {
      "X-Correlation-Id": {
        "equalTo": "cladj_differentfee"
      }
    }
  },
  "response" : {
    "status" : 200,
    "jsonBody" : {
      "fee": {
        "fulfillment": "CurbSide",
        "price": 5.00
      }
    }
   }
}
```

@[14-19] (Response JSON Body - Sending only mandatory fields)

#VSLIDE

#### Invalid Responses

```
{
  "priority": 1,
  "request" : {
    "urlPattern" : "/click-list-adjustment/adjustment",
    "method" : "POST",
    "headers": {
      "X-Correlation-Id": {
        "equalTo": "cladj_differentfee"
      }
    }
  },
  "response" : {
    "status" : 200,
    "jsonBody" : {
      "fee": {
        "fulfillment": CurbSide,
        "price": 5.00
      }
    }
   }
}
```

@[14-19] (Response JSON Body - Sending a invalid JSON)

#VSLIDE

#### Response Headers

```
{
  "priority": 1,
  "request" : {
    "urlPattern" : "/click-list-adjustment/adjustment",
    "method" : "POST",
    "headers": {
      "X-Correlation-Id": {
        "equalTo": "cladj_differentfee"
      }
    }
  },
  "response" : {
    "status" : 200,
    "jsonBody" : {
      "fee": {
        "fulfillment": "CurbSide",
        "price": 5.00
      }
    },
    "headers" : {
          "Server" : "nginx",
          "Content-Type" : "application/xml;charset=UTF-8",
          "Transfer-Encoding" : "chunked",
          "Connection" : "keep-alive",
          "Keep-Alive" : "timeout=5",
          "Transaction-Id" : "46b175ee-e18b-419c-80f5-0c2543201d4f"
     }
   }
}
```

@[20-27] (Response Headers)
@[22] (Response Header - Send content type as XML instead of JSON)

#VSLIDE

## Hard to Setup Data

- Item with no price |
- Item that always has promo price |
- Item always in Clicklist / not in Clicklist |

#VSLIDE

## Third Party limitations

- Rate |
- Cost |
- Availability |
- Access Restrictions |

#VSLIDE

### Kroger third party limitations - examples 

- Vantiv :: Add new credit card type |
- Accertify :: Simulate all business rules that are not supported |


#HSLIDE

## Selective Mocking / Proxying

#VSLIDE

### Selective Mocking / Proxying

![WiremockProxying](assets/WireMock_Proxying.png)

#VSLIDE

## Proxying - example

```
{
  "priority": 10,
  "request" : {
    "urlPattern" : "/click-list-master-order/(.*)"
  },
  "response" : {
    "proxyBaseUrl" : "https://dc1-stage.kroger.com"
  }
}
```

@[7] (base URL for Master Order)
@[2] (Higher Priority)

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

@[13] (Delay 5 secs before sending response)