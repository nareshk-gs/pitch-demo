#HSLIDE
## WireMock 
### More than just a Stub


![WireMock](assets/wiremock-logo.png)
 
 <font size=6>
Arun Dharmar (Thor)

Kalesh Rajappan (Personalization)
 
Naresh Gourishetty (Personalization)

#HSLIDE

## What slows us down?
<img src="assets/challenges.png" width="80%">

#HSLIDE

## What is WireMock?

### Advanced HTTP API simulator

<font size=6>
- WireMock is a library for stubbing and mocking web services. |
- It constructs a HTTP server that we could connect to as we would to an actual web service. |


#VSLIDE

## Key Features

<font size=6>
- HTTP response stubbing |
- Record/playback of stubs |
- Fault injection |
- Per-request conditional proxying |
- Stateful behaviour simulation |
- Configurable response delays |
- Runs in unit tests, as a standalone process or as a WAR app |
- Configurable via Java API, JSON files and JSON over HTTP |


#VSLIDE

## How WireMock?

```
java -jar wiremock-standalone-2.6.0.jar
```
- Just a jar and a bunch of JSONs |
- That's it! (No Gradle, No Maven, No artifactory) |
- Optionally use Java |

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

<font size=6>
Send transactions through WireMock to the target API

```
curl http://localhost:8080/coupons/scored/kroger
```

#HSLIDE

## Mapping

#### request-response pair

#VSLIDE

## Mapping - example

```json
{
  "priority": 1,
  "request" : {
    "urlPattern" : "/APITest/",
    "method" : "GET"
  },
  "response" : {
    "status" : 200,
    "jsonBody" : {
        "status" : "success"
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

## Request Matching

#VSLIDE

## Request Matching

- URL |
- HTTP Method |
- Query parameters |
- Headers |
- Basic authentication (a special case of header matching) |
- Cookies |
- Request body |
- Multipart/form-data |

#VSLIDE

## Request Matching

```
{
  "priority": 1,
  "request" : {
    "urlPattern" : "/click-list-store-capacity/reservation/(.*)",
    "method" : "GET",
    "headers": {
      "X-Correlation-Id": {
        "equalTo": "storecap_getreserve_500"
      }
    }
  },
  "response" : {
    "status" : 500,
    "bodyFileName" : "body-500.json",
    "transformers": ["response-template"],
    "headers" : {
      "Server" : "nginx",
      "Content-Type" : "application/json;charset=UTF-8",
      "Transfer-Encoding" : "chunked",
      "Connection" : "keep-alive",
      "Keep-Alive" : "timeout=5",
      "Transaction-Id" : "46b175ee-e18b-419c-80f5-0c2543201d4f"
    }
  }
}
```
@[4] (URL Pattern Match)
@[5] (REST Method Match)
@[6-10] (Header Match - like CorrelationID)

#HSLIDE

## DEMO

#HSLIDE

## Deploy WireMock

<font size=6>
Depending on the implementation we can run WireMock from within your 

- Java application |
- JUnit test |
- Servlet container |
- or as a standalone process. |
 

#VSLIDE

## Deploy WireMock

**DockerFile**
```
FROM docker-prod.registry.kroger.com/library/java-openjdk:8-latest

WORKDIR /app
EXPOSE 8080
COPY . /app/

CMD ["/app/entrypoint.sh"]
```

**entrypoint.sh**
```
#!/bin/sh
java -jar /app/wiremock-standalone-*.jar --port 8080 --local-response-templating --verbose
```

#HSLIDE

## Use Cases

#HSLIDE

## Improve Time to Market

#VSLIDE

#### Application Development - Traditional

![WireMock](assets/AD_timeline.png)

#VSLIDE

#### Application Development - Parallel

![WireMock](assets/AD_timeline2.png)

#HSLIDE

## Simulating Test Data

#VSLIDE

#### Response with specific data

```json
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

#### Simulating Faults

```json
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

```json
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

#### Response Headers

