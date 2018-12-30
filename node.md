# Node
The node is the engine that lets you run a hook. When you run a node you have to specify the hook to use and it's configuration.

![TronHook node](/images/node.png)

!> A node can run a single hook, but you can run as many node as you want on a single or multiple servers.

## Block processor
The block processor is in charge of retrieving data directly from Tron's nodes. It has a 2 jobs that will process the latest incoming blocks as well as the previous blocks that were not yet processed (depending of your configuration/requirements). These two jobs can be configured independently with [`latestBlocks`](/configuration) and [`previousBlocks`](/configuration) options.

It is possible to configure the number of workers per job ([`workers`](/configuration) option) with as well as batch size ([`batchSize`](/configuration) option). You can change these options in order to process more blocks ans speed up the process. These configuration greatly depends on the server configuration (network/memory...) the node is running on, test different configurations to find out which one is the best.

To keep track of the blocks that were already processed, a mongo database is used and its url location must be configured with the [`db`](/configuration) option.

A [`retryPolicy`](/configuration) configuration is available so that the processor doesn't drop a block on the first failed try if something wrong happens.

The block processor can be configured to retrieve a specific range of blocks with [`blockStart`](/configuration) and [`blockStop`](/configuration) options. Setting `blockStop` to -1 will allow to always keep processing the last block.


## Scaling

![TronHook node](/images/scaling.png)

You can run one or multiple node on a single server, but you can also choose to scale accross multiple servers in order to distribute the work load. For example Node1 can process from block 0 to 100000, Node2 blocks 100001 to 2000000 both on server 1 and node 3 blocks X to n on another server 2...

!>In such a configuration all the nodes should use the same mongodb instance so that they can share the same rules. If you don't want to use the rule engine feature, you don't have to use the same database for each node.

## API

The node provides an API interface that can be used at runtime in order to submit rules to the rule engine or change some of the node configurations

- `GET /rules` list of rules available
- `POST /rule` add a new rule
- `DELETE /rule/:id` remove a rule by id
- `GET /stats` get statistics info about current node

## Starting a node

The simplest way to get started is to use the docker image. If you don't want to use it, you can still build the project from source and launch from the compiled project.

### Prerequisite

Before launching a node, you need to have a mongo instance running.

You can launch it with following docker command for example:

```bash
docker run --name mongo -p 27017:27017 -d mongo
```

### Launching with Docker

To launch with docker use the following command and change it according to the hook you want to use

```bash
docker run -p 7171:7171 -p 7272:7272 --link mongo --name tronhook.notification -d -v ${working_dir}/application.prod.conf:/application.prod.conf -e APP_OPTS="prod hook=<HOOK_QNAME>" tronhook/node
```

Follow [quicktart](/quickstart) for more details

### Launching from source

**Build requirements**

- maven 3
- jdk 8

To build the sources run the following commands:

```bash
$ git clone https://github.com/tronhook/tronhook.git
$ cd tronhook
$ mvn install
```

Then, to run the node, run the following command:

```bash
java -jar ./target/node-<CURRENT_VERSION>.jar
```
