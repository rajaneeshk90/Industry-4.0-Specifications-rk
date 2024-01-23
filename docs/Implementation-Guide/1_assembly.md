# 1. Implementation Guide

This document contains the REQUIRED and RECOMMENDED standard functionality that must be implemented by any assembly Service Provider Platform a.k.a BPPs and assembly Consumer Platform a.k.a BAPs.

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119 [RFC2119].

## 1.1 Discovery of Assembly Services

### 1.1.1 Recommendations for BPPs
The following recommendations need to be considered when implementing discovery functionality for a assembly BPP

- REQUIRED. The BPP MUST implement the `search` endpoint to receive an `Intent` object sent by BAPs
- REQUIRED. The BPP MUST return a catalog of assembly products on the `on_search` callback endpoint specified in the `context.bap_uri` field of the `search` request body.
- REQUIRED. The BPP MUST map its assembly service products to the `Item` schema.
- REQUIRED. Any assembly service provider-related information like name, logo, short description must be mapped to the `Provider.descriptor` schema
- REQUIRED. Any form that must be filled before receiving a quotation must be mapped to the `XInput` schema
- REQUIRED. If the platform wants to group its products under a specific category, it must map each category to the `Category` schema
- REQUIRED. Any service fulfillment-related information MUST be mapped to the `Fulfillment` schema.
- REQUIRED. If the BPP does not want to respond to a search request, it MUST return a `ack.status` value equal to `NACK`
- RECOMMENDED. Upon receiving a `search` request, the BPP SHOULD return a catalog that best matches the intent. This can be done by indexing the catalog against the various probable paths in the `Intent` schema relevant to typical assembly use cases

### 1.1.2 Recommendations for BAPs
- REQUIRED. The BAP MUST call the `search` endpoint of the BG to discover multiple BPPs on a network
- REQUIRED. The BAP MUST implement the `on_search` endpoint to consume the `Catalog` objects containing assembly services sent by BPPs.
- REQUIRED. The BAP MUST expect multiple catalogs sent by the respective service assembly Providers on the network
- REQUIRED. The assembly products can be found in the `Catalog.providers[].items[]` array in the `on_search` request
- REQUIRED. If the `catalog.providers[].items[].xinput` object is present, then the BAP MUST redirect the user to, or natively render the form present on the link specified on the `items[].xinput.form.url` field.
- REQUIRED. If the `catalog.providers[].items[].xinput.required` field is set to `"true"` , then the BAP MUST NOT fire a `select`, `init` or `confirm` call until the form is submitted and a successful response is received
- RECOMMENDED. If the `catalog.providers[].items[].xinput.required` field is set to `"false"` , then the BAP SHOULD allow the user to skip filling the form


### Example
A search request for an assembly service  may look like this
```
{
    "context": {
      "domain": "supply-chain-services:assembly",
      "location": {
        "country": {
          "code": "DE"
        }
      },
      "transaction_id": "a9aaecca-10b7-4d19-b640-b047a7c62196",
      "message_id": "bb579fb8-cb82-4824-be12-fcbc405b6608",
      "action": "search",
      "timestamp": "2023-05-25T05:23:03.443Z",
      "version": "1.1.0",
      "bap_uri": "https://supply-chain-protocol-network.becknprotocol.io/",
      "bap_id": "supply-chain-protocol.becknprotocol.io",
      "ttl": "PT10M"
    },
    "message": {
      "intent": {
        "category": {
          "descriptor": {
            "code": "assembly"
          }
        }
      }
    }
}
```

An example catalog of an assembly service may look like this
```
{
  "context": {
    "domain": "supply-chain-services:assembly",
    "location": {
      "country": {
        "code": "DE"
      }
    },
    "version": "1.1.0",
    "action": "on_search",
    "bap_id": "supply-chain-protocol.becknprotocol.io",
    "bap_uri": "https://supply-chain-protocol-network.becknprotocol.io/",
    "transaction_id": "a9aaecca-10b7-4d19-b640-b047a7c62196",
    "message_id": "bb579fb8-cb82-4824-be12-fcbc405b6608",
    "ttl": "PT30M",
    "timestamp": "2023-05-25T05:23:03.443Z",
    "bpp_id": "bpp.supply-chain.makerspace.io",
    "bpp_uri": "https://bpp.supply-chain.makerspace.io"
  },
  "message": {
    "catalog": {
      "descriptor": {
        "name": "Makerspace"
      },
      "providers": [
        {
          "id": "1",
          "descriptor": {
            "images": [
              {
                "url": "makerspace_logo.png",
                "size_type": "sm"
              }
            ],
            "name": "Makerspace",
            "short_desc": "Makerspace",
            "long_desc": "Makerspace, Hof."
          },
          "categories": [
            {
              "id": "p2",
              "descriptor": {
                "code": "assembly",
                "name": "Assembly"
              }
            },
            {
              "id": "p2c1",
              "parent_category_id": "p2",
              "descriptor": {
                "code": "classic",
                "name": "Classic"
              }
            },
            {
              "id": "p2c2",
              "parent_category_id": "p2",
              "descriptor": {
                "code": "automated",
                "name": "Automated"
              }
            },
            {
              "id": "p2c3",
              "parent_category_id": "p2",
              "descriptor": {
                "code": "intermittent",
                "name": "Intermittent"
              }
            },
            {
              "id": "p2c4",
              "parent_category_id": "p2",
              "descriptor": {
                "code": "lean",
                "name": "Lean"
              }
            }
          ],
          "fulfillments": [
            {
              "id": "f1",
              "type": "Delivery"
            },
            {
              "id": "f2",
              "type": "Self-Pickup"
            }
          ],
          "locations": [
            {
                "id": "location/hof",
                "city": {
                    "code": 95028,
                    "name": "HOF"
                },
                "gps": "50.311674, 11.903358"
            }
          ],
          "rating": "3.7",
          "items": [
            {
              "id": "66b7b9bad166-4a3f-ada6-ca063dc9d321",
              "descriptor": {
                "images": [
                  {
                    "url": "https://makerspace/assembly/intermittent.png",
                    "size_type": "sm"
                  }
                ],
                "name": "Intermittent assembly type"
              },
              "category_ids" : [
                "c3"
              ],
              "location_ids": [
                "location/hof"
              ],
              "fulfillment_ids": ["f1"],
              "tags": [
                {
                  "descriptor": {
                    "code": "product-info",
                    "name": "Product Information"
                  },
                  "list": [
                    {
                      "descriptor": {
                        "name": "product-type"
                      },
                      "value": "electronics"
                    }
                  ]
                },
                {
                  "descriptor": {
                    "code": "assembly-info",
                    "name": "Assembly Information"
                  },
                  "list": [
                    {
                      "descriptor": {
                        "name": "assembly-method"
                      },
                      "value": "automated"
                    }
                  ]
                },
                {
                  "descriptor": {
                    "code": "scale-info",
                    "name": "Scale Information"
                  },
                  "list": [
                    {
                      "descriptor": {
                        "name": "scale-type"
                      },
                      "value": "industrial"
                    }
                  ]
                }
              ],
              "price": {
                "currency": "EUR",
                "value": "starting from 50 EUR"
              }
            }
          ]
        }
      ]
    }
  }
}
```

