# Kong plugin template

_Forked from *[kong-plugin github repository](https://github.com/Kong/kong-plugin)*_

This repository contains a very simple Kong plugin template to get you
up and running quickly using **Docker** for developing your own plugins.

## Renaming the plugin

To change the current plugin name, use the rename.sh script

```bash
chmod +x ./rename.sh && ./rename.sh current_plugin_name new_plugin_name [new_plugin_version]
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
    -c "chmod +x ./rename.sh && ./rename.sh current_plugin_name new_plugin_name [new_plugin_version]"
```

## Preparation

```bash
docker-compose up -d postgres && docker-compose logs -f postgres
# wait until it's ready and press Ctrl+C
docker-compose up migrator
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