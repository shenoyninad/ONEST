## Implementation Guide

This document contains the REQUIRED and RECOMMENDED standard functionality that must be implemented by any ONEST Provider Platform a.k.a BPPs and ONEST Consumer Platform a.k.a BAPs.

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119 [RFC2119].

## 1 Discovery of Course providers

### 1.1 Recommendations for BPPs

The following recommendations need to be considered when implementing discovery functionality for a BPP that provides courses.

- REQUIRED. The BPP MUST implement the `search` endpoint to receive an `Intent` object sent by BAPs
- REQUIRED. The BPP MUST return a catalog of courses on the `on_search` callback endpoint specified in the `context.bpp_uri` field of the `search` request body.
- REQUIRED. The BPP MUST map its course products to the `Item` schema.
- REQUIRED. Any course provider-related information like name, logo, short description must be mapped to the `Provider.descriptor` schema
- REQUIRED. Any form that must be filled before receiving a quotation must be mapped to the `XInput` schema
- REQUIRED. If the platform wants to group its products under a specific category, it must map each category to the `Category` schema
- REQUIRED. Any service fulfillment-related information MUST be mapped to the `Fulfillment` schema.
- REQUIRED. If the BPP does not want to respond to a search request, it MUST return a `ack.status` value equal to `NACK`
- RECOMMENDED. Upon receiving a `search` request, the BPP SHOULD return a catalog that best matches the intent. This can be done by indexing the catalog against the various probable paths in the `Intent` schema relevant to typical courses use cases

### 1.2 Recommendations for BAPs

- REQUIRED. The BAP MUST call the `search` endpoint of the BG to discover multiple BPPs on a network
- REQUIRED. The BAP MUST implement the `on_search` endpoint to consume the `Catalog` objects containing courses sent by BPPs.
- REQUIRED. The BAP MUST expect multiple catalogs sent by the respective Course Providers on the network
- REQUIRED. The courses can be found in the `Catalog.providers[].items[]` array in the `on_search` request
- REQUIRED. If the `catalog.providers[].items[].xinput` object is present, then the BAP MUST redirect the user to, or natively render the form present on the link specified on the `items[].xinput.form.url` field.
- REQUIRED. If the `catalog.providers[].items[].xinput.required` field is set to `"true"`, then the BAP MUST NOT fire a `select`, `init` or `confirm` call until the form is submitted and a successful response is received
- RECOMMENDED. If the `catalog.providers[].items[].xinput.required` field is set to `"false"`, then the BAP SHOULD allow the user to skip filling the form

### Example

A search request for an English course might look like this

```json
{
  "context": {
    "domain": "dsep:courses",
    "transaction_id": "a9aaecca-10b7-4d19-b640-b047a7c62196",
    "message_id": "$bb579fb8-cb82-4824-be12-fcbc405b6608",
    "action": "search",
    "timestamp": "2022-12-15T05:23:03.443Z",
    "version": "1.1.0",
    "bap_uri": "https://onest-network.becknprotocol.io/",
    "bap_id": "onest.becknprotocol.io",
    "ttl": "PT10M"
  },
  "message": {
    "intent": {
      "item": {
        "descriptor": {
          "name": "english"
        }
      }
    }
  }
}
```

An example catalog of english courses by a provider may look like this