## 1.2 Application for Assembly service
This section provides recommendations for implementing the APIs related to assembly.

### 1.2.1 Recommendations for BPPs

#### 1.2.1.1 Selecting an assembly service from the catalog
- REQUIRED. The BPP MUST implement the `select` endpoint on the url specified in the `context.bpp_uri` field sent during `on_search`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry
- REQUIRED. If the assembly provider has returned a successful acknowledgement to a `select` request, it MUST send the offer encapsulated in an `Order` object with the Xinput Schema in the first `on_select` request.
- REQUIRED. The BPP must check for a form submission at the URL specified on the `xinput.form.url` and send 2nd `on_select` request with the offer encapsulated in an `Order` object with the quote.

#### 1.2.1.2 Initializing an assembly service
- REQUIRED. The BPP MUST implement the `init` endpoint on the url specified in  the `context.bpp_uri` field sent during `on_search`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry
- REQUIRED. The BPP MUST check for a form submission at the URL specified on the `xinput.form.url` before acknowledging a `init` request.
- REQUIRED. If the assembly provider has successfully received the information submitted by the assembly consumer, the BPP must return an acknowledgement with `ack.status` set to `ACK` in response to the `init` request


#### 1.2.1.3 Confirming the assembly service
- REQUIRED. The BPP MUST implement the `confirm` endpoint on the url specified in URL specified in the `context.bpp_uri` field sent during `on_search`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

### 1.2.2 Recommendations for BAPs

#### 1.2.2.1 Selecting an assembly service from the catalog
- REQUIRED. The BAP MUST implement the `on_select` endpoint on the url specified in the `context.bap_uri` field sent during `select`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry
- REQUIRED. The BAP user MUST submit the form on the url received from  first `on_select`  under `xinput.form.url`.


#### 1.2.2.2  Initializing an assembly service
- REQUIRED. The BAP MUST hit the `init` endpoint on the url specified in  the `context.bpp_uri` field sent during `on_search`. 
- REQUIRED. The BAP MUST implement the `on_init` endpoint on the url specified in  the `context.bap_uri` field sent during `init`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

#### 1.2.2.3 Confirming the assembly service
- REQUIRED. The BAP MUST hit the `confirm` endpoint on the url specified in  the `context.bpp_uri` field sent during `on_search`. 
- REQUIRED. The BAP MUST implement the `on_confirm` endpoint on the url specified in URL specified in the `context.bap_uri` field sent during `confirm`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

### 1.2.3 Example Workflow

### 1.2.3 Example Requests

Below is an example of a `select` request
```
{
    "context": {
      "domain": "supply-chain-services:assembly",
      "location": {
        "country": {
          "code": "DE"
        }
      },
      "transaction_id": "a9aaecca-10b7-4d19-b640-b047a7c62196",
      "message_id": "13abc9ad-3d5e-4f68-83cb-d85f96b81cd5",
      "action": "select",
      "timestamp": "2023-05-25T05:23:03.443Z",
      "version": "1.1.0",
      "bap_uri": "https://supply-chain-protocol-network.becknprotocol.io/",
      "bap_id": "supply-chain-protocol.becknprotocol.io",
      "ttl": "PT10M",
      "bpp_id": "bpp.supply-chain.makerspace.io",
      "bpp_uri": "https://bpp.supply-chain.makerspace.io"
    },
    "message": {
      "order": {
        "provider": {
          "id": "1"
        }, 
        "items": [
          {
            "id": "66b7b9bad166-4a3f-ada6-ca063dc9d321"
          }
        ],
        "fulfillments": [
          {
            "id": "f1"
          }
        ]
      }
    }
  }
```

