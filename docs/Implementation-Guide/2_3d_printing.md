# 1. Implementation Guide

This document contains the REQUIRED and RECOMMENDED standard functionality that must be implemented by any 3d-printing Service Provider Platform a.k.a BPPs and 3d-printing Consumer Platform a.k.a BAPs.

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119 [RFC2119].

## 1.1 Discovery of 3D Printing Services

### 1.1.1 Recommendations for BPPs
The following recommendations need to be considered when implementing discovery functionality for a 3d-printing BPP

- REQUIRED. The BPP MUST implement the `search` endpoint to receive an `Intent` object sent by BAPs
- REQUIRED. The BPP MUST return a catalog of 3d-printing products on the `on_search` callback endpoint specified in the `context.bap_uri` field of the `search` request body.
- REQUIRED. The BPP MUST map its 3d-printing service products to the `Item` schema.
- REQUIRED. Any 3d-printing service provider-related information like name, logo, short description must be mapped to the `Provider.descriptor` schema
- REQUIRED. Any form that must be filled before receiving a quotation must be mapped to the `XInput` schema
- REQUIRED. If the platform wants to group its products under a specific category, it must map each category to the `Category` schema
- REQUIRED. Any service fulfillment-related information MUST be mapped to the `Fulfillment` schema.
- REQUIRED. If the BPP does not want to respond to a search request, it MUST return a `ack.status` value equal to `NACK`
- RECOMMENDED. Upon receiving a `search` request, the BPP SHOULD return a catalog that best matches the intent. This can be done by indexing the catalog against the various probable paths in the `Intent` schema relevant to typical 3d-printing use cases

### 1.1.2 Recommendations for BAPs
- REQUIRED. The BAP MUST call the `search` endpoint of the BG to discover multiple BPPs on a network
- REQUIRED. The BAP MUST implement the `on_search` endpoint to consume the `Catalog` objects containing 3d-printing service sent by BPPs.
- REQUIRED. The BAP MUST expect multiple catalogs sent by the respective 3d-printing Providers on the network
- REQUIRED. The 3d-printing products can be found in the `Catalog.providers[].items[]` array in the `on_search` request
- REQUIRED. If the `catalog.providers[].items[].xinput` object is present, then the BAP MUST redirect the user to, or natively render the form present on the link specified on the `items[].xinput.form.url` field.
- REQUIRED. If the `catalog.providers[].items[].xinput.required` field is set to `"true"` , then the BAP MUST NOT fire a `select`, `init` or `confirm` call until the form is submitted and a successful response is received
- RECOMMENDED. If the `catalog.providers[].items[].xinput.required` field is set to `"false"` , then the BAP SHOULD allow the user to skip filling the form


### Example
A search request for a 3d printing service  may look like this
```
{
    "context": {
      "domain": "supply-chain-services:3d-printing",
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
        "provider": {
          "locations": [
            {
              "circle": {
                "gps": "40.748440, -73.984559",
                "radius": {
                  "type": "CONSTANT",
                  "value": "5",
                  "unit": "miles"
                }
              }
            }
          ],
          "rating": "gte>4"
        },
        "category": {
          "descriptor": {
            "code": "3d-printing"
          },
          "tags": [
            {
              "descriptor": {
                "code": "material-info",
                "name": "Material Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "product-material-type"
                  },
                  "value": "PVC-Plastic"
                }
              ]
            },
            {
              "descriptor": {
                "code": "technology-info",
                "name": "Technology Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "printing-technology-type"
                  },
                  "value": "FDM"
                }
              ]
            },
            {
              "descriptor": {
                "code": "specialisation-info",
                "name": "Specialisation Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "specialisation-type"
                  },
                  "value": "rapid-prototyping"
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
            },
            {
              "descriptor": {
                "code": "printer-info",
                "name": "Printer Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "Resolution"
                  },
                  "value": "gte>20%"
                },
                {
                  "descriptor": {
                    "name": "success-rate"
                  },
                  "value": "gte>95%"
                }
              ]
            }
          ]
        }
      }
    }
}
```