```json
{
  "context": {
    "domain": "dsep:courses",
    "version": "1.0.0",
    "action": "on_search",
    "bap_id": "onest.becknprotocol.io",
    "bap_uri": "https://onest-network.becknprotocol.io/",
    "transaction_id": "a9aaecca-10b7-4d19-b640-b047a7c62196",
    "message_id": "7db91181-718f-4720-83de-88e36e9f854e",
    "ttl": "PT10M",
    "timestamp": "2023-02-22T10:30:18.145Z",
    "bpp_id": "infosys.springboard.io",
    "bpp_uri": "https://infosys.springboard.io"
  },
  "message": {
    "catalog": {
      "descriptor": {
        "name": "Catalog for English courses"
      },
      "providers": [
        {
          "id": "INFOSYS",
          "descriptor": {
            "name": "Infosys Springboard",
            "short_desc": "Infosys Springboard Digital literacy program",
            "images": [
              {
                "url": "https://infyspringboard.onwingspan.com/web/assets/images/infosysheadstart/app_logos/landing-new.png",
                "size_type": "sm"
              }
            ]
          },
          "categories": [
            {
              "id": "LANGUAGE-COURSES",
              "descriptor": {
                "name": "Language Courses"
              }
            },
            {
              "id": "SKILL-DEVELOPMENT-COURSES",
              "descriptor": {
                "name": "Skill development Courses"
              }
            },
            {
              "id": "TECHNICAL-COURSES",
              "descriptor": {
                "name": "Technical Courses"
              }
            }
          ],
          "items": [
            {
              "id": "Q291cnNlTGlzdDovbmQyX25jZTIyX3NjMjk=",
              "descriptor": {
                "name": "Everyday Conversational English",
                "long_desc": "Everyday Conversational English",
                "images": [
                  {
                    "url": "https://infyspringboard.onwingspan.com/web/assets/images/infosysheadstart/everyday-conversational-english.png"
                  }
                ]
              },
              "price": {
                "currency": "INR",
                "value": "0"
              },
              "category_id": "LANGUAGE-COURSES",
              "recommended": false,
              "time": {
                "label": "Course Schedule",
                "duration": "P24W",
                "range": {
                  "start": "2022-11-01T18:29:00.000000Z",
                  "end": "2025-03-31T18:29:00.000000Z"
                }
              },
              "rating": "3",
              "tags": [
                {
                  "descriptor": {
                    "name": "courseInfo"
                  },
                  "list": [
                    {
                      "descriptor": {
                        "name": "Instructors"
                      },
                      "value": "Prof. Shipra Vaidya"
                    },
                    {
                      "descriptor": {
                        "name": "Course Provider"
                      },
                      "value": "Infosys Springboard"
                    },
                    {
                      "descriptor": {
                        "name": "url"
                      },
                      "value": "https://infyspringboard.onwingspan.com/web/courses/infosysheadstart/everyday-conversational-english/preview/"
                    }
                  ],
                  "display": true
                }
              ],
              "rateable": false
            },
            {
              "id": "Q291cnNlTGlzdDovbmQyX25jZTIyX3NjMzA=",
              "parent_item_id": "Q291cnNlTGlzdDovbmQyX25jZTIyX3NjMzA=",
              "descriptor": {
                "name": "Biology XI Part-I",
                "long_desc": "",
                "images": [
                  {
                    "url": "https://storage.googleapis.com/swayam-node2-production.appspot.com/assets/img/nce22_sc30/biology01.jpg"
                  }
                ]
              },
              "price": {
                "currency": "INR",
                "value": "0"
              },
              "category_id": "SCHOOL",
              "recommended": false,
              "time": {
                "label": "Course Schedule",
                "duration": "P24W",
                "range": {
                  "start": "2022-11-01T18:29:00.000000Z",
                  "end": "2023-03-31T18:29:00.000000Z"
                }
              },
              "rating": "1",
              "tags": [
                {
                  "descriptor": {
                    "name": "courseInfo"
                  },
                  "list": [
                    {
                      "descriptor": {
                        "name": "credits"
                      },
                      "value": "null"
                    },
                    {
                      "descriptor": {
                        "name": "instructors"
                      },
                      "value": "Dr. Pushpalatha"
                    },
                    {
                      "descriptor": {
                        "name": "offeringInstitue"
                      },
                      "value": "DESM, NCERT, New Delhi"
                    },
                    {
                      "descriptor": {
                        "name": "url"
                      },
                      "value": "https://onlinecourses.swayam2.ac.in/nce22_sc30/preview"
                    },
                    {
                      "decsriptor": {
                        "name": "enrollmentEndDate"
                      },
                      "value": "2023-02-28T18:29:00.000000Z"
                    }
                  ],
                  "display": true
                }
              ],
              "rateable": false
            },
            {
              "id": "Q291cnNlTGlzdDovbmQyX25jZTIyX3NjMzE=",
              "parent_item_id": "Q291cnNlTGlzdDovbmQyX25jZTIyX3NjMzE=",
              "descriptor": {
                "name": "Biology XI Part-II",
                "long_desc": "",
                "images": [
                  {
                    "url": "https://storage.googleapis.com/swayam-node2-production.appspot.com/assets/img/nce22_sc31/biology02.jpg"
                  }
                ]
              },
              "price": {
                "currency": "INR",
                "value": "0"
              },
              "category_id": "SCHOOL",
              "recommended": false,
              "time": {
                "label": "Course Schedule",
                "duration": "P24W",
                "range": {
                  "start": "2022-11-01T18:29:00.000000Z",
                  "end": "2023-03-31T18:29:00.000000Z"
                }
              },
              "rating": "0",
              "tags": [
                {
                  "descriptor": {
                    "name": "courseInfo"
                  },
                  "list": [
                    {
                      "descriptor": {
                        "name": "credits"
                      },
                      "value": "null"
                    },
                    {
                      "descriptor": {
                        "name": "instructors"
                      },
                      "value": "Dr. Pushpalatha"
                    },
                    {
                      "descriptor": {
                        "name": "offeringInstitue"
                      },
                      "value": "DESM, NCERT, New Delhi"
                    },
                    {
                      "descriptor": {
                        "name": "url"
                      },
                      "value": "https://onlinecourses.swayam2.ac.in/nce22_sc31/preview"
                    },
                    {
                      "decsriptor": {
                        "name": "enrollmentEndDate"
                      },
                      "value": "2023-02-28T18:29:00.000000Z"
                    }
                  ],
                  "display": true
                }
              ],
              "rateable": false
            },
            {
              "id": "Q291cnNlTGlzdDovbmQyX25jZTIyX3NjMzI=",
              "parent_item_id": "Q291cnNlTGlzdDovbmQyX25jZTIyX3NjMzI=",
              "descriptor": {
                "name": "Biology XII Part-I",
                "long_desc": "",
                "images": [
                  {
                    "url": "https://storage.googleapis.com/swayam-node2-production.appspot.com/assets/img/nce22_sc32/Biology03copy2%20(1).jpg"
                  }
                ]
              },
              "price": {
                "currency": "INR",
                "value": "0"
              },
              "category_id": "SCHOOL",
              "recommended": false,
              "time": {
                "label": "Course Schedule",
                "duration": "P24W",
                "range": {
                  "start": "2022-11-01T18:29:00.000000Z",
                  "end": "2023-03-31T18:29:00.000000Z"
                }
              },
              "rating": "0",
              "tags": [
                {
                  "descriptor": {
                    "name": "courseInfo"
                  },
                  "list": [
                    {
                      "descriptor": {
                        "name": "credits"
                      },
                      "value": "null"
                    },
                    {
                      "descriptor": {
                        "name": "instructors"
                      },
                      "value": "Prof. Animesh Mohapatra"
                    },
                    {
                      "descriptor": {
                        "name": "offeringInstitue"
                      },
                      "value": "DESM, RIE, Bhubaneswar"
                    },
                    {
                      "descriptor": {
                        "name": "url"
                      },
                      "value": "https://onlinecourses.swayam2.ac.in/nce22_sc32/preview"
                    },
                    {
                      "decsriptor": {
                        "name": "enrollmentEndDate"
                      },
                      "value": "2023-02-28T18:29:00.000000Z"
                    }
                  ],
                  "display": true
                }
              ],
              "rateable": false
            },
            {
              "id": "Q291cnNlTGlzdDovbmQyX25jZTIyX3NjMzM=",
              "parent_item_id": "Q291cnNlTGlzdDovbmQyX25jZTIyX3NjMzM=",
              "descriptor": {
                "name": "Business Studies XI Part-I",
                "long_desc": "",
                "images": [
                  {
                    "url": "https://storage.googleapis.com/swayam-node2-production.appspot.com/assets/img/nce22_sc33/businessstudies01.jpg"
                  }
                ]
              },
              "price": {
                "currency": "INR",
                "value": "0"
              },
              "category_id": "SCHOOL",
              "recommended": false,
              "time": {
                "label": "Course Schedule",
                "duration": "P24W",
                "range": {
                  "start": "2022-11-01T18:29:00.000000Z",
                  "end": "2023-03-31T18:29:00.000000Z"
                }
              },
              "rating": "2",
              "tags": [
                {
                  "descriptor": {
                    "name": "courseInfo"
                  },
                  "list": [
                    {
                      "descriptor": {
                        "name": "credits"
                      },
                      "value": "null"
                    },
                    {
                      "descriptor": {
                        "name": "instructors"
                      },
                      "value": "Dr. Punnam Veeraiah"
                    },
                    {
                      "descriptor": {
                        "name": "offeringInstitue"
                      },
                      "value": "RIE Campus, Shyamla Hills, Bhopal"
                    },
                    {
                      "descriptor": {
                        "name": "url"
                      },
                      "value": "https://onlinecourses.swayam2.ac.in/nce22_sc33/preview"
                    },
                    {
                      "decsriptor": {
                        "name": "enrollmentEndDate"
                      },
                      "value": "2023-02-28T18:29:00.000000Z"
                    }
                  ],
                  "display": true
                }
              ],
              "rateable": false
            },
            {
              "id": "Q291cnNlTGlzdDovbmQyX25jZTIyX3NjMzQ=",
              "parent_item_id": "Q291cnNlTGlzdDovbmQyX25jZTIyX3NjMzQ=",
              "descriptor": {
                "name": "Business Studies XII Part-I",
                "long_desc": "",
                "images": [
                  {
                    "url": "https://storage.googleapis.com/swayam-node2-production.appspot.com/assets/img/nce22_sc34/businessstudies03.jpg"
                  }
                ]
              },
              "price": {
                "currency": "INR",
                "value": "0"
              },
              "category_id": "SCHOOL",
              "recommended": false,
              "time": {
                "label": "Course Schedule",
                "duration": "P24W",
                "range": {
                  "start": "2022-11-01T18:29:00.000000Z",
                  "end": "2023-03-31T18:29:00.000000Z"
                }
              },
              "rating": "0",
              "tags": [
                {
                  "descriptor": {
                    "name": "courseInfo"
                  },
                  "list": [
                    {
                      "descriptor": {
                        "name": "credits"
                      },
                      "value": "null"
                    },
                    {
                      "descriptor": {
                        "name": "instructors"
                      },
                      "value": "Dr. Punnam Veeraiah"
                    },
                    {
                      "descriptor": {
                        "name": "offeringInstitue"
                      },
                      "value": "RIE Campus, Shyamla Hills, Bhopal"
                    },
                    {
                      "descriptor": {
                        "name": "url"
                      },
                      "value": "https://onlinecourses.swayam2.ac.in/nce22_sc34/preview"
                    },
                    {
                      "decsriptor": {
                        "name": "enrollmentEndDate"
                      },
                      "value": "2023-02-28T18:29:00.000000Z"
                    }
                  ],
                  "display": true
                }
              ],
              "rateable": false
            },
            {
              "id": "Q291cnNlTGlzdDovbmQyX25jZTIyX3NjMzU=",
              "parent_item_id": "Q291cnNlTGlzdDovbmQyX25jZTIyX3NjMzU=",
              "descriptor": {
                "name": "Chemistry XI Part-I",
                "long_desc": "",
                "images": [
                  {
                    "url": "https://storage.googleapis.com/swayam-node2-production.appspot.com/assets/img/nce22_sc35/chemistry01-copy2.jpg"
                  }
                ]
              },
              "price": {
                "currency": "INR",
                "value": "0"
              },
              "category_id": "SCHOOL",
              "recommended": false,
              "time": {
                "label": "Course Schedule",
                "duration": "P24W",
                "range": {
                  "start": "2022-11-01T18:29:00.000000Z",
                  "end": "2023-03-31T18:29:00.000000Z"
                }
              },
              "rating": "0",
              "tags": [
                {
                  "descriptor": {
                    "name": "courseInfo"
                  },
                  "list": [
                    {
                      "descriptor": {
                        "name": "credits"
                      },
                      "value": "null"
                    },
                    {
                      "descriptor": {
                        "name": "instructors"
                      },
                      "value": "Prof R. K. Parashar"
                    },
                    {
                      "descriptor": {
                        "name": "offeringInstitue"
                      },
                      "value": "DESM, NIE, NCERT, New Delhi"
                    },
                    {
                      "descriptor": {
                        "name": "url"
                      },
                      "value": "https://onlinecourses.swayam2.ac.in/nce22_sc35/preview"
                    },
                    {
                      "decsriptor": {
                        "name": "enrollmentEndDate"
                      },
                      "value": "2023-02-28T18:29:00.000000Z"
                    }
                  ],
                  "display": true
                }
              ],
              "rateable": false
            },
            {
              "id": "Q291cnNlTGlzdDovbmQyX25jZTIyX3NjMzY=",
              "parent_item_id": "Q291cnNlTGlzdDovbmQyX25jZTIyX3NjMzY=",
              "descriptor": {
                "name": "Chemistry XI Part-II",
                "long_desc": "",
                "images": [
                  {
                    "url": "https://storage.googleapis.com/swayam-node2-production.appspot.com/assets/img/nce22_sc36/chemistry02.jpg"
                  }
                ]
              },
              "price": {
                "currency": "INR",
                "value": "0"
              },
              "category_id": "SCHOOL",
              "recommended": false,
              "time": {
                "label": "Course Schedule",
                "duration": "P24W",
                "range": {
                  "start": "2022-11-01T18:29:00.000000Z",
                  "end": "2023-03-31T18:29:00.000000Z"
                }
              },
              "rating": "5",
              "tags": [
                {
                  "descriptor": {
                    "name": "courseInfo"
                  },
                  "list": [
                    {
                      "descriptor": {
                        "name": "credits"
                      },
                      "value": "null"
                    },
                    {
                      "descriptor": {
                        "name": "instructors"
                      },
                      "value": "Prof. R.K. Parashar"
                    },
                    {
                      "descriptor": {
                        "name": "offeringInstitue"
                      },
                      "value": "DESM, NCERT, New Delhi"
                    },
                    {
                      "descriptor": {
                        "name": "url"
                      },
                      "value": "https://onlinecourses.swayam2.ac.in/nce22_sc36/preview"
                    },
                    {
                      "decsriptor": {
                        "name": "enrollmentEndDate"
                      },
                      "value": "2023-02-28T18:29:00.000000Z"
                    }
                  ],
                  "display": true
                }
              ],
              "rateable": false
            },
            {
              "id": "Q291cnNlTGlzdDovbmQyX25jZTIyX3NjMzc=",
              "parent_item_id": "Q291cnNlTGlzdDovbmQyX25jZTIyX3NjMzc=",
              "descriptor": {
                "name": "Chemistry XII Part-I",
                "long_desc": "",
                "images": [
                  {
                    "url": "https://storage.googleapis.com/swayam-node2-production.appspot.com/assets/img/nce22_sc37/chemistry03.jpg"
                  }
                ]
              },
              "price": {
                "currency": "INR",
                "value": "0"
              },
              "category_id": "SCHOOL",
              "recommended": false,
              "time": {
                "label": "Course Schedule",
                "duration": "P24W",
                "range": {
                  "start": "2022-11-01T18:29:00.000000Z",
                  "end": "2023-03-31T18:29:00.000000Z"
                }
              },
              "rating": "1",
              "tags": [
                {
                  "descriptor": {
                    "name": "courseInfo"
                  },
                  "list": [
                    {
                      "descriptor": {
                        "name": "credits"
                      },
                      "value": "null"
                    },
                    {
                      "descriptor": {
                        "name": "instructors"
                      },
                      "value": "Prof R. K. Parashar"
                    },
                    {
                      "descriptor": {
                        "name": "offeringInstitue"
                      },
                      "value": "DESM, NIE, NCERT, New Delhi"
                    },
                    {
                      "descriptor": {
                        "name": "url"
                      },
                      "value": "https://onlinecourses.swayam2.ac.in/nce22_sc37/preview"
                    },
                    {
                      "decsriptor": {
                        "name": "enrollmentEndDate"
                      },
                      "value": "2023-02-28T18:29:00.000000Z"
                    }
                  ],
                  "display": true
                }
              ],
              "rateable": false
            },
            {
              "id": "Q291cnNlTGlzdDovbmQyX25jZTIyX3NjMzg=",
              "parent_item_id": "Q291cnNlTGlzdDovbmQyX25jZTIyX3NjMzg=",
              "descriptor": {
                "name": "Economic XI Part-I",
                "long_desc": "",
                "images": [
                  {
                    "url": "https://storage.googleapis.com/swayam-node2-production.appspot.com/assets/img/nce22_sc38/economics01.jpg"
                  }
                ]
              },
              "price": {
                "currency": "INR",
                "value": "0"
              },
              "category_id": "SCHOOL",
              "recommended": false,
              "time": {
                "label": "Course Schedule",
                "duration": "P24W",
                "range": {
                  "start": "2022-11-01T18:29:00.000000Z",
                  "end": "2023-03-31T18:29:00.000000Z"
                }
              },
              "rating": "1",
              "tags": [
                {
                  "descriptor": {
                    "name": "courseInfo"
                  },
                  "list": [
                    {
                      "descriptor": {
                        "name": "credits"
                      },
                      "value": "null"
                    },
                    {
                      "descriptor": {
                        "name": "instructors"
                      },
                      "value": "Dr. Neeraja Rashmi"
                    },
                    {
                      "descriptor": {
                        "name": "offeringInstitue"
                      },
                      "value": "DESS, NCERT, New Delhi"
                    },
                    {
                      "descriptor": {
                        "name": "url"
                      },
                      "value": "https://onlinecourses.swayam2.ac.in/nce22_sc38/preview"
                    },
                    {
                      "decsriptor": {
                        "name": "enrollmentEndDate"
                      },
                      "value": "2023-02-28T18:29:00.000000Z"
                    }
                  ],
                  "display": true
                }
              ],
              "rateable": false
            }
          ]
        }
      ]
    }
  }
}
```

