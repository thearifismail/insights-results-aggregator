---
layout: page
nav_order: 2
---
# Configuration
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

Configuration is done by toml config, default one is `config.toml` in working directory,
but it can be overwritten by `INSIGHTS_RESULTS_AGGREGATOR_CONFIG_FILE` env var.

Also each key in config can be overwritten by corresponding env var. For example if you have config

```toml
[storage]
db_driver = "sqlite3"
sqlite_datasource = "./aggregator.db"
pg_username = "user"
pg_password = "password"
pg_host = "localhost"
pg_port = 5432
pg_db_name = "aggregator"
pg_params = ""
```

and environment variables

```shell
INSIGHTS_RESULTS_AGGREGATOR__STORAGE__DB_DRIVER="postgres"
INSIGHTS_RESULTS_AGGREGATOR__STORAGE__PG_PASSWORD="your secret password"
```

the actual driver will be postgres with password "your secret password"

It's very useful for deploying docker containers and keeping some of your configuration
outside of main config file(like passwords).

## Broker configuration

Broker configuration is in section `[broker]` in config file

```toml
[broker]
address = "localhost:9092"
topic = "topic"
payload_tracker_topic = "payload-tracker-topic"
service_name = "insights-results-aggregator"
group = "aggregator"
enabled = true
save_offset = true
```

* `address` is an address of kafka broker (DEFAULT: "")
* `topic` is a topic to consume messages from (DEFAULT: "")
* `payload_tracker_topic` is a topic to which messages for the Payload Tracker are published (see `producer` package) (DEFAULT: "")
* `service_name` is the name of this service as reported to the Payload Tracker (DEFAULT: "")
* `group` is a kafka group (DEFAULT: "")
* `enabled` is an option to turn broker on (DEFAULT: false)
* `save_offset` is an option to turn on saving offset of successfully consumed messages.
The offset is stored in the same kafka broker. If it turned off,
consuming will be started from the most recent message (DEFAULT: false)

Option names in env configuration:

* `address` - INSIGHTS_RESULTS_AGGREGATOR__BROKER__ADDRESS
* `topic` - INSIGHTS_RESULTS_AGGREGATOR__BROKER__TOPIC
* `payload_tracker_topic` - INSIGHTS_RESULTS_AGGREGATOR__BROKER__PAYLOAD_TRACKER_TOPIC
* `service_name` - INSIGHTS_RESULTS_AGGREGATOR__BROKER__SERVICE_NAME
* `group` - INSIGHTS_RESULTS_AGGREGATOR__BROKER__GROUP
* `enabled` - INSIGHTS_RESULTS_AGGREGATOR__BROKER__ENABLED
* `save_offset` - INSIGHTS_RESULTS_AGGREGATOR__BROKER__SAVE_OFFSET

## Server configuration

Server configuration is in section `[server]` in config file.

```toml
[server]
address = ":8080"
api_prefix = "/api/v1/"
api_spec_file = "openapi.json"
debug = true
auth = true
auth_type = "xrh"
use_https = true
enable_cors = true
```

* `address` is host and port which server should listen to
* `api_prefix` is prefix for RestAPI path
* `api_spec_file` is the location of a required OpenAPI specifications file
* `debug` is developer mode that enables some special API endpoints not used on production. In
production, `false` is used every time.
* `auth` turns on or turns authentication. Please note that this option can be set to `false` only
in devel environment. In production, `true` is used every time.
* `auth_type` set type of auth, it means which header to use for auth `x-rh-identity` or
`Authorization`. Can be used only with `auth = true`. Possible options: `jwt`, `xrh`
* `use_https` is option to turn on TLS server. Please note that this option can be set to `false`
only in devel environment. In production, `true` is used every time.
* `enable_cors` is option to turn on CORS header, that allows to connect from different hosts
(**don't use it in production**)

Please note that if `auth` configuration option is turned off, not all REST API endpoints will be
usable. Whole REST API schema is satisfied only for `auth = true`.
