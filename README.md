# Kong plugin template

_Forked from *[kong-plugin github repository](https://github.com/Kong/kong-plugin)*_

This repository contains a very simple Kong plugin template to get you
up and running quickly using **Docker** for developing your own plugins.

## Renaming the plugin

To change the current plugin name, use the rename.sh script. Note that the default current plugin name of this repository is `myplugin`

```bash
chmod +x ./rename.sh && ./rename.sh <current_plugin_name> <new_plugin_name> [<new_plugin_version>]
```

If you are not on linux or somehow not able to run this script, utilize docker and run this script:

```bash
docker run \
    -it \
    --rm \
    -v $PWD:/tmp/rename \
    -w /tmp/rename \
    --entrypoint /bin/bash \
    ubuntu:16.04 \
    -c "chmod +x ./rename.sh && ./rename.sh <current_plugin_name> <new_plugin_name> [<new_plugin_version>]"
```

## <a name="preparation">Preparation</a>

The following command should be run manually once before using `kong` or `kong_busted` service. Note that after `postgres` service have been started successfuly (by examining the output of *docker-compose logs postgres*), press **ctrl+c** to exit from *docker-compose logs postgres* and start the `migrator`

```bash
docker-compose up -d postgres && \
docker-compose logs -f postgres; \
docker-compose up migrator
```

## Runing kong service

the following is an example how to run the `kong` service, add an api that point to mockbin.org, and invoke the api

```bash
docker-compose up --build -d kong && docker-compose logs -f kong

# add mockbin API
curl -i -X POST \
  --url http://localhost:8001/apis/ \
  --data 'name=mockbin' \
  --data 'upstream_url=http://mockbin.org/request' \
  --data 'uris=/'

# add mobdebug plugin
curl -i -X POST \
  --url http://localhost:8001/apis/mockbin/plugins/ \
  --data 'name=mobdebug'

# add your plugin


# try the mockbin API
curl -i http://localhost:8000
```

## Debugging via zerobrane

*Assuming you mount **./volumes/kong/usr/local/share/lua/5.1** into **$KONG_LUA_PATH/$KONG_LUA_VERSION** container path*

1. Click "Project" > "Project Directory" > "choose" and poin the project directory to ***./volumes/kong/usr/local/share/lua/5.1***.

1. Click "Project" > "Start Debugger Server"

1. Invoke the mockbin API

## Testing with busted

1. specify the right config for `kong_dev_net` docker network to prevent conflit with your existing docker network. If subnet must be changed, than service configuration under the following section must also be changed

    ```yaml
    networks:
      kong_dev_net:
        ipv4_address:
    ```

1. to start your plugin test after completing the step from [Preparation](#preparation), run:

    ```bash
    docker-compose up kong_busted
    ```