## 2 Enrolling into a course

This section provides recommendations for implementing the APIs related to placing an order or enrolling into a course.

### 2.1 Recommendations for BPPs

#### 2.1.1 Selecting a course from the catalog

- REQUIRED. The BPP MUST implement the `select` endpoint on the url specified in URL specified in the `context.bpp_uri` field sent during `on_search`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry
- REQUIRED. The BPP MUST check for a form submission at the URL specified on the `xinput.form.url` before acknowledging a `select` request.
- REQUIRED. If the course provider has successfully received the information submitted by the course consumer, the BPP must return an acknowledgement with `ack.status` set to `ACK` in response to the `select` request
- REQUIRED. If the course provider has returned a successful acknowledgement to a `select` request, it MUST send the offer encapsulated in an `Order` object

#### 2.1.2 Initializing an order for a course

- REQUIRED. The BPP MUST implement the `init` endpoint on the url specified in URL specified in the `context.bpp_uri` field sent during `on_search`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry
- REQUIRED. The BPP MUST check for a form submission at the URL specified on the `xinput.form.url` before acknowledging a `init` request.
- REQUIRED. If the course provider has successfully received the information submitted by the course consumer, the BPP must return an acknowledgement with `ack.status` set to `ACK` in response to the `init` request

#### 2.1.3 Confirming the order for the course

