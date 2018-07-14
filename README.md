# TeamCity-Configuration
Javacord's configuations for the TeamCity build server

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

#### Start or update the stack

```shell
docker stack deploy --prune -c javacord-docker-stack.yml javacord-stack
```