Below is an example of an `on_select #1` callback
```
{
    "context": {
      "domain": "supply-chain-services:assembly",
      "location": {
        "country": {
          "code": "DE"
        }
      },
      "action": "on_select",
      "version": "1.1.0",
      "bap_uri": "https://supply-chain-protocol-network.becknprotocol.io/",
      "bap_id": "supply-chain-protocol.becknprotocol.io",
      "bpp_id": "bpp.supply-chain.makerspace.io",
      "bpp_uri": "https://bpp.supply-chain.makerspace.io",
      "transaction_id": "a9aaecca-10b7-4d19-b640-b047a7c62195",
      "message_id": "13abc9ad-3d5e-4f68-83cb-d85f96b81cd5",
      "timestamp": "2023-05-25T05:23:03.443Z",
      "ttl": "P30M"
    },
    "message": {
      "order": {
        "provider": {
          "id": "1",
          "descriptor": {
            "images": [
              {
                "url": "makerspace_logo.png",
                "size_type": "sm"
              }
            ],
            "name": "Makerspace",
            "short_desc": "Makerspace",
            "long_desc": "Makerspace, Hof."
          }
        },
        "items": [
          {
            "id": "66b7b9bad166-4a3f-ada6-ca063dc9d321",
            "descriptor": {
              "images": [
                {
                  "url": "https://makerspace/assembly/intermittent.img",
                  "size_type": "sm"
                }
              ],
              "name": "Intermittent assembly type"
            },
            "category_ids": [
              "p2c3"
            ],
            "tags": [
              {
                "descriptor": {
                  "code": "product-info",
                  "name": "Product Information"
                },
                "list": [
                  {
                    "descriptor": {
                      "name": "product-type"
                    },
                    "value": "electronics"
                  }
                ]
              },
              {
                "descriptor": {
                  "code": "assembly-info",
                  "name": "Assembly Information"
                },
                "list": [
                  {
                    "descriptor": {
                      "name": "assembly-method"
                    },
                    "value": "automated"
                  }
                ]
              },
              {
                "descriptor": {
                  "code": "scale-info",
                  "name": "Scale Information"
                },
                "list": [
                  {
                    "descriptor": {
                      "name": "scale-type"
                    },
                    "value": "industrial"
                  }
                ]
              }
            ],
            "xinput": {
              "required": true,
              "head": {
                "descriptor": {
                  "name": "Configuration Form Assembly"
                },
                "index": {
                  "min": 0,
                  "cur": 0,
                  "max": 0
                },
                "headings": [
                  "Configuration Details for Assembly Service"
                ]
              },
              "form": {
                "mime_type": "text/html",
                "url": "https://formurl/orderdetails/assembly",
                "resubmit": false,
                "auth": {
                  "descriptor": {
                    "code": "jwt"
                  },
                  "value": "eyJhbGciOiJIUzI.eyJzdWIiOiIxMjM0NTY3O.SflKxwRJSMeKKF2QT4"
                }
              }
            },
            "price": {
              "currency": "EUR",
              "value": "starting from 50 EUR"
            }
          }
        ],
        "fulfillments": [
          {
            "id": "f1"
          }
        ],
        "quote": {
          "price": {
            "currency": "EUR",
            "value": "50"
          }
        }
      }
    }
  }
```
Below is an example of an `on_select #2` callback
```
{
    "context": {
      "domain": "supply-chain-services:assembly",
      "location": {
        "country": {
          "code": "DE"
        }
      },
      "action": "on_select",
      "version": "1.1.0",
      "bap_uri": "https://supply-chain-protocol-network.becknprotocol.io/",
      "bap_id": "supply-chain-protocol.becknprotocol.io",
      "bpp_id": "bpp.supply-chain.makerspace.io",
      "bpp_uri": "https://bpp.supply-chain.makerspace.io",
      "transaction_id": "a9aaecca-10b7-4d19-b640-b047a7c62195",
      "message_id": "13abc9ad-3d5e-4f68-83cb-d85f96b81cd5",
      "timestamp": "2023-05-25T05:23:03.443Z",
      "ttl": "P30M"
    },
    "message": {
      "order": {
        "provider": {
          "id": "1",
          "descriptor": {
            "images": [
              {
                "url": "makerspace_logo.png",
                "size_type": "sm"
              }
            ],
            "name": "Makerspace",
            "short_desc": "Makerspace",
            "long_desc": "Makerspace, Hof."
          }
        },
        "items": [
          {
            "id": "66b7b9bad166-4a3f-ada6-ca063dc9d321",
            "descriptor": {
              "images": [
                {
                  "url": "https://makerspace/assembly/intermittent.img",
                  "size_type": "sm"
                }
              ],
              "name": "Intermittent assembly type"
            },
            "category_ids": [
              "p2c3"
            ],
            "tags": [
              {
                "descriptor": {
                  "code": "product-info",
                  "name": "Product Information"
                },
                "list": [
                  {
                    "descriptor": {
                      "name": "product-type"
                    },
                    "value": "electronics"
                  }
                ]
              },
              {
                "descriptor": {
                  "code": "assembly-info",
                  "name": "Assembly Information"
                },
                "list": [
                  {
                    "descriptor": {
                      "name": "assembly-method"
                    },
                    "value": "automated"
                  }
                ]
              },
              {
                "descriptor": {
                  "code": "scale-info",
                  "name": "Scale Information"
                },
                "list": [
                  {
                    "descriptor": {
                      "name": "scale-type"
                    },
                    "value": "industrial"
                  }
                ]
              }
            ],
            "price": {
              "currency": "EUR",
              "value": "starting from 50 EUR"
            }
          }
        ],
        "fulfillments": [
          {
            "id": "f1",
            "stops": [
              {
                "type": "end",
                "location": {
                  "gps": "1.3806217468119772, 103.74636438437074",
                  "area_code": "680230"
                }
              }
            ]
          }
        ],
        "quote": {
          "breakup": [
            {
              "price": {
                "currency": "EUR",
                "value": "200"
              },
              "title": "Base Price"
            },
            {
              "price": {
                "currency": "EUR",
                "value": "50"
              },
              "title": "Tax"
            }
          ],
          "price": {
            "currency": "EUR",
            "value": "250"
          }
        }
      }
    }
  }
```

