# Elastic stack (ELK) on Docker

Run the latest version of the [Elastic stack][elk-stack] with Docker and Docker Compose.

It gives you the ability to analyze any data set by using the searching/aggregation capabilities of Elasticsearch and
the visualization power of Kibana.

![Animated demo](https://user-images.githubusercontent.com/3299086/155972072-0c89d6db-707a-47a1-818b-5f976565f95a.gif)



## Usage

> **Warning**  
> You must rebuild the stack images with `docker-compose build` whenever you switch branch or update the
> [version](#version-selection) of an already existing stack.

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

> **Note**  
> Upon the initial startup, the `elastic`, `logstash_internal` and `kibana_system` Elasticsearch users are intialized
> with the values of the passwords defined in the [`.env`](.env) file (_"changeme"_ by default). The first one is the
> [built-in superuser][builtin-users], the other two are used by Kibana and Logstash respectively to communicate with
> Elasticsearch. This task is only performed during the _initial_ startup of the stack. To change users' passwords
> _after_ they have been initialized, please refer to the instructions in the next section.

### Initial setup

#### Setting up user authentication

> **Note**  
> Refer to [Security settings in Elasticsearch][es-security] to disable authentication.

> **Warning**  
> Starting with Elastic v8.0.0, it is no longer possible to run Kibana using the bootstraped privileged `elastic` user.

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

    If the need for it arises (e.g. if you want to [collect monitoring information][ls-monitoring] through Beats and
    other components), feel free to repeat this operation at any time for the rest of the [built-in
    users][builtin-users].

1. Replace usernames and passwords in configuration files

    Replace the password of the `elastic` user inside the `.env` file with the password generated in the previous step.
    Its value isn't used by any core component, but [extensions](#how-to-enable-the-provided-extensions) use it to
    connect to Elasticsearch.

   
1. Restart and Kibana to re-connect to Elasticsearch using the new passwords

    ```console
    $ docker-compose up -d kibana
    ```

> **Note**  
> Learn more about the security of the Elastic stack at [Secure the Elastic Stack][sec-cluster].


### Cleanup

Elasticsearch data is persisted inside a volume by default.

In order to entirely shutdown the stack and remove all persisted data, use the following Docker Compose command:

```console
$ docker-compose down -v
```

> **Note**  
> The files provided in this repositry are configured to run synthetic monitoring via heartbeat
