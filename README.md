# Javacord Infrastructure

The configuration for Javacord's infrastructure.

## Contents

### `.teamcity`

The TeamCity project configuration using the "Versioned Settings" feature of TeamCity.

***WARNING:*** Changing anything here and pushing the changes will also push these
configuration changes to TeamCity and be applied there.

### `javacord-docker-stack.yml`

The Javacord stack of servers run in docker containers on the CI server.

#### Requirements

* `shields` image built from https://github.com/badges/shields
* `javadoc_proxy` image built from https://github.com/Javacord/Javadoc-Proxy
* `/home/teamcity/shields.env` which sets these environment properties for the shields:
  * `GH_CLIENT_ID`
  * `GH_CLIENT_SECRET`
  * `GH_TOKEN`
  * `SHIELDS_IP`
* `openssl rand -base64 20 | docker secret create mysql_root_password -`
* `openssl rand -base64 20 | docker secret create mysql_teamcity_password -`

#### Start or update the stack

```shell
docker stack deploy --prune -c javacord-docker-stack.yml javacord-stack
```

### `javacord-build`

A `Dockerfile` for building the image in which the actual Javacord build is running.

#### Build the image

```shell
docker build -t javacord-build javacord-build/
```

### `teamcity-agent`

A `Dockerfile` for building the TeamCity agent images tailored to our needs.

#### Build the images

```shell
docker build --build-arg i=1 -t teamcity-agent-1 teamcity-agent/ && \
docker build --build-arg i=2 -t teamcity-agent-2 teamcity-agent/ && \
docker build --build-arg i=3 -t teamcity-agent-3 teamcity-agent/
```

### `javacord-bot-build`

A `Dockerfile` for building the image in which the actual Javacord Bot build is running.

#### Build the image

```shell
docker build -t javacord-bot-build javacord-bot-build/
```