```json
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

#HSLIDE

## Hard to Setup Data

#VSLIDE

## Hard to Setup Data

- Item with no price |
- Item that always has promo price |
- Item always in Clicklist / not in Clicklist |
- Specific type of coupons |

#HSLIDE

## Third Party limitations

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

```json
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

#VSLIDE

## Proxying

- Flexibility | 
- Don't need to switch between mock vs actual service |
- Mock only what is required |

#HSLIDE

## Timeouts

#VSLIDE

## Timeout

![WiremockTimeout](assets/WireMockTimeout.png)

#VSLIDE

## Timeout - example

```json
{
  "priority": 1,
  "request" : {
    "urlPattern" : "/onlinepayments/order/auth/(.*)",
    "method" : "POST",
    "headers": {
      "X-Correlation-Id": {
        "equalTo": "walletauth_timeout"
      }
    }
  },
  "response" : {
    "fixedDelayMilliseconds": 5000,
    "transformers": ["response-template"],
    "status" : 500,
    "bodyFileName" : "body-500.json",
    "headers" : {
      "Server" : "nginx",
      "Content-Type" : "application/json;charset=UTF-8",
      "Transfer-Encoding" : "chunked",
      "Connection" : "keep-alive",
      "Keep-Alive" : "timeout=5",
      "Transaction-Id" : "46b175ee-e18b-419c-80f5-0c2543201d4f"
    }
  }
}
```

@[13] (Delay 5 secs before sending 500 response)

#VSLIDE

## Timeout and Proxy

![WiremockTimeoutAndProxy](assets/WireMockTimeoutPoxy.png)

#VSLIDE

### Timeout and Proxy  - example

```json
{
  "priority": 1,
  "request" : {
    "urlPattern" : "/onlinepayments/order/auth/(.*)",
    "method" : "POST",
    "headers": {
      "X-Correlation-Id": {
        "equalTo": "walletauth_timeout_Proxy"
      }
    }
  },
  "response" : {
    "fixedDelayMilliseconds": 5000,
    "proxyBaseUrl" : "https://ecsb-test.kroger.com"
  }
}
```

@[13] (Delay 5 secs)
@[14] (Forward request to actual service)

#HSLIDE

## RESPONSE Templating

#VSLIDE

## RESPONSE Templating

#### Get data from input to send in stubbed output

- Get data from URL / query parameters |
- Get data from request json |
- Get data from headers |
- Get data from cookies |

#VSLIDE

### RESPONSE Templating

```json
{
  "priority": 1,
  "request" : {
    "urlPattern" : "/click-list-master-order/sub-order/(.*)",
    "method" : "PUT",
    "headers": {
      "X-Correlation-Id": {
        "equalTo": "updateSubOrder_notFound"
      }
    }
  },
  "response" : {
    "status" : 404,
    "jsonBody" : {
      "transactionId": "07fa7203-c691-4212-921b-9e9fec54cbd8",
      "correlationId": "11801236283590334",
      "httpStatus": 404,
      "appErrorCode": "MasterOrderNotFound",
      "detail": {
        "errors": [
          "Master order not found for subOrderId: {{request.path.[2]}}"
        ],
        "context": {
          "subOrderId": "{{request.path.[2]}}"
        }
      }
    },
    "transformers": ["response-template"]
  }
}
```

@[28] (Enable templating)
@[21] (Get suborder from URL ```/click-list-master-order/sub-order/1180645484990963501```)

#HSLIDE

## Stateful Mocking

#VSLIDE

### Stateful Mocking
- Product price change during checkout

![ItemPriceChange](assets/ItemPriceChange_Scenario.png)

Note: User flow where user takes time to come to checkout and price changes during checkout

#VSLIDE

### Stateful Mocking 
- Product price change during checkout

![ItemPriceChange](assets/WireMockStateSample.png)

Note: 2 different mapping with same Scenario and different state and response

#VSLIDE

#### Stateful Mocking - State 1 - Promo

