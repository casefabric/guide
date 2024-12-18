---
id: configuration
title: Cafienne Engine Configuration
sidebar_label: Configuration
---

# Introduction

This page gives an overview of the various configuration options of the Cafienne Engine.
<br/>
Cafienne Engine runs on the Pekko actor system and is typically hosted in a configuration of Docker containers.


## `docker-compose.yml, local.conf & environment variables`

Both Docker and Pekko have a wide range of configuration possibilities.
- Docker configuration makes use of the [**`YAML`**](https://en.wikipedia.org/wiki/YAML) format
- Pekko makes use of the [**`HOCON`**](https://pekko.apache.org/docs/pekko/1.1/general/configuration.html) format 

Both languages also support the use of environment variables.
<br/>An example configuration that combines all these options can be found in the Cafienne Demo environment of the [getting-started ](https://github.com/cafienne/getting-started) repository in GitHub.

## Various persistence configurations

The Cafienne Engine requires two types of storage, based on the [CQRS principle](https://en.wikipedia.org/wiki/Command_Query_Responsibility_Segregation).
The configuration for the event journal is done through that standard pekko persistence configuration.

| &nbsp;&nbsp; storage |  |
|----------|-------------|
| **Event Journal** | Events that are generated during the lifecycle of a case are stored in the Event Journal. |
| **Query DB** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | In order to have fast retrieval of case information, projections of events are stored in the Query database. |


> **Note: include the following configuration when using Microsoft SQL Server as event journal** 
> <br/>This setting is required to avoid full table scans while inserting events.
>
> **`url = "jdbc:sqlserver://...;DatabaseName=event-journal;sendStringParametersAsUnicode=false;`**
>

- [Example configuration for Cassandra](assets/engine/configuration/cassandra.conf)
  <br/> The Cassandra configuration uses Cassandra as Event Journal, and PostgreSQL as Query DB.
- [Example configuration for JDBC](assets/engine/configuration/jdbc.conf)

## Cafienne specific settings

Cafienne Engine specific settings can be added to the Pekko configuration file. These settings include the following:

```yml
cafienne {
  # Engine wide platform settings
  platform {
    # Platform has owners that are allowed to create/disable/enable tenants
    #  This property specifies the set of user-id's that are owners
    #  This array may not be empty.
    owners = ["admin"]
    owners = ${?CAFIENNE_PLATFORM_OWNERS}
    # Default tenant will be used when a user does not provide the tenant as a parameter to
    #  the API call (e.g. in StartCase). When the user is member of only one tenant,
    #  then that tenant will be submitted along with the StartCase command.
    #  If the user belongs to multiple tenants, then this default-tenant option will be passed.
    default-tenant = "world"
    default-tenant = ${?CAFIENNE_PLATFORM_DEFAULT_TENANT}
    # bootstrap-tenants holds a reference to zero or more json (or yaml) files that have default tenant information.
    #  Each file is should contain information for one tenant (tenant name, users and owners).
    #  During launch of the case engine, the files will be scanned and a CreateTenant command is sent
    #  into the system, thereby setting up one or more default tenants.
    # If the bootstrap-tenants property is not filled and the default-tenant has a value, the system will search for
    #  a file that holds the default tenant name plus either a .conf, .json, .yml or .yaml extension.
    #  E.g. in case default-tenant = 'world', the system would search for existence in the following order:
    #  - 'world.conf'
    #  - 'world.json'
    #  - 'world.yml'
    #  - 'world.yaml'
    # If none of these files are found, the bootstrap attempt will be skipped.
    bootstrap-tenants = ["world.conf"]
  }

  engine {
    # Properties for sending tasks of type Mail
    mail-service {
      # Here you can fill any regular jakarta.mail properties
      #  All properties mentioned here are passed into the connection with the mail server
      mail.host = mailcatcher
      mail.smtp.port = 1025
    }
    # more settings can be found in the getting-started configuration
  }

  api {
    bindhost = "localhost"
    bindport = 2027

    security {
      ###############################################################################
      ##  Below settings can be used to configure multiple OIDC services           ##
      ##   that ships with the docker images in the cafienne repository named      ##
      ##   getting-started.                                                        ##
      ###############################################################################
      # configuration settings for OpenID Connect
      oidc = [{
        ### This one has dynamic resolution (only a issuer needs to be set)
        issuer = "http://localhost:5556/dex"
      }, {
        ### This one seems to have dynamic resolution ...
        ###   but since the key-url is filled, that will take precedence (at this moment)
        connect-url = "http://localhost:2377"
        token-url = "http://localhost:2377/token"
        key-url = "http://localhost:2377/keys"
        authorization-url = "http://localhost:2377/auth"
        issuer = "http://localhost:2377"
      }, {
        ### This one will be skipped, unless the environment variables are filled.
        connect-url = ${?CAFIENNE_OIDC_CONNECT_URL}
        token-url = ${?CAFIENNE_OIDC_TOKEN_URL}
        key-url = ${?CAFIENNE_OIDC_KEY_URL}
        authorization-url = ${?CAFIENNE_OIDC_AUTHORIZATION_URL}
        issuer = ${?CAFIENNE_OIDC_ISSUER}
      }]
  }

  definitions {
    # The case engine supports various ways to list, load and deploy case definitions.
  }

  actor {
    # the seconds of wait time before a response to a command is expected
    # by the pekko http command routes
    ask-timeout = 60

    # the seconds of idle time after which a case actor is removed from pekko memory
    # if the case has not received new commands after the specified number of seconds,
    # the case engine will ask pekko to remove the case from memory to avoid memory leaks.
    idle-period = 600
  }

  # This setting tells cafienne which journal to use for reading events.
  #  If omitted, cafienne will try to guess the read journal, based on the pekko settings
  read-journal = "jdbc-read-journal"

  query-db {
    profile = ${?QUERY_DB_PROFILE}
    db {
      driver =  ${?QUERY_DB_DRIVER}
      user =  ${?QUERY_DB_USER}
      password =  ${?QUERY_DB_PASSWORD}
      numThreads = 10
      connectionTimeout = 5000
      validationTimeout = 5000

      url =  ${?QUERY_DB_URL}
    }

    # Configuration options handling exceptions that may occur while reading
    #  the event streams that populate the query-db tables
    #  See also https://pekko.apache.org/docs/pekko/1.0/stream/stream-error.html#delayed-restarts-with-a-backoff-operator
    restart-stream {
      min-back-off = 500ms
      max-back-off = 30s
      random-factor = 0.20
      max-restarts = 20
      max-restarts-within = 5m
    }
  }
}
```