An example catalog of a 3d printing service may look like this
```
{
  "context": {
    "domain": "supply-chain-services:3d-printing",
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
              "id": "p1",
              "descriptor": {
                "code": "3d-printing",
                "name": "3D printing"
              }
            },
            {
              "id": "p1c1",
              "parent_category_id": "p1",
              "descriptor": {
                "code": "Stereolithography",
                "name": "Stereolithography"
              }
            },
            {
              "id": "p1c2",
              "parent_category_id": "p1",
              "descriptor": {
                "code": "Selective-Laser-Sintering",
                "name": "Selective-Laser-Sintering"
              }
            },
            {
              "id": "p1c3",
              "parent_category_id": "p1",
              "descriptor": {
                "code": "Digital-Light-Process",
                "name": "Digital Light Process"
              }
            },
            {
              "id": "p1c4",
              "parent_category_id": "p1",
              "descriptor": {
                "code": "Fused-Deposition-Modeling",
                "name": "Fused-Deposition-Modeling"
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
          "items": [
            {
              "id": "66b7b9bad166-4a3f-ada6-ca063dc9d321",
              "descriptor": {
                "images": [
                  {
                    "url": "https://makerspace/3d/SLAtype.img",
                    "size_type": "sm"
                  }
                ],
                "name": "SLA 3D Printing"
              },
              "category_ids": [
                "p1c1"
              ],
              "fulfillment_ids": [
                "f1",
                "f2"
              ],
              "tags": [
                {
                  "descriptor": {
                    "code": "material-info",
                    "name": "Material Information"
                  },
                  "list": [
                    {
                      "descriptor": {
                        "name": "product-material-type"
                      },
                      "value": "PVC-Plastic"
                    }
                  ]
                },
                {
                  "descriptor": {
                    "code": "technology-info",
                    "name": "Technology Information"
                  },
                  "list": [
                    {
                      "descriptor": {
                        "name": "printing-technology-type"
                      },
                      "value": "FDM"
                    }
                  ]
                },
                {
                  "descriptor": {
                    "code": "specialisation-info",
                    "name": "Specialisation Information"
                  },
                  "list": [
                    {
                      "descriptor": {
                        "name": "specialisation-type"
                      },
                      "value": "rapid-prototyping"
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
                },
                {
                  "descriptor": {
                    "code": "printer-info",
                    "name": "Printer Information"
                  },
                  "list": [
                    {
                      "descriptor": {
                        "name": "Resolution"
                      },
                      "value": "gte>20%"
                    },
                    {
                      "descriptor": {
                        "name": "success-rate"
                      },
                      "value": "gte>95%"
                    }
                  ]
                },
                {
                  "descriptor": {
                    "code": "color-info",
                    "name": "Color Info"
                  },
                  "list": [
                    {
                      "descriptor": {
                        "name": "color"
                      },
                      "value": "Red"
                    },
                    {
                      "descriptor": {
                        "name": "color"
                      },
                      "value": "Blue"
                    }
                  ]
                }
              ],
              "price": {
                "currency": "EUR",
                "value": "starting from 200 EUR"
              }
            },
            {
              "id": "0f3e9527-5929-403a-8c10-9386f910ef83",
              "descriptor": {
                "images": [
                  {
                    "url": "https://makerspace/3d/sls.img",
                    "size_type": "sm"
                  }
                ],
                "name": "SLS 3D printing"
              },
              "category_ids": [
                "c2"
              ],
              "fulfillment_ids": [
                "f1"
              ],
              "tags": [
                {
                  "descriptor": {
                    "code": "material-info",
                    "name": "Material Information"
                  },
                  "list": [
                    {
                      "descriptor": {
                        "name": "product-material-type"
                      },
                      "value": "PVC-Plastic"
                    },
                    {
                      "descriptor": {
                        "name": "product-material-type"
                      },
                      "value": "PLA"
                    }
                  ]
                },
                {
                  "descriptor": {
                    "code": "technology-info",
                    "name": "Technology Information"
                  },
                  "list": [
                    {
                      "descriptor": {
                        "name": "printing-technology-type"
                      },
                      "value": "FDM"
                    }
                  ]
                },
                {
                  "descriptor": {
                    "code": "specialisation-info",
                    "name": "Specialisation Information"
                  },
                  "list": [
                    {
                      "descriptor": {
                        "name": "specialisation-type"
                      },
                      "value": "rapid-prototyping"
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
                },
                {
                  "descriptor": {
                    "code": "printer-info",
                    "name": "Printer Information"
                  },
                  "list": [
                    {
                      "descriptor": {
                        "name": "Resolution"
                      },
                      "value": "gte>20%"
                    },
                    {
                      "descriptor": {
                        "name": "success-rate"
                      },
                      "value": "gte>95%"
                    }
                  ]
                },
                {
                  "descriptor": {
                    "code": "color-info",
                    "name": "Color Info"
                  },
                  "list": [
                    {
                      "descriptor": {
                        "name": "color"
                      },
                      "value": "Red"
                    },
                    {
                      "descriptor": {
                        "name": "color"
                      },
                      "value": "Blue"
                    }
                  ]
                }
              ],
              "price": {
                "currency": "EUR",
                "value": "starting from 200 EUR"
              }
            }
          ]
        }
      ]
    }
  }
}
```

## 1.2 Application for 3D Printing service
This section provides recommendations for implementing the APIs related to 3d-printing.

### 1.2.1 Recommendations for BPPs