Below is an example of a `init` request
```
{
  "context": {
    "domain": "supply-chain-services:assembly",
    "location": {
      "country": {
        "code": "DE"
      }
    },
    "action": "init",
    "version": "1.1.0",
    "bap_uri": "https://supply-chain-protocol-network.becknprotocol.io/",
    "bap_id": "supply-chain-protocol.becknprotocol.io",
    "bpp_id": "bpp.supply-chain.makerspace.io",
    "bpp_uri": "https://bpp.supply-chain.makerspace.io",
    "transaction_id": "a9aaecca-10b7-4d19-b640-b047a7c62195",
    "message_id": "27f9d99a-5ec6-11ee-8c99-0242ac120002",
    "timestamp": "2023-05-25T05:23:03.443Z",
    "ttl": "P30M"
  },
  "message": {
    "order": {
      "provider": {
        "id": "1"
      },
      "items": [
        {
          "id": "66b7b9bad166-4a3f-ada6-ca063dc9d321"
        }
      ],
      "fulfillments": [
        {
          "id": "f1",
          "customer": {
            "contact": {
              "email": "fox.judie@abc.org",
              "phone": "+91-9999999999"
            },
            "person": {
              "name": "Judie Fox"
            }
          },
          "stops": [
            {
              "type": "end",
              "location": {
                "gps": "1.3806217468119772, 103.74636438437074",
                "address": "My House #, My building",
                "city": {
                  "name": "Jurong East"
                },
                "country": {
                  "code": "SGP"
                },
                "area_code":"680230",
                "state":{
                  "name": "bayern"
                }
              },
              "contact": {
                "phone": "9886098860"
              }
            }
          ]
        }
      ],
      "billing": {
        "name":"Industry buyer",
        "address": "B005 aspire heights, Jurong East, SGP, 680230",
        "state": {
          "name": "Jurong East"
        },
        "city": {
          "name": "Jurong East"
        },
        "email":"nobody@nomail.com",
        "phone":"9886098860"
      }
    }
  }
}
```

Below is an example of an `on_init` callback
```
{
  "context": {
    "domain": "supply-chain-services:assembly",
    "location": {
      "country": {
        "code": "DE"
      }
    },
    "action": "on_init",
    "version": "1.1.0",
    "bap_uri": "https://supply-chain-protocol-network.becknprotocol.io/",
    "bap_id": "supply-chain-protocol.becknprotocol.io",
    "bpp_id": "bpp.supply-chain.makerspace.io",
    "bpp_uri": "https://bpp.supply-chain.makerspace.io",
    "transaction_id": "a9aaecca-10b7-4d19-b640-b047a7c62195",
    "message_id": "27f9d99a-5ec6-11ee-8c99-0242ac120002",
    "timestamp": "2023-05-25T05:23:03.443Z",
    "ttl": "P30M"
  },
  "message": {
    "order": {
      "provider": {
        "descriptor": {
          "name": "Makerspace",
          "short_desc": "Makerspace",
          "long_desc": "Makerspace, Hof",
          "images": [
            {
              "url": "makerspace_logo.png",
              "size_type": "sm"
            }
          ]
        },
        "id": "1"
      },
      "items": [
        {
          "id": "66b7b9bad166-4a3f-ada6-ca063dc9d321",
          "descriptor": {
            "images": [
              {
                "url": "https://makerspace/assembly/intermittent.img",
                "size_type": "sm"
              }
            ],
            "name": "Intermittent assembly type"
          },
          "category_ids": [
            "p2c3"
          ],
          "tags": [
            {
              "descriptor": {
                "code": "product-info",
                "name": "Product Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "product-type"
                  },
                  "value": "electronics"
                }
              ]
            },
            {
              "descriptor": {
                "code": "assembly-info",
                "name": "Assembly Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "assembly-method"
                  },
                  "value": "automated"
                }
              ]
            },
            {
              "descriptor": {
                "code": "scale-info",
                "name": "Scale Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "scale-type"
                  },
                  "value": "industrial"
                }
              ]
            }
          ],
          "price": {
            "currency": "EUR",
            "value": "starting from 200 EUR"
          }
        }
      ],
      "fulfillments": [
        {
          "id": "f1",
          "customer": {
            "contact": {
              "email": "fox.judie@abc.org",
              "phone": "+91-9999999999"
            },
            "person": {
              "name": "Judie Fox"
            }
          },
          "stops": [
            {
              "type": "end",
              "location": {
                "gps": "1.3806217468119772, 103.74636438437074",
                "address": "My House #, My building",
                "city": {
                  "name": "Jurong East"
                },
                "country": {
                  "code": "SGP"
                },
                "area_code":"680230",
                "state":{
                  "name": "bayern"
                }
              },
              "contact": {
                "phone": "9886098860"
              }
            }
          ]
        }
      ],
      "billing": {
        "name":"Industry buyer",
        "address": "B005 aspire heights, Jurong East, SGP, 680230",
        "state": {
          "name": "Jurong East"
        },
        "city": {
          "name": "Jurong East"
        },
        "email":"nobody@nomail.com",
        "phone":"9886098860"
      },
      "payments": [
        {
          "collected_by": "BPP",
          "params": {
            "amount": "250",
            "currency": "EUR",
            "bank_account_number": "1234002341",
            "bank_code": "INB0004321",
            "bank_account_name": "Makerspace Assembly Ltd"
          },
          "status": "NOT-PAID",
          "type": "PRE-ORDER"
        }
      ],
      "quote": {
        "breakup": [
          {
            "price": {
              "currency": "EUR",
              "value": "200"
            },
            "title": "Base Price"
          },
          {
            "price": {
              "currency": "EUR",
              "value": "50"
            },
            "title": "Tax"
          }
        ],
        "price": {
          "currency": "EUR",
          "value": "250"
        }
      }
    }
  }
}
```