```json
{
  "scenarioName": "PromoExpiration",
  "requiredScenarioState": "Promo",
  "newScenarioState": "Regular",
  "priority": 1,
  "request" : {
    "url" : "/item-discovery/items?div=035&store=00577&upcs=0007447100050",
    "method" : "GET"
  },
  "response" : {
    "status" : 200,
    "bodyFileName" : "ItemDiscovery_Apple(Promo).json"
    }
}
```
@[2] (Scenario Name)
@[3] (Current State)
@[4] (Next State)
@[6-9] (Request Pattern Match)
@[12] (Response File - to send promo price)

#VSLIDE

#### Stateful Mocking - State 2 - Regular

```json
{
  "scenarioName": "PromoExpiration",
  "requiredScenarioState": "Regular",
  "newScenarioState": "Promo",
  "priority": 1,
  "request" : {
    "url" : "/item-discovery/items?div=035&store=00577&upcs=0007447100050",
    "method" : "GET"
  },
  "response" : {
    "status" : 200,
    "bodyFileName" : "ItemDiscovery_Apple(Regular).json"
    }
}
```
@[2] (Same Scenario Name)
@[3] (Current State)
@[4] (Next State)
@[6-9] (Same Request Pattern Match)
@[12] (Response File - to send Regular price)

#VSLIDE

### Stateful Mocking - Other examples

- Service internal retry scenarios |
- Order status for an order management system |
- Change in coupon status when loaded to card |

#HSLIDE

### Performance Testing

- Isolate the system under test by using mocks
- Introduce latency
- Save time on environment / data setup

#HSLIDE

## Admin APIs

#VSLIDE

### Admin APIs

```
/__admin/docs/
```
- Set of powerful APIs to control the Server at runtime |
- Add / Update / Remove Mappings |
- Intercept requests flowing through WireMock |
- Reset mappings / recordings / logs |
- Reset scenario of stateful Stub |
- Enabled immediately. No Server restart |
- Many more ... |

#HSLIDE

## Logging / Debugging

#VSLIDE

### Logging / Debugging

![WireMockDebugging](assets/WireMock_Logging_Debugging.png)

Note: WireMock records all the traffic going through the server.

#VSLIDE

#### Logging / Debugging - Sample (Wallet Auth)

```
POST https://mockcheckout-test.cfcdcinternaltest.kroger.com/__admin/requests/find
```

```json
{
    "url" : "/onlinepayments/order/auth/pay_924233"
}
```