#### 1.2.1.1 Selecting an 3d-printing service from the catalog
- REQUIRED. The BPP MUST implement the `select` endpoint on the url specified in the `context.bpp_uri` field sent during `on_search`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry
- REQUIRED. If the 3d printing provider has returned a successful acknowledgement to a `select` request, it MUST send the offer encapsulated in an `Order` object with the Xinput Schema in the first `on_select` request.
- REQUIRED. The BPP must check for a form submission at the URL specified on the `xinput.form.url` and send 2nd `on_select` request with the offer encapsulated in an `Order` object with the quote.


#### 1.2.1.2 Initializing an 3d-printing service
- REQUIRED. The BPP MUST implement the `init` endpoint on the url specified in  the `context.bpp_uri` field sent during `on_search`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry
- REQUIRED. The BPP MUST check for a form submission at the URL specified on the `xinput.form.url` before acknowledging a `init` request.
- REQUIRED. If the 3d printing provider has successfully received the information submitted by the 3d printing consumer, the BPP must return an acknowledgement with `ack.status` set to `ACK` in response to the `init` request


#### 1.2.1.3 Confirming the 3d-printing service
- REQUIRED. The BPP MUST implement the `confirm` endpoint on the url specified in URL specified in the `context.bpp_uri` field sent during `on_search`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

### 1.2.2 Recommendations for BAPs

#### 1.2.2.1 Selecting an 3d-printing service from the catalog
- REQUIRED. The BAP MUST implement the `on_select` endpoint on the url specified in the `context.bap_uri` field sent during `select`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry
- REQUIRED. The BAP user MUST submit the form on the url received from  first `on_select`  under `xinput.form.url`.


#### 1.2.2.2  Initializing an 3d-printing service
- REQUIRED. The BAP MUST hit the `init` endpoint on the url specified in  the `context.bpp_uri` field sent during `on_search`. 
- REQUIRED. The BAP MUST implement the `on_init` endpoint on the url specified in  the `context.bap_uri` field sent during `init`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

#### 1.2.2.3 Confirming the 3d-printing service
- REQUIRED. The BAP MUST hit the `confirm` endpoint on the url specified in  the `context.bpp_uri` field sent during `on_search`. 
- REQUIRED. The BAP MUST implement the `on_confirm` endpoint on the url specified in URL specified in the `context.bap_uri` field sent during `confirm`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

### 1.2.3 Example Workflow

### 1.2.3 Example Requests

