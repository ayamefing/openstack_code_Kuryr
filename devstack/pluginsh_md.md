# plugin.sh

包括安装 etcd 和检测 kuryr 服务是否启用两个函数。

```sh
install_etcd_data_store {

    if [ ! -f "$DEST/etcd/etcd-$ETCD_VERSION-linux-amd64/etcd" ]; then
        echo "Installing etcd server"
        mkdir $DEST/etcd
        wget https://github.com/coreos/etcd/releases/download/$ETCD_VERSION/etcd-$ETCD_VERSION-linux-amd64.tar.gz -O $DEST/etcd/etcd-$ETCD_VERSION-linux-amd64.tar.gz
        tar xzvf $DEST/etcd/etcd-$ETCD_VERSION-linux-amd64.tar.gz -C $DEST/etcd
    fi
}

# main loop
if is_service_enabled kuryr; then
    if [[ "$1" == "stack" && "$2" == "install" ]]; then
        install_etcd_data_store
        setup_develop $KURYR_HOME

    elif [[ "$1" == "stack" && "$2" == "post-config" ]]; then

        if [[ ! -d "${KURYR_JSON_DIR}" ]]; then
            echo -n "${KURYR_JSON_DIR} directory is missing. Creating it... "
            sudo mkdir -p ${KURYR_JSON_DIR}
            echo "Done"
        fi

        if [[ ! -f "${KURYR_JSON}" ]]; then
             echo -n "${KURYR_JSON} is missing. Copying the default one... "
             sudo cp ${KURYR_DEFAULT_JSON} ${KURYR_JSON}
             echo "Done"
        fi

        run_process etcd-server "$DEST/etcd/etcd-$ETCD_VERSION-linux-amd64/etcd"

        wget http://get.docker.com -O install_docker.sh
        sudo chmod 777 install_docker.sh
        sudo sh install_docker.sh
        sudo rm install_docker.sh

        sudo killall docker
        run_process docker-engine "sudo /usr/bin/docker daemon --cluster-store etcd://localhost:4001"
        run_process kuryr "sudo PYTHONPATH=$PYTHONPATH:$DEST/kuryr SERVICE_USER=admin SERVICE_PASSWORD=$SERVICE_PASSWORD SERVICE_TENANT_NAME=admin SERVICE_TOKEN=$SERVICE_TOKEN IDENTITY_URL=http://127.0.0.1:5000/v2.0 python $DEST/kuryr/scripts/run_server.py"
    fi

    if [[ "$1" == "unstack" ]]; then
        stop_process kuryr
        stop_process etcd-server
        stop_process docker-engine
    fi
fi

# Restore xtrace
$XTRACE
```