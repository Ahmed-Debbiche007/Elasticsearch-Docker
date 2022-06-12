# Elastic stack (ELK) on Docker

Run the latest version of the [Elastic stack][elk-stack] with Docker and Docker Compose.

It gives you the ability to analyze any data set by using the searching/aggregation capabilities of Elasticsearch and
the visualization power of Kibana.

### Bringing up the stack

Clone this repository onto the Docker host that will run the stack, then start the stack's services locally using Docker
Compose:

```console
$ docker-compose up
```

> **Note**  
> You can also run all services in the background (detached mode) by appending the `-d` flag to the above command.

Give Kibana about a minute to initialize, then access the Kibana web UI by opening <http://localhost:5601> in a web
browser and use the following (default) credentials to log in:

* user: *elastic*
* password: *changeme*

### Initial setup

#### Setting up user authentication

The _"changeme"_ password set by default for all aforementioned users is **unsecure**. For increased security, we will
reset the passwords of all aforementioned Elasticsearch users to random secrets.

1. Reset passwords for default users

    The commands below resets the passwords of the `elastic` and `kibana_system` users. Take note
    of them.

    ```console
    $ docker-compose exec elasticsearch bin/elasticsearch-reset-password --batch --user elastic
    ```

    ```console
    $ docker-compose exec elasticsearch bin/elasticsearch-reset-password --batch --user kibana_system
    ```


1. Replace usernames and passwords in configuration files

    Replace the password of the `elastic` user inside the `.env` file with the password generated in the previous step.
    Its value isn't used by any core component, but [extensions](#how-to-enable-the-provided-extensions) use it to
    connect to Elasticsearch.

   
1. Restart and Kibana to re-connect to Elasticsearch using the new passwords

    ```console
    $ docker-compose up -d kibana
    ```
    
### Cleanup

Elasticsearch data is persisted inside a volume by default.

In order to entirely shutdown the stack and remove all persisted data, use the following Docker Compose command:

```console
$ docker-compose down -v
```

> **Note**  
> The files provided in this repositry are configured to run synthetic monitoring via heartbeat