Below is an example of a `select` request
```
{
    "context": {
      "domain": "supply-chain-services:3d-printing",
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
    "domain": "supply-chain-services:3d-printing",
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
                "url": "https://makerspace/3d/SLAtype.img",
                "size_type": "sm"
              }
            ],
            "name": "SLA 3D Printing"
          },
          "category_ids": [
            "p1c1"
          ],
          "tags": [
            {
              "descriptor": {
                "code": "material-info",
                "name": "Material Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "product-material-type"
                  },
                  "value": "PVC-Plastic"
                }
              ]
            },
            {
              "descriptor": {
                "code": "technology-info",
                "name": "Technology Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "printing-technology-type"
                  },
                  "value": "FDM"
                }
              ]
            },
            {
              "descriptor": {
                "code": "specialisation-info",
                "name": "Specialisation Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "specialisation-type"
                  },
                  "value": "rapid-prototyping"
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
            },
            {
              "descriptor": {
                "code": "printer-info",
                "name": "Printer Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "Resolution"
                  },
                  "value": "gte>20%"
                },
                {
                  "descriptor": {
                    "name": "success-rate"
                  },
                  "value": "gte>95%"
                }
              ]
            },
            {
              "descriptor": {
                "code": "color-info",
                "name": "Color Info"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "color"
                  },
                  "value": "Red"
                },
                {
                  "descriptor": {
                    "name": "color"
                  },
                  "value": "Blue"
                }
              ]
            }
          ],
          "xinput": {
            "required": true,
            "head": {
              "descriptor": {
                "name": "Configuration Form"
              },
              "index": {
                "min": 0,
                "cur": 0,
                "max": 0
              },
              "headings": [
                "Configuration Details"
              ]
            },
            "form": {
              "mime_type": "text/html",
              "url": "https://formurl/orderdetails/3d-printing",
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
            "value": "starting from 200 EUR"
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
          "value": "200"
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
      "domain": "supply-chain-services:3d-printing",
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
                  "url": "https://makerspace/3d/SLAtype.img",
                  "size_type": "sm"
                }
              ],
              "name": "SLA 3D Printing"
            },
            "category_ids": [
              "p1c1"
            ],
            "tags": [
              {
                "descriptor": {
                  "code": "material-info",
                  "name": "Material Information"
                },
                "list": [
                  {
                    "descriptor": {
                      "name": "product-material-type"
                    },
                    "value": "PVC-Plastic"
                  }
                ]
              },
              {
                "descriptor": {
                  "code": "technology-info",
                  "name": "Technology Information"
                },
                "list": [
                  {
                    "descriptor": {
                      "name": "printing-technology-type"
                    },
                    "value": "FDM"
                  }
                ]
              },
              {
                "descriptor": {
                  "code": "specialisation-info",
                  "name": "Specialisation Information"
                },
                "list": [
                  {
                    "descriptor": {
                      "name": "specialisation-type"
                    },
                    "value": "rapid-prototyping"
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
              },
              {
                "descriptor": {
                  "code": "printer-info",
                  "name": "Printer Information"
                },
                "list": [
                  {
                    "descriptor": {
                      "name": "Resolution"
                    },
                    "value": "gte>20%"
                  },
                  {
                    "descriptor": {
                      "name": "success-rate"
                    },
                    "value": "gte>95%"
                  }
                ]
              },
              {
                "descriptor": {
                  "code": "color-info",
                  "name": "Color Info"
                },
                "list": [
                  {
                    "descriptor": {
                      "name": "color"
                    },
                    "value": "Red"
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
                "value": "500"
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
            "value": "550"
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
    "domain": "supply-chain-services:3d-printing",
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
          "id": "66b7b9bad166-4a3f-ada6-ca063dc9d321",
          "tags": [
            {
              "descriptor": {
                "code": "material-info",
                "name": "Material Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "product-material-type"
                  },
                  "value": "PVC-Plastic"
                }
              ]
            },
            {
              "descriptor": {
                "code": "technology-info",
                "name": "Technology Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "printing-technology-type"
                  },
                  "value": "FDM"
                }
              ]
            },
            {
              "descriptor": {
                "code": "specialisation-info",
                "name": "Specialisation Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "specialisation-type"
                  },
                  "value": "rapid-prototyping"
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
            },
            {
              "descriptor": {
                "code": "printer-info",
                "name": "Printer Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "Resolution"
                  },
                  "value": "gte>20%"
                },
                {
                  "descriptor": {
                    "name": "success-rate"
                  },
                  "value": "gte>95%"
                }
              ]
            },
            {
              "descriptor": {
                "code": "color-info",
                "name": "Color Info"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "color"
                  },
                  "value": "Red"
                }
              ]
            }
          ]
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
    "domain": "supply-chain-services:3d-printing",
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
                "url": "https://makerspace/3d/SLAtype.img",
                "size_type": "sm"
              }
            ],
            "name": "SLA 3D Printing"
          },
          "category_ids": [
            "p1c1"
          ],
          "tags": [
            {
              "descriptor": {
                "code": "material-info",
                "name": "Material Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "product-material-type"
                  },
                  "value": "PVC-Plastic"
                }
              ]
            },
            {
              "descriptor": {
                "code": "technology-info",
                "name": "Technology Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "printing-technology-type"
                  },
                  "value": "FDM"
                }
              ]
            },
            {
              "descriptor": {
                "code": "specialisation-info",
                "name": "Specialisation Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "specialisation-type"
                  },
                  "value": "rapid-prototyping"
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
            },
            {
              "descriptor": {
                "code": "printer-info",
                "name": "Printer Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "Resolution"
                  },
                  "value": "gte>20%"
                },
                {
                  "descriptor": {
                    "name": "success-rate"
                  },
                  "value": "gte>95%"
                }
              ]
            },
            {
              "descriptor": {
                "code": "color-info",
                "name": "Color Info"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "color"
                  },
                  "value": "Red"
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
            "amount": "550",
            "currency": "EUR",
            "bank_account_number": "1234002341",
            "bank_code": "INB0004321"
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
              "value": "500"
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
          "value": "550"
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
    "domain": "supply-chain-services:3d-printing",
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
          "id": "66b7b9bad166-4a3f-ada6-ca063dc9d321",
          "tags": [
            {
              "descriptor": {
                "code": "material-info",
                "name": "Material Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "product-material-type"
                  },
                  "value": "PVC-Plastic"
                }
              ]
            },
            {
              "descriptor": {
                "code": "technology-info",
                "name": "Technology Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "printing-technology-type"
                  },
                  "value": "FDM"
                }
              ]
            },
            {
              "descriptor": {
                "code": "specialisation-info",
                "name": "Specialisation Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "specialisation-type"
                  },
                  "value": "rapid-prototyping"
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
            },
            {
              "descriptor": {
                "code": "printer-info",
                "name": "Printer Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "Resolution"
                  },
                  "value": "gte>20%"
                },
                {
                  "descriptor": {
                    "name": "success-rate"
                  },
                  "value": "gte>95%"
                }
              ]
            },
            {
              "descriptor": {
                "code": "color-info",
                "name": "Color Info"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "color"
                  },
                  "value": "Red"
                }
              ]
            }
          ]
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
            "amount": "550",
            "currency": "EUR",
            "bank_account_number": "1234002341",
            "bank_code": "INB0004321",
            "source_bank_code": "YDYYE8789574",
            "source_bank_account_number": "54378958487",
            "transaction_id": "a35b56cf-e5cf-41f1-9b5d-fa99d8d5ac8c"
          },
          "status": "PAID",
          "type": "PRE-ORDER"
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
    "domain": "supply-chain-services:3d-printing",
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
                "url": "https://makerspace/3d/SLAtype.img",
                "size_type": "sm"
              }
            ],
            "name": "SLA 3D Printing"
          },
          "category_ids": [
            "p1c1"
          ],
          "tags": [
            {
              "descriptor": {
                "code": "material-info",
                "name": "Material Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "product-material-type"
                  },
                  "value": "PVC-Plastic"
                }
              ]
            },
            {
              "descriptor": {
                "code": "technology-info",
                "name": "Technology Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "printing-technology-type"
                  },
                  "value": "FDM"
                }
              ]
            },
            {
              "descriptor": {
                "code": "specialisation-info",
                "name": "Specialisation Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "specialisation-type"
                  },
                  "value": "rapid-prototyping"
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
            },
            {
              "descriptor": {
                "code": "printer-info",
                "name": "Printer Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "Resolution"
                  },
                  "value": "gte>20%"
                },
                {
                  "descriptor": {
                    "name": "success-rate"
                  },
                  "value": "gte>95%"
                }
              ]
            },
            {
              "descriptor": {
                "code": "color-info",
                "name": "Color Info"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "color"
                  },
                  "value": "Red"
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
            "amount": "550",
            "currency": "EUR",
            "bank_account_number": "1234002341",
            "bank_code": "INB0004321",
            "source_bank_code": "YDYYE8789574",
            "source_bank_account_number": "54378958487",
            "transaction_id": "a35b56cf-e5cf-41f1-9b5d-fa99d8d5ac8c"
          },
          "status": "PAID",
          "type": "PRE-ORDER"
        }
      ],
      "quote": {
        "breakup": [
          {
            "price": {
              "currency": "EUR",
              "value": "500"
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
          "value": "550"
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

## 1.3 Fulfillment of 3d-printing Services
This section contains recommendations for implementing the APIs related to fulfilling a 3d-printing service

### 1.3.1 Recommendations for BPPs

#### 1.3.1.1 Sending status updates
- REQUIRED. The BPP MUST implement the `status` endpoint on the url specified in URL specified in the `context.bpp_uri` field sent during `on_search`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

#### 1.3.1.2 Updating an 3d-printing service
- REQUIRED. The BPP MUST implement the `update` endpoint on the url specified in URL specified in the `context.bpp_uri` field sent during `on_search`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

#### 1.3.1.3 Cancelling an 3d-printing service
- REQUIRED. The BPP MUST implement the `cancel` endpoint on the url specified in URL specified in the `context.bpp_uri` field sent during `on_search`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry
- REQUIRED. The BPP MUST implement the `get_cancellation_reasons` endpoint on the url specified in URL specified in the `context.bpp_uri` field sent during `on_search`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

#### 1.3.1.4 Real-time tracking
- REQUIRED. The BPP MUST implement the `track` endpoint on the url specified in URL specified in the `context.bpp_uri` field sent during `on_search`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

### 1.3.2 Recommendations for BAPs

#### 1.3.2.1 Receiving status updates
- REQUIRED. The BAP MUST implement the `on_status` endpoint on the url specified in URL specified in the `context.bap_uri` field sent during `status`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

#### 1.3.2.2 Updating an 3d-printing services
- REQUIRED. The BAP MUST implement the `on_update` endpoint on the url specified in URL specified in the `context.bap_uri` field sent during `update`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

#### 1.3.2.3 Cancelling an 3d-printing services
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
    "domain": "supply-chain-services:3d-printing",
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
    "domain": "supply-chain-services:3d-printing",
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
                "url": "https://makerspace/3d/SLAtype.img",
                "size_type": "sm"
              }
            ],
            "name": "SLA 3D Printing"
          },
          "category_ids": [
            "p1c1"
          ],
          "tags": [
            {
              "descriptor": {
                "code": "material-info",
                "name": "Material Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "product-material-type"
                  },
                  "value": "PVC-Plastic"
                }
              ]
            },
            {
              "descriptor": {
                "code": "technology-info",
                "name": "Technology Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "printing-technology-type"
                  },
                  "value": "FDM"
                }
              ]
            },
            {
              "descriptor": {
                "code": "specialisation-info",
                "name": "Specialisation Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "specialisation-type"
                  },
                  "value": "rapid-prototyping"
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
            },
            {
              "descriptor": {
                "code": "printer-info",
                "name": "Printer Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "Resolution"
                  },
                  "value": "gte>20%"
                },
                {
                  "descriptor": {
                    "name": "success-rate"
                  },
                  "value": "gte>95%"
                }
              ]
            },
            {
              "descriptor": {
                "code": "color-info",
                "name": "Color Info"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "color"
                  },
                  "value": "Red"
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
              "code": "PRINTING",
              "short_desc": "Items are getting printed..."
            },
            "updated_at": "2023-05-26T05:03:04.443Z"
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
            "amount": "550",
            "currency": "EUR",
            "bank_account_number": "1234002341",
            "bank_code": "INB0004321",
            "source_bank_code": "YDYYE8789574",
            "source_bank_account_number": "54378958487",
            "transaction_id": "a35b56cf-e5cf-41f1-9b5d-fa99d8d5ac8c"
          },
          "status": "PAID",
          "type": "PRE-ORDER"
        }
      ],
      "quote": {
        "breakup": [
          {
            "price": {
              "currency": "EUR",
              "value": "500"
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
          "value": "550"
        }
      },
      "cancellation_terms" : [
        {
          "cancellation_fee" : {
            "amount" : {
              "currency" : "INR",
              "value" : "Penalty upto 100 EUR"
            }
          }
        }
      ]
    }
  }
}
```