```json
{
    "requests": [
        {
            "url": "/onlinepayments/order/auth/pay_924233",
            "absoluteUrl": "http://mockcheckout-test.cfcdcinternaltest.kroger.com/onlinepayments/order/auth/pay_924233",
            "method": "POST",
            "clientIp": "10.9.87.55, 10.9.87.55",
            "headers": {
                "Cookie": "aid=CB92EDDEAF51043E3B1983ECCF3142430784A944704F29E312EA5480DEC77D57A73DDD8A7224167AF1AC58A3A70EBE284D8B3AB444D2DC3DD244867740D4767B; aid_2=1522185913925|6405ae66-cd57-4886-bdad-e5e0fc34d947; loggedIn=yes; sid=6405ae66-cd57-4886-bdad-e5e0fc34d947; TS01572b6e=014d3549aee23e1cc5cb8f609a15af37a2f5ec4aafa20c0c8af9dc43af730335f1b1c4612ff96a8236a65ad809a9316943e6dcb0e757e19f1144e7df6f13d8f5b8726a2daa323049844c23ada13fa162e0a2118aee; TS0190491f=014d3549ae9de5f437bbdae4a037724a1be6c4c28fa20c0c8af9dc43af730335f1b1c4612faadcea6162718ad7a5217c435bef167504a061e68b6e51b92642b3c12cf340a8c743a251ab6346a177af7f42afd6003c045b80e476296574d67cae6120ad6edf9ea168acda4a1360041666fbe5675ae088e05c2624aaefaf765e724e601cae98; dtCookie=1$32D4A5691D17A24C58615A59D9817FC0|Banner|1|_default|1|RUM+Default+Application|1",
                "X-Cf-Instanceid": "634629cf-7e46-43a1-53c1-5a45",
                "Accept": "*/*",
                "X-Correlation-Id": "try again",
                "User-Agent": "PostmanRuntime/7.1.1",
                "X-Forwarded-Proto": "https",
                "Connection": "close",
                "X-Request-Start": "1522690601842",
                "Host": "mockcheckout-test.cfcdcinternaltest.kroger.com",
                "Accept-Encoding": "gzip, deflate",
                "X-Vcap-Request-Id": "999b8af8-9b4d-427f-7882-9116b0564a89",
                "Authorization": "Basic U1ZDQ05DUEFZT1JERVJEOjBuMnNqVXY0WGdKNENMakg4d0hTV2c=",
                "X-Cf-Instanceindex": "0",
                "X-B3-Spanid": "13319500551d0ed2",
                "X-Cf-Applicationid": "3de25567-8231-40f2-8c40-be3cb6204eac",
                "X-Forwarded-For": "10.9.87.55, 10.9.87.55",
                "X-B3-Traceid": "13319500551d0ed2",
                "Content-Length": "201",
                "Content-Type": "application/json"
            },
            "cookies": {
                "aid": "CB92EDDEAF51043E3B1983ECCF3142430784A944704F29E312EA5480DEC77D57A73DDD8A7224167AF1AC58A3A70EBE284D8B3AB444D2DC3DD244867740D4767B",
                "aid_2": "1522185913925|6405ae66-cd57-4886-bdad-e5e0fc34d947",
                "loggedIn": "yes",
                "sid": "6405ae66-cd57-4886-bdad-e5e0fc34d947",
                "TS01572b6e": "014d3549aee23e1cc5cb8f609a15af37a2f5ec4aafa20c0c8af9dc43af730335f1b1c4612ff96a8236a65ad809a9316943e6dcb0e757e19f1144e7df6f13d8f5b8726a2daa323049844c23ada13fa162e0a2118aee",
                "TS0190491f": "014d3549ae9de5f437bbdae4a037724a1be6c4c28fa20c0c8af9dc43af730335f1b1c4612faadcea6162718ad7a5217c435bef167504a061e68b6e51b92642b3c12cf340a8c743a251ab6346a177af7f42afd6003c045b80e476296574d67cae6120ad6edf9ea168acda4a1360041666fbe5675ae088e05c2624aaefaf765e724e601cae98",
                "dtCookie": "1$32D4A5691D17A24C58615A59D9817FC0|Banner|1|_default|1|RUM+Default+Application|1"
            },
            "browserProxyRequest": false,
            "loggedDate": 1522690601848,
            "bodyAsBase64": "ew0KCSJhcHBOYW1lIjogIkNsaWNrTGlzdCIsDQoJImFjY291bnROdW1iZXIiOiAiMTdiODRjNzUtMTQ2MC1jODMzLWFmYjktZWNmYWY5ZmEyZTI2IiwNCgkiYXV0aFR5cGUiOiAicmVhbFRpbWVBdXRoIiwNCgkiYW1vdW50IjogMTAwLjAwLA0KCSJwYXltZW50TWV0aG9kSWQiOiAiMjg0Njg1MCIsDQoJInBpY2t1cFN0b3JlSWQiOiAiMDE0MDAzODMiDQp9",
            "body": "{\r\n\t\"appName\": \"ClickList\",\r\n\t\"accountNumber\": \"17b84c75-1460-c833-afb9-ecfaf9fa2e26\",\r\n\t\"authType\": \"realTimeAuth\",\r\n\t\"amount\": 100.00,\r\n\t\"paymentMethodId\": \"2846850\",\r\n\t\"pickupStoreId\": \"01400383\"\r\n}",
            "loggedDateString": "2018-04-02T17:36:41Z",
            "queryParams": {}
        }
    ],
    "requestJournalDisabled": false
}
```

Note: Many other use cases. Debug why a particular case if failing if you only have a 500. PCI Testing made easy. Instant than ECHO.

#HSLIDE

## Dynamic Mocking

#VSLIDE

