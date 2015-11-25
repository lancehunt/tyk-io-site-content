+++
draft = false
title = "Key Management"
date = 2014-07-29T10:58:44Z
[menu.sidebar_v1_9]
    parent = "rest"
    weight = -100
+++

### Create Keys

Create keys will generate a new key - Tyk wil generate the access token based on the OrgID specified in the API Definition and a
random UUID. This ensures that keys can be "owned" by different API Owners should segmentation be needed at an organisational level.

API keys without access_rights data will be written to _all_ API's on the system (this also means that they will be created
across all SessionHandlers and StorageHandlers, it is recommended to always embed access_rights data in a key to ensure that only targeted API's and their
back-ends are written to.

For smaller API's and implementations using the default Redis back end will not be unduly affected by this.


|   **Property**    |   **Description**     |
|   -----------     |   ---------------     |
|   Resource URL    |   `/tyk/keys/create`  |
|   Method          |   POST                |
|   Type            |   JSON                |
|   Body            |   Session Object      |

#### Options

Adding the `suppress_reset` parameter and setting it to `1`, will cause Tyk to *not* reset the quota limit that is in the current live quota manager. By default Tyk will reset the quota in the live quota manager (initialising it) when ADDing a key. Adding the `suppress_reset` flag to the URL parameters will avoid this behaviour.

#### Sample Request

    POST /tyk/keys/create HTTP/1.1
    Host: localhost:5000
    x-tyk-authorization: 352d20ee67be67f6340b4c0605b044bc4
    Cache-Control: no-cache

{{% content link="/v1.8/object_examples/session_key/" view="fragment" %}}

#### Sample response

    {
        "key": "53ac07777cbb8c2d53000002140b44ebc86f4e99644412a8ea8a344d",
        "status": "ok",
        "action": "create"
    }

### Add/Update Keys

You can also manually add keys to Tyk using your own key-generation algorithm. it is recommended if using this approach to ensure that the
`OrgID` being used in the API Definition and the key data is blank so that Tyk does not try to prepend or manage the key in any way.

|   **Property**    |   **Description**                 |
|   -----------     |   ---------------                 |
|   Resource URL    |   `/tyk/keys/{{access_token}}`    |
|   Method          |   POST or PUT                     |
|   Type            |   JSON                            |
|   Body            |   Session Object                  |

#### Options

Adding the `suppress_reset` parameter and setting it to `1`, will cause Tyk to *not* reset the quota limit that is in the current live quota manager. By default Tyk will reset the quota in the live quota manager (initialising it) when Adding or updating a key. Adding the `suppress_reset` flag to the URL parameters will avoid this behaviour.

#### Sample Request

    PUT /tyk/keys/sample-key-b3da0730-1d5a-11e4-8c21-0800200c9a66 HTTP/1.1
    Host: localhost:5000
    x-tyk-authorization: 352d20ee67be67f6340b4c0605b044bc4
    Cache-Control: no-cache

    {
        "allowance": 999,
        "rate": 1000,
        "per": 60,
        "expires": 0,
        "quota_max": -1,
        "quota_renews": 1406121006,
        "quota_remaining": 0,
        "quota_renewal_rate": 60,
        "access_rights": {
            "234a71b4c2274e5a57610fe48cdedf40": {
                "api_name": "Versioned API",
                "api_id": "234a71b4c2274e5a57610fe48cdedf40",
                "versions": [
                    "v1"
                ]
            }
        },
        "org_id": ""
    }

#### Sample response

    {
        "key": "sample-key-b3da0730-1d5a-11e4-8c21-0800200c9a66",
        "status": "ok",
        "action": "modified"
    }

### Delete Key

Deleting a key will remove it permanently from the system, however analytics relating to that key will still be available.

|   **Property**    |   **Description**                 |
|   -----------     |   ---------------                 |
|   Resource URL    |   `/tyk/keys/{{access_token}}`    |
|   Method          |   DELETE                          |
|   Type            |   None                            |
|   Body            |   None                            |
|   Params          |   api_id={api_id}                 |


#### Sample Request

    DELETE /tyk/keys/sample-key-b3da0730-1d5a-11e4-8c21-0800200c9a66?api_id=90d2416f4710453663bcc376265f886e HTTP/1.1
    Host: localhost:5000
    x-tyk-authorization: 352d20ee67be67f6340b4c0605b044bc4
    Cache-Control: no-cache

#### Sample response

    {
        "key": "sample-key-b3da0730-1d5a-11e4-8c21-0800200c9a66",
        "status": "ok",
        "action": "deleted"
    }

### List Keys

You can retrieve all the keys in your Tyk instance

|   **Property**    |   **Description**                 |
|   -----------     |   ---------------                 |
|   Resource URL    |   `/tyk/keys/`                    |
|   Method          |   GET                             |
|   Type            |   None                            |
|   Body            |   None                            |
|   Params          |   api_id={api_id}                 |

#### Sample Request

    GET /tyk/keys/?api_id=90d2416f4710453663bcc376265f886e HTTP/1.1
    Host: localhost:5000
    x-tyk-authorization: 352d20ee67be67f6340b4c0605b044bc4
    Cache-Control: no-cache

#### Sample Response

    {
        "keys": [
            "53ac07777cbb8c2d53000002c29ebe0faf6540e0673d6af76b270088",
            "53ac07777cbb8c2d530000027776e9f910e94cd9552c22c908d2d081",
            "53ac07777cbb8c2d53000002d698728ce964432d7167596bc005c5fc",
            "53ac07777cbb8c2d530000028210d848c5854cb35917b2f013529d95"
        ]
    }
