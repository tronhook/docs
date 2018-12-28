# Quickstart

The easiest way to get started with TronHook is to use the Docker image.

## Setup your configuration file

Get a copy of TronHook base configuration file (`application.conf`) in your working directory and rename it as `application.prod.conf`.

You can use the following command to do that:

```bash
wget https://raw.githubusercontent.com/tronhook/tronhook/master/node/conf/application.conf -O application.prod.conf
```

!> Get more details about the configuration properties [here](/configuration)

## Start a mongo instance

Tronhook requires a mongodb instance in order to keep a record of the blocks already processed.

You can start mongo with following command:

```bash
docker run --name mongo -p 27017:27017 -d mongo
```

## Run a hook

### Notification hook

Follow these steps if you want to start receiving notifications via websocket or http for your dApp

!> The notification hook will only send notifications if some rules where added, see how to add new rules [here](/rules)

Run the node:

```bash
docker run -p 7171:7171 -p 7272:7272 --link mongo --name tronhook.notification -d -v application.prod.conf:/application.prod.conf -e APP_OPTS="prod hook=org.tronhook.hook.NotificationHook" tronhook/node
```

This hook will start sending messages via websocket to port 7272

You can start receiving message on client side with this sample code:

```javascript
var ws = new WebSocket('ws://localhost:7272')
ws.onmessage = (msg) => {
    let message = JSON.parse(msg.data)
}
```


### Block synchronization hook

Follow these steps if you want to synchronize data from Tron blockchain into a database

The database used to is elasticsearch, you can start an elasticsearch instance with following command:

```bash
docker run -d --name elastic /home/<user>/elastic-data:/usr/share/elasticsearch/data -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" docker.elastic.co/elasticsearch/elasticsearch:6.5.
```

Run the node:

```bash
docker run -p 7171:7171 -p 7272:7272 --link mongo --name tronhook.blocksync -d -v application.prod.conf:/application.prod.conf -e APP_OPTS="prod hook=org.tronhook.hook.elastic.BlockSyncHook" tronhook/node
```

!> If no rule was added into rule engine, every blocks/transactions will be synced. Otherwise, only those matching your rules will be synced.