Below is an example of a `confirm` request
```
{
  "context": {
    "domain": "supply-chain-services:assembly",
    "location": {
      "country": {
        "code": "DE"
      }
    },
    "action": "confirm",
    "version": "1.1.0",
    "bap_uri": "https://supply-chain-protocol-network.becknprotocol.io/",
    "bap_id": "supply-chain-protocol.becknprotocol.io",
    "bpp_id": "bpp.supply-chain.makerspace.io",
    "bpp_uri": "https://bpp.supply-chain.makerspace.io",
    "transaction_id": "a9aaecca-10b7-4d19-b640-b047a7c62195",
    "message_id": "1d4a7fa4-3301-4d8a-8146-5ab5fd35db99",
    "timestamp": "2023-05-25T05:23:04.443Z",
    "ttl": "P30M"
  },
  "message": {
    "order": {
      "provider": {
        "id": "1"
      },
      "items": [
        {
          "id": "66b7b9bad166-4a3f-ada6-ca063dc9d321"
        }
      ],
      "fulfillments": [
        {
          "id": "f1",
          "customer": {
            "contact": {
              "email": "fox.judie@abc.org",
              "phone": "+91-9999999999"
            },
            "person": {
              "name": "Judie Fox"
            }
          },
          "stops": [
            {
              "type": "end",
              "location": {
                "gps": "1.3806217468119772, 103.74636438437074",
                "address": "My House #, My building",
                "city": {
                  "name": "Jurong East"
                },
                "country": {
                  "code": "SGP"
                },
                "area_code":"680230",
                "state":{
                  "name": "bayern"
                }
              },
              "contact": {
                "phone": "9886098860"
              }
            }
          ]
        }
      ],
      "billing": {
        "name":"Industry buyer",
        "address": "B005 aspire heights, Jurong East, SGP, 680230",
        "state": {
          "name": "Jurong East"
        },
        "city": {
          "name": "Jurong East"
        },
        "email":"nobody@nomail.com",
        "phone":"9886098860"
      },
      "payments": [
        {
          "collected_by": "BPP",
          "params": {
            "amount": "250",
            "currency": "EUR",
            "bank_account_number": "1234002341",
            "bank_code": "INB0004321",
            "bank_account_name": "Makerspace Assembly Ltd"
          },
          "status": "PAID",
          "type": "PRE-ORDER",
          "transaction_id": "a35b56cf-e5cf-41f1-9b5d-fa99d8d5ac8c"
        }
      ]
    }
  }
}
```
Below is an example of an `on_confirm` callback
```
{
  "context": {
    "domain": "supply-chain-services:assembly",
    "location": {
      "country": {
        "code": "DE"
      }
    },
    "action": "on_confirm",
    "version": "1.1.0",
    "bap_uri": "https://supply-chain-protocol-network.becknprotocol.io/",
    "bap_id": "supply-chain-protocol.becknprotocol.io",
    "bpp_id": "bpp.supply-chain.makerspace.io",
    "bpp_uri": "https://bpp.supply-chain.makerspace.io",
    "transaction_id": "a9aaecca-10b7-4d19-b640-b047a7c62195",
    "message_id": "1d4a7fa4-3301-4d8a-8146-5ab5fd35db99",
    "timestamp": "2023-05-25T05:23:04.443Z",
    "ttl": "P30M"
  },
  "message": {
    "order": {
      "id":"b989c9a9-f603-4d44-b38d-26fd72286b38",
      "provider": {
        "descriptor": {
          "name": "Makerspace",
          "short_desc": "Makerspace",
          "long_desc": "Makerspace, Hof",
          "images": [
            {
              "url": "makerspace_logo.png",
              "size_type": "sm"
            }
          ]
        },
        "id": "1"
      },
      "items": [
        {
          "id": "66b7b9bad166-4a3f-ada6-ca063dc9d321",
          "descriptor": {
            "images": [
              {
                "url": "https://makerspace/assembly/intermittent.img",
                "size_type": "sm"
              }
            ],
            "name": "Intermittent assembly type"
          },
          "category_ids": [
            "p2c3"
          ],
          "tags": [
            {
              "descriptor": {
                "code": "product-info",
                "name": "Product Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "product-type"
                  },
                  "value": "electronics"
                }
              ]
            },
            {
              "descriptor": {
                "code": "assembly-info",
                "name": "Assembly Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "assembly-method"
                  },
                  "value": "automated"
                }
              ]
            },
            {
              "descriptor": {
                "code": "scale-info",
                "name": "Scale Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "scale-type"
                  },
                  "value": "industrial"
                }
              ]
            }
          ],
          "price": {
            "currency": "EUR",
            "value": "starting from 200 EUR"
          }
        }
      ],
      "fulfillments": [
        {
          "id": "f1",
          "state": {
            "descriptor": {
              "code": "ORDER_ACCEPTED",
              "short_desc": "Order has been confirmed..."
            }
          },
          "customer": {
            "contact": {
              "email": "fox.judie@abc.org",
              "phone": "+91-9999999999"
            },
            "person": {
              "name": "Judie Fox"
            }
          },
          "stops": [
            {
              "type": "end",
              "location": {
                "gps": "1.3806217468119772, 103.74636438437074",
                "address": "My House #, My building",
                "city": {
                  "name": "Jurong East"
                },
                "country": {
                  "code": "SGP"
                },
                "area_code":"680230",
                "state":{
                  "name": "bayern"
                }
              },
              "contact": {
                "phone": "9886098860"
              }
            }
          ]
        }
      ],
      "billing": {
        "name":"Industry buyer",
        "address": "B005 aspire heights, Jurong East, SGP, 680230",
        "state": {
          "name": "Jurong East"
        },
        "city": {
          "name": "Jurong East"
        },
        "email":"nobody@nomail.com",
        "phone":"9886098860"
      },
      "payments": [
        {
          "collected_by": "BPP",
          "params": {
            "amount": "250",
            "currency": "EUR",
            "bank_account_number": "1234002341",
            "bank_code": "INB0004321",
            "bank_account_name": "Makerspace Assembly Ltd"
          },
          "status": "PAID",
          "type": "PRE-ORDER",
          "transaction_id": "a35b56cf-e5cf-41f1-9b5d-fa99d8d5ac8c"
        }
      ],
      "quote": {
        "breakup": [
          {
            "price": {
              "currency": "EUR",
              "value": "200"
            },
            "title": "Base Price"
          },
          {
            "price": {
              "currency": "EUR",
              "value": "50"
            },
            "title": "Tax"
          }
        ],
        "price": {
          "currency": "EUR",
          "value": "250"
        }
      },
      "cancellation_terms" : [
        {
          "cancellation_fee" : {
            "amount" : {
              "currency" : "EUR",
              "value" : "100"
            }
          }
        }
      ]
    }
  }
}
```