Below is an example of a `update` request
```
{
  "context": {
    "domain": "supply-chain-services:3d-printing",
    "location": {
      "country": {
        "code": "DE"
      }
    },
    "action": "update",
    "version": "1.1.0",
    "bap_uri": "https://supply-chain-protocol-network.becknprotocol.io/",
    "bap_id": "supply-chain-protocol.becknprotocol.io",
    "bpp_id": "bpp.supply-chain.makerspace.io",
    "bpp_uri": "https://bpp.supply-chain.makerspace.io",
    "transaction_id": "a9aaecca-10b7-4d19-b640-b047a7c62195",
    "message_id": "d03e57ed-8bb9-4a70-9a8e-9e47f39710d8",
    "timestamp": "2023-05-25T05:23:04.443Z",
    "ttl": "P30M"
  },
  "message": {
    "order": {
      "id":"b989c9a9-f603-4d44-b38d-26fd72286b38",
      "fulfillments": [
        {
          "customer": {
            "contact": {
              "phone": "+91-81347776660"
            }
          }
        }
      ]
    },
    "updated_target": "order.fulfillments[0].customer.contact.phone"
  }
}
```


Below is an example of an `on_update` callback
```
{
  "context": {
    "domain": "supply-chain-services:3d-printing",
    "location": {
      "country": {
        "code": "DE"
      }
    },
    "action": "on_update",
    "version": "1.1.0",
    "bap_uri": "https://supply-chain-protocol-network.becknprotocol.io/",
    "bap_id": "supply-chain-protocol.becknprotocol.io",
    "bpp_id": "bpp.supply-chain.makerspace.io",
    "bpp_uri": "https://bpp.supply-chain.makerspace.io",
    "transaction_id": "a9aaecca-10b7-4d19-b640-b047a7c62195",
    "message_id": "d03e57ed-8bb9-4a70-9a8e-9e47f39710d8",
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
                "url": "https://makerspace/3d/SLAtype.img",
                "size_type": "sm"
              }
            ],
            "name": "SLA 3D Printing"
          },
          "category_ids": [
            "p1c1"
          ],
          "tags": [
            {
              "descriptor": {
                "code": "material-info",
                "name": "Material Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "product-material-type"
                  },
                  "value": "PVC-Plastic"
                }
              ]
            },
            {
              "descriptor": {
                "code": "technology-info",
                "name": "Technology Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "printing-technology-type"
                  },
                  "value": "FDM"
                }
              ]
            },
            {
              "descriptor": {
                "code": "specialisation-info",
                "name": "Specialisation Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "specialisation-type"
                  },
                  "value": "rapid-prototyping"
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
            },
            {
              "descriptor": {
                "code": "printer-info",
                "name": "Printer Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "Resolution"
                  },
                  "value": "gte>20%"
                },
                {
                  "descriptor": {
                    "name": "success-rate"
                  },
                  "value": "gte>95%"
                }
              ]
            },
            {
              "descriptor": {
                "code": "color-info",
                "name": "Color Info"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "color"
                  },
                  "value": "Red"
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
              "phone": "+91-81347776660"
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
            "amount": "550",
            "currency": "EUR",
            "bank_account_number": "1234002341",
            "bank_code": "INB0004321",
            "source_bank_code": "YDYYE8789574",
            "source_bank_account_number": "54378958487",
            "transaction_id": "a35b56cf-e5cf-41f1-9b5d-fa99d8d5ac8c"
          },
          "status": "PAID",
          "type": "PRE-ORDER"
        }
      ],
      "quote": {
        "breakup": [
          {
            "price": {
              "currency": "EUR",
              "value": "500"
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
          "value": "550"
        }
      },
      "cancellation_terms" : [
        {
          "cancellation_fee" : {
            "amount" : {
              "currency" : "INR",
              "value" : "Penalty upto 100 EUR"
            }
          }
        }
      ]
    }
  }
}
```

