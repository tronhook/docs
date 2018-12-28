Here is the base configuration file with the description of each properties

```

#Configuration

application {
 tz="UTC"
 port=7171
 env=prod
 name=tronhook_${application.env}
 baseUrl ="http://localhost:"${application.port}
}

db = <mongodburl> # mongodb connection url

#Tron nodes configuration
tron{
	fullnode="<FULL_NODE_HOST:GRPC_PORT>"
	soliditynode="<SOLIDITY_NODE_HOST:GRPC_PORT>"
	mainNet=true
}

nodeId=1 #useful if you want to start multiple nodes to process the same hook in parell

#Specify the qualified name of the Hook to use
hook = "org.tronhook.XXX" # For example: org.tronhook.hook.elastic.BlockSyncHook


blockStart=0 # The block from which this node should start processing
blockStop=-1 # The block to which this node should stop processing 
(-1 means until last available blocks)

blockRefBatchSize=100000 #Block reference batch size

node=[full|solidity] # node type to use (full or solidity)

#Latest block processor configuration
latestBlocks{
	enabled=true
	batchSize=1
	workers=1
	workerBatchSize=1
}

#Previous block processor configuration
previousBlocks{
	enabled=true
	batchSize=1000
	workers=10
	workerBatchSize=100	
}


```