## 1.3 Fulfillment of assembly Services
This section contains recommendations for implementing the APIs related to fulfilling a assembly service

### 1.3.1 Recommendations for BPPs

#### 1.3.1.1 Sending status updates
- REQUIRED. The BPP MUST implement the `status` endpoint on the url specified in URL specified in the `context.bpp_uri` field sent during `on_search`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

#### 1.3.1.2 Updating an assembly service
- REQUIRED. The BPP MUST implement the `update` endpoint on the url specified in URL specified in the `context.bpp_uri` field sent during `on_search`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

#### 1.3.1.3 Cancelling an assembly service
- REQUIRED. The BPP MUST implement the `cancel` endpoint on the url specified in URL specified in the `context.bpp_uri` field sent during `on_search`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry
- REQUIRED. The BPP MUST implement the `get_cancellation_reasons` endpoint on the url specified in URL specified in the `context.bpp_uri` field sent during `on_search`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

#### 1.3.1.4 Real-time tracking
- REQUIRED. The BPP MUST implement the `track` endpoint on the url specified in URL specified in the `context.bpp_uri` field sent during `on_search`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

### 1.3.2 Recommendations for BAPs

#### 1.3.2.1 Receiving status updates
- REQUIRED. The BAP MUST implement the `on_status` endpoint on the url specified in URL specified in the `context.bap_uri` field sent during `status`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

#### 1.3.2.2 Updating an assembly services
- REQUIRED. The BAP MUST implement the `on_update` endpoint on the url specified in URL specified in the `context.bap_uri` field sent during `update`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

#### 1.3.2.3 Cancelling an assembly services
- REQUIRED. The BAP MUST implement the `on_cancel` endpoint on the url specified in URL specified in the `context.bap_uri` field sent during `cancel`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry
- REQUIRED. The BAP MUST implement the `cancellation_reasons` endpoint on the url specified in URL specified in the `context.bap_uri` field sent during `get_cancellation_reasons`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

#### 1.3.2.4 Real-time tracking
- REQUIRED. The BAP MUST implement the `on_track` endpoint on the url specified in URL specified in the `context.bap_uri` field sent during `track`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

### 1.3.3 Example Workflow

### 1.3.4 Example Requests

Below is an example of a `status` request
```
{
  "context": {
    "domain": "supply-chain-services:assembly",
    "location": {
      "country": {
        "code": "DE"
      }
    },
    "action": "status",
    "version": "1.1.0",
    "bap_uri": "https://supply-chain-protocol-network.becknprotocol.io/",
    "bap_id": "supply-chain-protocol.becknprotocol.io",
    "bpp_id": "bpp.supply-chain.makerspace.io",
    "bpp_uri": "https://bpp.supply-chain.makerspace.io",
    "transaction_id": "a9aaecca-10b7-4d19-b640-b047a7c62195",
    "message_id": "2382b170-16ac-4092-a969-bc413932572a",
    "timestamp": "2023-05-25T05:23:04.443Z",
    "ttl": "P30M"
  },
  "message": {
      "order_id": "b989c9a9-f603-4d44-b38d-26fd72286b38"
  }
}
```