Below is an example of a `track` request
```
{
  "context": {
    "domain": "supply-chain-services:3d-printing",
    "location": {
      "country": {
        "code": "DE"
      }
    },
    "action": "track",
    "version": "1.1.0",
    "bap_uri": "https://supply-chain-protocol-network.becknprotocol.io/",
    "bap_id": "supply-chain-protocol.becknprotocol.io",
    "bpp_id": "bpp.supply-chain.makerspace.io",
    "bpp_uri": "https://bpp.supply-chain.makerspace.io",
    "transaction_id": "a9aaecca-10b7-4d19-b640-b047a7c62195",
    "message_id": "b2e59d95-6cda-4b4d-a5ed-0d8d3a9742b6",
    "timestamp": "2023-05-25T05:23:04.443Z",
    "ttl": "P30M"
  },
  "message": {
    "order_id": "04389d8c-6a50-4664-9c08-4ee45fef44e8"
  }
}
```

Below is an example of an `on_track` callback
```
{
  "context": {
    "domain": "supply-chain-services:3d-printing",
    "location": {
      "country": {
        "code": "DE"
      }
    },
    "action": "on_track",
    "version": "1.1.0",
    "bap_uri": "https://supply-chain-protocol-network.becknprotocol.io/",
    "bap_id": "supply-chain-protocol.becknprotocol.io",
    "bpp_id": "bpp.supply-chain.makerspace.io",
    "bpp_uri": "https://bpp.supply-chain.makerspace.io",
    "transaction_id": "a9aaecca-10b7-4d19-b640-b047a7c62195",
    "message_id": "b2e59d95-6cda-4b4d-a5ed-0d8d3a9742b6",
    "timestamp": "2023-05-25T05:23:04.443Z",
    "ttl": "P30M"
  },
  "message": {
    "tracking": {
      "id": "66b7b9bad166",
      "url": "https://merkspace/tracking/201f6fa2-a2f7-42e7-a2e5-0f9e59aeeb80",
      "status": "active"
    }
  }
}
```