## Dynamic Mocking

- Fail Master Order Fulfil for a particular order|
- Order number is unknown until the order is created |
- Dynamically add mapping as part of the script |
- Remove the mapping at end of the script |

Note: For automation testing where correlation ID can't be controlled.

#VSLIDE

## Dynamic Mocking

![Dynamic Mapping](assets/TestScript_DynamicMapping.png)

#VSLIDE

## Dynamic Mocking

#### Mapping to be sent to Admin API

```
{
  "request" : {
    "urlPattern" : "/click-list-master-order/draft-order/{{master_order_number}}/fulfill",
    "method" : "PUT"
  },
  "response" : {
  	"fixedDelayMilliseconds": 5000,
    "status" : 500,
    "bodyFileName" : "body-500.json"
  }
}
```
@[3]({{master_order_number}} will be replaced with the actual master order number created and will be sent to Admin API)

#VSLIDE

## Dynamic Mocking

#### Admin API - Add Mapping

```
POST https://mockcheckout-test.cfcdcinternaltest.kroger.com/__admin/mappings
```

* Send the same mapping file as input.
* WireMock starts using the mapping instantly

```
"id": "68c47560-164a-4099-af1e-5fc12f2cc815"
```


#VSLIDE

## Dynamic Mocking

#### Admin API - Remove Mapping

```
DELETE https://mockcheckout-test.cfcdcinternaltest.kroger.com/__admin/mappings/37416e0c-76fc-4d39-b98a-df1d47de5cc6
```

* Send the mapping id in the URL
* WireMock instantly removes the mapping.

#HSLIDE

## Use Cases - Recap

#VSLIDE

#### Use Cases - Recap

<font size=6>
- Improve Time to Market 
- Simulating Test Data 
- Hard to Setup Data 
- Third Party Limitations 
- Selective Mocking / Proxying 
- Timeouts 
- Stateful Mocking
- Logging / Debugging
- Dynamic Mocking
- Performance Testing
- Increase in Test Automation coverage | 

#HSLIDE

## Chrome Extension

#VSLIDE

#### Chrome Extension - Configure Server 

<img src="assets/WireMockPostman_Setting.png" width="50%">

#VSLIDE

#### Chrome Extension - Edit Mappings

<img src="assets/WireMockPostman_Edit.png" width="50%">

#VSLIDE

#### Chrome Extension - Edit Mappings

<img src="assets/WireMockPostman_EditMapping.png" width="40%">

#HSLIDE

## Java helper library

#VSLIDE

### Java helper library

![WireMockHelper](assets/yaapita_helper.png)

```
 addDynamicMapping ("\Users\kon7017\File\mappingFile.json", 
        "mockcheckout-test.cfcdcinternaltest.kroger.com");
        
 deleteDynamicMappings ("2a12532b-299d-4679-aeed-65b755736569, 
        04ffca91-ff08-9d3f-0706-c0747058d2dd");
```

#HSLIDE

## WireMock in Digital

#VSLIDE

## WireMock in Digital
#### Checkout Web

![WireMockCheckout](assets/Checkout-Mock.png)

Note: Time out, Master Order Fulfil failure, Fraud

#VSLIDE

## WireMock in Digital
#### Checkout Mobile

![WireMockMobileCheckout](assets/MSL_WireMock.png)

Note: Time out, Various Item validations, 500/400 errors

#VSLIDE

## WireMock in Digital
#### Master Order

- CPS Timeouts
- Instacart
- Store capacity

#VSLIDE

## WireMock in Digital
#### Personalization

- Fail over / Fall back / Back fill / Error handling Testing for all services
- Parallel Development or Improve Time to Market
    - Recipe Ingredient to upc service mocked to support Good Eats
    - Third Party (Spot Front) services mocked for Monetization services

#HSLIDE

## QUESTIONS?
![WireMock](assets/Questions.png)

#HSLIDE
          
## Show is over!
![WireMock](assets/wiremock-logo.png)
#### http://wiremock.org/

####  'Under testing' is a sin, 'over testing' is a crime 