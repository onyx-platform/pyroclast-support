---
title: Pyroclast API
mediaroot: media
markdown2extras: wiki-tables
---

# Pyroclast API

HTTP API for deployed Pyroclast services.

### All API calls require basic authentication

<pre class="base">
curl -u "[user id]:[api token]"
</pre>

### Format

All responses are formatted in **JSON**.

# Pyroclast Deployed Services

## GET /svc/:service-id

Returns information about all materialized views for this service.

#### example request

    $ curl -u "[user id]:[api key]" "https://us-east-1.pyroclast.io/svc/kzPgyNWQYJRVdr"

#### parameters

|| **name**   || **in** || **required** || **type** || **description**                       ||
|| service-id || path   || true         || string   || Identifier for this deployed service. ||
|| start      || query  || false        || integer  || Exclude all aggregates whose timestamp ending boundary falls before the specified time. This value is in milliseconds since the Unix epoch. ||
|| end        || query  || false        || integer  || Exclude all aggregates whose timestamp starting boundary falls after the specified time. This value is in milliseconds since the Unix epoch. ||
|| sort       || query  || false        || boolean  || When true, sorts aggregate results in ascending order according to the timestamp starting bound. ||
|| datetime-format || query || false || string || The format for datetime instances. (Options: `"iso-8601"`) ||

#### response

    {
        "trending-hashtags": {
            "contents": {
                "#yolo": [
                    {
                        "bounds": [
                            1493856000000,
                            1493942399999
                        ],
                        "value": 259
                    }
                ],
                "#pyroclast": [
                    {
                        "bounds": [
                            1493856000000,
                            1493942399999
                        ],
                        "value": 121355146
                    },
                    {
                        "bounds": [
                            1493942400000,
                            1494028799999
                        ],
                        "value": 110717456
                    }
                ]
            },
            "grouped?": true,
            "id": "a21715fb-4662-4a41-9740-950d5c84b65b",
            "type": "fixed"
        }
    }

## GET /svc/:service-id/:aggregate

Gets a specific aggregate by name from this service.

#### example request

    $ curl -u "[user id]:[api key]" "https://us-east-1.pyroclast.io/svc/kzPgyNWQYJRVdr/trending-hashtags"

#### parameters

|| **name**   || **in** || **required** || **type** || **description**                       ||
|| service-id || path   || true         || string   || Identifier for this deployed service. ||
|| start      || query  || false        || integer  || Exclude all aggregates whose timestamp ending boundary falls before the specified time. This value is in milliseconds since the Unix epoch. ||
|| end        || query  || false        || integer  || Exclude all aggregates whose timestamp starting boundary falls after the specified time. This value is in milliseconds since the Unix epoch. ||
|| sort       || query  || false        || boolean  || When true, sorts aggregate results in ascending order according to the timestamp starting bound. ||
|| datetime-format || query || false || string || The format for datetime instances. (Options: `"iso-8601"`) ||

#### response

    {
        "contents": {
            "#yolo": [
                {
                    "bounds": [
                        1493856000000,
                        1493942399999
                    ],
                    "value": 259
                }
            ],
            "#pyroclast": [
                {
                    "bounds": [
                        1493856000000,
                        1493942399999
                    ],
                    "value": 121355146
                },
                {
                    "bounds": [
                        1493942400000,
                        1494028799999
                    ],
                    "value": 110717456
                }
            ]
        },
        "grouped?": true,
        "id": "a21715fb-4662-4a41-9740-950d5c84b65b",
        "type": "fixed"
    }

## GET /svc/:aggregate/:group

Gets a specific aggregate by name from this service.

#### example request

    $ curl -u "[user id]:[api key]" "https://us-east-1.pyroclast.io/svc/kzPgyNWQYJRVdr/trending-hashtags/yolo"

#### parameters

|| **name**   || **in** || **required** || **type** || **description**                       ||
|| service-id || path   || true         || string   || Identifier for this deployed service. ||
|| start      || query  || false        || integer  || Exclude all aggregates whose timestamp ending boundary falls before the specified time. This value is in milliseconds since the Unix epoch.  |
|| end        || query  || false        || integer  || Exclude all aggregates whose timestamp starting boundary falls after the specified time. This value is in milliseconds since the Unix epoch. ||
|| sort       || query  || false        || boolean  || When true, sorts aggregate results in ascending order according to the timestamp starting bound. ||
|| datetime-format || query || false || string || The format for datetime instances. (Options: `"iso-8601"`) ||

#### response

    {
        "contents": [
            {
                "bounds": [
                    1493856000000,
                    1493942399999
                ],
                "value": 259
            }
        ],
        "grouped?": true,
        "id": "a21715fb-4662-4a41-9740-950d5c84b65b",
        "type": "fixed"
    }