Below is an example of a `cancel` request
```
{
  "context": {
    "domain": "supply-chain-services:3d-printing",
    "location": {
      "country": {
        "code": "DE"
      }
    },
    "action": "cancel",
    "version": "1.1.0",
    "bap_uri": "https://supply-chain-protocol-network.becknprotocol.io/",
    "bap_id": "supply-chain-protocol.becknprotocol.io",
    "bpp_id": "bpp.supply-chain.makerspace.io",
    "bpp_uri": "https://bpp.supply-chain.makerspace.io",
    "transaction_id": "a9aaecca-10b7-4d19-b640-b047a7c62195",
    "message_id": "7f3f5b44-fa87-4ea2-b675-4991dd25728e",
    "timestamp": "2023-05-25T05:23:04.443Z",
    "ttl": "P30M"
  },
  "message": {
      "order_id": "04389d8c-6a50-4664-9c08-4ee45fef44e8",
      "cancellation_reason_id": "4",
      "descriptor": {
        "short_desc": "Order delayed"
      }
    }
}
```

Below is an example of an `on_cancel` request
```
{
  "context": {
    "domain": "supply-chain-services:3d-printing",
    "location": {
      "country": {
        "code": "DE"
      }
    },
    "action": "on_cancel",
    "version": "1.1.0",
    "bap_uri": "https://supply-chain-protocol-network.becknprotocol.io/",
    "bap_id": "supply-chain-protocol.becknprotocol.io",
    "bpp_id": "bpp.supply-chain.makerspace.io",
    "bpp_uri": "https://bpp.supply-chain.makerspace.io",
    "transaction_id": "a9aaecca-10b7-4d19-b640-b047a7c62195",
    "message_id": "92083fe0-372d-4e24-a902-108f3eb78e42",
    "timestamp": "2023-05-25T05:23:04.443Z",
    "ttl": "P30M"
  },
  "message": {
    "order": {
      "id":"b989c9a9-f603-4d44-b38d-26fd72286b38",
      "provider": {
        "name": "Makerspace",
        "short_desc": "Makerspace",
        "long_desc": "Makerspace, Hof",
        "images": [
          {
            "url": "makerspace_logo.png",
            "size_type": "sm"
          }
        ],
        "id": "1"
      },
      "items": [
        {
          "id": "66b7b9bad166-4a3f-ada6-ca063dc9d321",
          "descriptor": {
            "images": [
              {
                "url": "https://makerspace/3d/SLAtype.img",
                "size_type": "sm"
              }
            ],
            "name": "SLA 3D Printing"
          },
          "category_ids": [
            "p1c1"
          ],
          "tags": [
            {
              "descriptor": {
                "code": "material-info",
                "name": "Material Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "product-material-type"
                  },
                  "value": "PVC-Plastic"
                }
              ]
            },
            {
              "descriptor": {
                "code": "technology-info",
                "name": "Technology Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "printing-technology-type"
                  },
                  "value": "FDM"
                }
              ]
            },
            {
              "descriptor": {
                "code": "specialisation-info",
                "name": "Specialisation Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "specialisation-type"
                  },
                  "value": "rapid-prototyping"
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
            },
            {
              "descriptor": {
                "code": "printer-info",
                "name": "Printer Information"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "Resolution"
                  },
                  "value": "gte>20%"
                },
                {
                  "descriptor": {
                    "name": "success-rate"
                  },
                  "value": "gte>95%"
                }
              ]
            },
            {
              "descriptor": {
                "code": "color-info",
                "name": "Color Info"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "color"
                  },
                  "value": "Red"
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
          "customer": {
            "contact": {
              "email": "fox.judie@abc.org",
              "phone": "+91-81347776660"
            },
            "person": {
              "name": "Judie Fox"
            }
          },
          "id": "fid1",
          "state": {
            "descriptor": {
              "code": "CANCELLED",
              "short_desc": "cancellation reason..."
            },
            "updated_at": "2023-05-25T05:23:04.443Z"
          }
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
            "amount": "550",
            "currency": "EUR",
            "bank_account_number": "1234002341",
            "bank_code": "INB0004321",
            "source_bank_code": "YDYYE8789574",
            "source_bank_account_number": "54378958487",
            "transaction_id": "a35b56cf-e5cf-41f1-9b5d-fa99d8d5ac8c"
          },
          "status": "PAID",
          "type": "PRE-ORDER"
        }
      ],
      "quote": {
        "breakup": [
          {
            "price": {
              "currency": "EUR",
              "value": "500"
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
          "value": "550"
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

## 1.4 Post-fulfillment of 3d-printing Services
This section contains recommendations for implementing the APIs after fulfilling a 3d-printing service

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
    "domain": "supply-chain-services:3d-printing",
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
    "message_id": "b2e59d95-6cda-4b4d-a5ed-0d8d3a9742b6",
    "timestamp": "2023-05-25T05:23:04.443Z",
    "ttl": "P30S"
  }
}
```