Below is an example of an `on_status` callback
```
{
  "context": {
    "domain": "supply-chain-services:assembly",
    "location": {
      "country": {
        "code": "DE"
      }
    },
    "action": "on_status",
    "version": "1.1.0",
    "bap_uri": "https://supply-chain-protocol-network.becknprotocol.io/",
    "bap_id": "supply-chain-protocol.becknprotocol.io",
    "bpp_id": "bpp.supply-chain.makerspace.io",
    "bpp_uri": "https://bpp.supply-chain.makerspace.io",
    "transaction_id": "a9aaecca-10b7-4d19-b640-b047a7c62195",
    "message_id": "1d4a7fa4-3301-4d8a-8146-5ab5fd35db99",
    "timestamp": "2023-05-25T05:23:04.443Z",
    "ttl": "P30M"
  },
  "message": {
    "order": {
      "id":"b989c9a9-f603-4d44-b38d-26fd72286b38",
      "provider": {
        "descriptor": {
          "name": "Makerspace",
          "short_desc": "Makerspace",
          "long_desc": "Makerspace, Hof",
          "images": [
            {
              "url": "makerspace_logo.png",
              "size_type": "sm"
            }
          ]
        },
        "id": "1"
      },
      "items": [
        {
          "id": "66b7b9bad166-4a3f-ada6-ca063dc9d321",
          "descriptor": {
            "images": [
              {
                "url": "https://makerspace/assembly/intermittent.img",
                "size_type": "sm"
              }
            ],
            "name": "Intermittent assembly service"
          },
          "category_ids": [
            "p2c3"
          ],
          "tags": [
            {
              "descriptor": {
                "code": "product-info",
                "name": "Product Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "product-type"
                  },
                  "value": "electronics"
                }
              ]
            },
            {
              "descriptor": {
                "code": "assembly-info",
                "name": "Assembly Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "assembly-method"
                  },
                  "value": "automated"
                }
              ]
            },
            {
              "descriptor": {
                "code": "scale-info",
                "name": "Scale Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "scale-type"
                  },
                  "value": "industrial"
                }
              ]
            }
          ],
          "price": {
            "currency": "EUR",
            "value": "starting from 200 EUR"
          }
        }
      ],
      "fulfillments": [
        {
          "id": "f1",
          "state": {
            "descriptor": {
              "code": "ITEM-PRINTING",
              "short_desc": "The item is in line for printing"
            },
            "updated_at": "2023-05-25T05:23:04.443Z"
          },
          "customer": {
            "contact": {
              "email": "fox.judie@abc.org",
              "phone": "+91-9999999999"
            },
            "person": {
              "name": "Judie Fox"
            }
          },
          "stops": [
            {
              "type": "end",
              "location": {
                "gps": "1.3806217468119772, 103.74636438437074",
                "address": "My House #, My building",
                "city": {
                  "name": "Jurong East"
                },
                "country": {
                  "code": "SGP"
                },
                "area_code":"680230",
                "state":{
                  "name": "bayern"
                }
              },
              "contact": {
                "phone": "9886098860"
              }
            }
          ]
        }
      ],
      "billing": {
        "name":"Industry buyer",
        "address": "B005 aspire heights, Jurong East, SGP, 680230",
        "state": {
          "name": "Jurong East"
        },
        "city": {
          "name": "Jurong East"
        },
        "email":"nobody@nomail.com",
        "phone":"9886098860"
      },
      "payments": [
        {
          "collected_by": "BPP",
          "params": {
            "amount": "250",
            "currency": "EUR",
            "bank_account_number": "1234002341",
            "bank_code": "INB0004321",
            "bank_account_name": "Makerspace assembly Ltd"
          },
          "status": "PAID",
          "type": "PRE-ORDER",
          "transaction_id": "a35b56cf-e5cf-41f1-9b5d-fa99d8d5ac8c"
        }
      ],
      "quote": {
        "breakup": [
          {
            "price": {
              "currency": "EUR",
              "value": "200"
            },
            "title": "Base Price"
          },
          {
            "price": {
              "currency": "EUR",
              "value": "50"
            },
            "title": "Tax"
          }
        ],
        "price": {
          "currency": "EUR",
          "value": "250"
        }
      },
      "cancellation_terms" : [
        {
          "cancellation_fee" : {
            "amount" : {
              "currency" : "EUR",
              "value" : "100"
            }
          }
        }
      ]
    }
  }
}
```

## 1.4 Post-fulfillment of assembly Services
This section contains recommendations for implementing the APIs after fulfilling a assembly service

### 1.4.1 Recommendations for BPPs

#### 1.4.1.1 Rating and Feedback
- REQUIRED. The BPP MUST implement the `rating` endpoint on the url specified in URL specified in the `context.bpp_uri` field sent during `on_search`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry
- REQUIRED. The BPP MUST implement the `get_rating_categories` endpoint on the url specified in URL specified in the `context.bpp_uri` field sent during `on_search`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

#### 1.4.1.2 Providing Support
- REQUIRED. The BPP MUST implement the `support` endpoint on the url specified in URL specified in the `context.bpp_uri` field sent during `on_search`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

### 1.4.2 Recommendations for BAPs

#### 1.4.2.1 Rating and Feedback
- REQUIRED. The BAP MUST implement the `on_rating` endpoint on the url specified in URL specified in the `context.bap_uri` field sent during `rating`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry
- REQUIRED. The BAP MUST implement the `rating_categories` endpoint on the url specified in URL specified in the `context.bap_uri` field sent during `get_rating_categories`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

#### 1.4.2.2 Providing Support
- REQUIRED. The BAP MUST implement the `on_support` endpoint on the url specified in URL specified in the `context.bap_uri` field sent during `support`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

### 1.4.3 Example Workflow

### 1.4.4 Example Requests

