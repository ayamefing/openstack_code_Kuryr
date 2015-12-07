# settings

定义一些环境变量。

```sh
KURYR_HOME=${KURYR_HOME:-$DEST/kuryr}
KURYR_JSON_FILENAME=kuryr.json
KURYR_DEFAULT_JSON=${KURYR_HOME}/etc/${KURYR_JSON_FILENAME}

# See libnetwork's plugin discovery mechanism:
#   https://github.com/docker/docker/blob/c4d45b6a29a91f2fb5d7a51ac36572f2a9b295c6/docs/extend/plugin_api.md#plugin-discovery
KURYR_JSON_DIR=${KURYR_JSON_DIR:-/usr/lib/docker/plugins/kuryr}
KURYR_JSON=${KURYR_JSON_DIR}/${KURYR_JSON_FILENAME}
```