Below is an example of an `rating_categories` callback
```
{
  "context": {
    "domain": "supply-chain-services:3d-printing",
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
    "message_id": "b2e59d95-6cda-4b4d-a5ed-0d8d3a9742b6",
    "timestamp": "2023-05-25T05:23:04.443Z",
    "ttl": "P30S"
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
    "domain": "supply-chain-services:3d-printing",
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
    "message_id": "b2e59d95-6cda-4b4d-a5ed-0d8d3a9742b6",
    "timestamp": "2023-05-25T05:23:04.443Z",
    "ttl": "P30M"
  },
  "message": {
    "ratings": [
      {
        "id": "66b7b9bad166-4a3f-ada6-ca063dc9d321",
        "rating_category": "Item",
        "value": "7"
      }
    ]
  }
}
```
Below is an example of an `on_rating` callback
```
{
  "context": {
    "domain": "supply-chain-services:3d-printing",
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
    "message_id": "b2e59d95-6cda-4b4d-a5ed-0d8d3a9742b6",
    "timestamp": "2023-05-25T05:23:04.443Z",
    "ttl": "P30M"
  },
  "message": {
    "feedback_form": {
        "form": {
          "url": "https://inds-network-bpp.becknprotocol.io/feedback/portal"
        },
        "required": "false"
    }
  }
}
```

Below is an example of a `support` request
```
{
  "context": {
    "domain": "supply-chain-services:3d-printing",
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
    "message_id": "2561bd3e-46fe-497b-bdea-b3e5b2645042",
    "timestamp": "2023-05-25T05:23:04.443Z",
    "ttl": "P30M"
  },
  "message": {
    "support": {
      "ref_id": "04389d8c-6a50-4664-9c08-4ee45fef44e8",
      "callback_phone": "+91-81347776660",
      "email": "manjunath@gmail.com"
    }
  }
}
```

Below is an example of an `on_support` callback
```
{
  "context": {
    "domain": "supply-chain-services:3d-printing",
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
    "message_id": "2561bd3e-46fe-497b-bdea-b3e5b2645042",
    "timestamp": "2023-05-25T05:23:04.443Z",
    "ttl": "P30M"
  },
  "message": {
    "support": {
      "ref_id": "d4975df5-b18c-4772-80ad-368669856d52",
      "phone": "+91 9988776543",
      "email": "abcd.makerspace@support.com"
    }
  }
}
```