Below is an example of a `get_rating_categories` request
```
{
  "context": {
    "domain": "supply-chain-services:assembly",
    "location": {
      "country": {
        "code": "DE"
      }
    },
    "action": "get_rating_categories",
    "version": "1.1.0",
    "bap_uri": "https://supply-chain-protocol-network.becknprotocol.io/",
    "bap_id": "supply-chain-protocol.becknprotocol.io",
    "bpp_id": "bpp.supply-chain.makerspace.io",
    "bpp_uri": "https://bpp.supply-chain.makerspace.io",
    "transaction_id": "a9aaecca-10b7-4d19-b640-b047a7c62195",
    "message_id": "1d4a7fa4-3301-4d8a-8146-5ab5fd35db99",
    "timestamp": "2023-05-25T05:23:04.443Z",
    "ttl": "P30S"
  }
}

```

Below is an example of an `rating_categories` callback
```
{
  "context": {
    "domain": "supply-chain-services:assembly",
    "location": {
      "country": {
        "code": "DE"
      }
    },
    "action": "rating_categories",
    "version": "1.1.0",
    "bap_uri": "https://supply-chain-protocol-network.becknprotocol.io/",
    "bap_id": "supply-chain-protocol.becknprotocol.io",
    "bpp_id": "bpp.supply-chain.makerspace.io",
    "bpp_uri": "https://bpp.supply-chain.makerspace.io",
    "transaction_id": "a9aaecca-10b7-4d19-b640-b047a7c62195",
    "message_id": "1d4a7fa4-3301-4d8a-8146-5ab5fd35db99",
    "timestamp": "2024-01-15T16:00:00.000Z",
    "ttl": "PT30S"
  },
  "message": {
    "rating_categories": [
      "Item",
      "Order",
      "Fulfillment",
      "provider",
      "Agent",
      "SUpport"
    ]
  }
}
```

Below is an example of a `rating` request
```
{
  "context": {
    "domain": "supply-chain-services:assembly",
    "location": {
      "country": {
        "code": "DE"
      }
    },
    "action": "rating",
    "version": "1.1.0",
    "bap_uri": "https://supply-chain-protocol-network.becknprotocol.io/",
    "bap_id": "supply-chain-protocol.becknprotocol.io",
    "bpp_id": "bpp.supply-chain.makerspace.io",
    "bpp_uri": "https://bpp.supply-chain.makerspace.io",
    "transaction_id": "a9aaecca-10b7-4d19-b640-b047a7c62195",
    "message_id": "2f21ebf5-7ca2-416f-8c9f-9a0aed0c14d9",
    "timestamp": "2023-05-25T05:23:04.443Z",
    "ttl": "P30M"
  },
  "message": {
    "ratings": [
      {
        "id": "b989c9a9-f603-4d44-b38d-26fd72286b38",
        "rating_category": "Order",
        "value": "8"
      }
    ]
  }
}
```
Below is an example of an `on_rating` callback
```
{
  "context": {
    "domain": "supply-chain-services:assembly",
    "location": {
      "country": {
        "code": "DE"
      }
    },
    "action": "on_rating",
    "version": "1.1.0",
    "bap_uri": "https://supply-chain-protocol-network.becknprotocol.io/",
    "bap_id": "supply-chain-protocol.becknprotocol.io",
    "bpp_id": "bpp.supply-chain.makerspace.io",
    "bpp_uri": "https://bpp.supply-chain.makerspace.io",
    "transaction_id": "a9aaecca-10b7-4d19-b640-b047a7c62195",
    "message_id": "2f21ebf5-7ca2-416f-8c9f-9a0aed0c14d9",
    "timestamp": "2023-05-25T05:23:04.443Z",
    "ttl": "P30M"
  },
  "message": {
    "feedback_form": {
      "xinput": {
        "form": {
          "url": "https://inds-network-bpp.becknprotocol.io/feedback/portal"
        },
        "required": "false"
      }
    }
  }
}
```

Below is an example of a `support` request
```
{
  "context": {
    "domain": "supply-chain-services:assembly",
    "location": {
      "country": {
        "code": "DE"
      }
    },
    "action": "support",
    "version": "1.1.0",
    "bap_uri": "https://supply-chain-protocol-network.becknprotocol.io/",
    "bap_id": "supply-chain-protocol.becknprotocol.io",
    "bpp_id": "bpp.supply-chain.makerspace.io",
    "bpp_uri": "https://bpp.supply-chain.makerspace.io",
    "transaction_id": "a9aaecca-10b7-4d19-b640-b047a7c62195",
    "message_id": "90883e2b-7798-4278-90eb-00646400e615",
    "timestamp": "2023-05-25T05:23:04.443Z",
    "ttl": "P30M"
  },
  "message": {
    "support": {
      "ref_id": "894789-43954",
      "callback_phone": "+91-81347776660",
      "phone": "+91 9988776543",
      "email": "supportperson@gmail.com"
    }
  }
}
```

Below is an example of an `on_support` callback
```
{
  "context": {
    "domain": "supply-chain-services:assembly",
    "location": {
      "country": {
        "code": "DE"
      }
    },
    "action": "on_support",
    "version": "1.1.0",
    "bap_uri": "https://supply-chain-protocol-network.becknprotocol.io/",
    "bap_id": "supply-chain-protocol.becknprotocol.io",
    "bpp_id": "bpp.supply-chain.makerspace.io",
    "bpp_uri": "https://bpp.supply-chain.makerspace.io",
    "transaction_id": "a9aaecca-10b7-4d19-b640-b047a7c62195",
    "message_id": "90883e2b-7798-4278-90eb-00646400e615",
    "timestamp": "2023-05-25T05:23:04.443Z",
    "ttl": "P30M"
  },
  "message": {
    "support": {
      "ref_id": "d4975df5-b18c-4772-80ad",
      "callback_phone": "+91 8765495826",
      "phone": "+91 9876543298",
      "email": "abcd.makerspace@support.com"
    }
  }
}
```