- REQUIRED. The BPP MUST implement the `confirm` endpoint on the url specified in URL specified in the `context.bpp_uri` field sent during `on_search`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry
- REQUIRED. The BPP MUST check for a form submission at the URL specified on the `xinput.form.url` before acknowledging a `confirm` request.
- REQUIRED. If the course provider has successfully received the information submitted by the course consumer, the BPP must return an acknowledgement with `ack.status` set to `ACK` in response to the `confirm` request

### 2.2 Recommendations for BAPs

#### 2.2.1 Selecting a course from the catalog

#### 2.2.2 Initializing an application for a course

#### 2.2.3 Confirming the application for the course

### 2.3 Example Workflow

The BAP shows a list of courses provided by the BPPs, the user chooses courses from the list they want to enroll in. The BPP prompts the user's personal details to create a new user (if not an existing user) for enrolling in this course.

The user fills in his details and agrees to the terms and conditions, order policies and confirms their enrollment for the course. The user then receives an enrollment ID against the selected course(s) along with the course details.

### 2.4 Example Requests

## 3 Fulfillment of Courses

This section contains recommendations for implementing the APIs related to fulfilling a course order

### 3.1 Recommendations for BPPs

#### 3.1.1 Sending status updates

- REQUIRED. The BPP MUST implement the `status` endpoint on the url specified in URL specified in the `context.bpp_uri` field sent during `on_search`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

#### 3.1.2 Updating an application for course

- REQUIRED. The BPP MUST implement the `update` endpoint on the url specified in URL specified in the `context.bpp_uri` field sent during `on_search`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

#### 3.1.3 Cancelling a course application

- REQUIRED. The BPP MUST implement the `cancel` endpoint on the url specified in URL specified in the `context.bpp_uri` field sent during `on_search`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry
- REQUIRED. The BPP MUST implement the `get_cancellation_reasons` endpoint on the url specified in URL specified in the `context.bpp_uri` field sent during `on_search`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

#### 3.1.4 Real-time tracking

- REQUIRED. The BPP MUST implement the `track` endpoint on the url specified in URL specified in the `context.bpp_uri` field sent during `on_search`. In case of permissioned networks, this URL MUST match the `Subscriber.url` present on the respective entry in the Network Registry

### 3.2 Recommendations for BAPs

#### 3.2.1 Sending status updates

#### 3.2.2 Updating an order for course

#### 3.2.3 Cancelling a course order

#### 3.2.4 Real-time tracking

### 3.3 Example Workflow

### 3.4 Example Requests
