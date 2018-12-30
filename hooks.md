# Hooks

![TronHook node](/images/hook.png)

A hook is a task that will be executed for each block. It receives data directly from the rule engine that filters blocks and transactions according to the all rules available.

## Available hooks

Tronhook comes with some default hooks that you can use right away.

### Notification hook

NotificationHook lets you receive notifications (via HTTP or WS) when the rules have matching facts (block or transactions)

- http notification: you must provide a callback url that will be use to make a POST request with all the rules that were 'true' as well as the associated facts data (blocks or transactions)

**Configuration to add in application.[env].conf**
```conf
#Use notification hook
hook="org.tronhook.hook.NotificationHook"

NotificationHook{
	protocol=ws # choose protocol to use : http or ws
	httpcallbackurl="<http_url>" # when protocol = http will be used for callback

    #Websocket server configuration
	ws{
		host="localhost"
		port=7272
	}
}

```

**Sample of notification body**

The notification request body is a json payload structured as followed:

```json
{
'ruleId':[list of facts matching this rule]
}
```
Example:

```json
{
    "contract_trigger": [
        {
            "hash": "97f2947ca4b1c0f688625cf35035d21a9387ed84b1951bd0b8375d3beb29b8d1",
            "block": 5068245,
            "contract": {
                "from": "TSV5n6u5CcaT2K1c6WbWuqBZXDgUddnn9N",
                "contractAddress": "TEEXEWrkMFKapSMJ6mErg39ELFKDqEs6w3",
                "callValue": 0,
                "data": null,
                "callTokenValue": 45000000,
                "tokenId": 0
            },
            "from": "TSV5n6u5CcaT2K1c6WbWuqBZXDgUddnn9N",
            "to": null,
            "timestamp": 1545206664000,
            "type": 31,
            "data": null,
            "size": 216
        }
    ]
}
```
### Block synchronization hook

Synchronizes the blocks into an elasticsearch database

**Configuration to add in application.[env].conf**
```
hook="org.tronhook.hook.elastic.BlockSyncHook"

BlockSyncES{
	soliditynode=${tron.soliditynode} #solidity node to use (only used if node type is set to solidity)
	fetchFee=false #should tx fees be retrieved while processing transactions ? (only used if node type is set to solidity)
	elasticsearch{
		host="<HOST>"
		port=<PORT>
		scheme=[http|https]
	}
}

```


## Writing your own Hook

Writing a hook only consist of extending a class that will receive the filtered blocks.


```java

class MyHook extends TronHook {

    // The field TRON_HOOK_ID is mandatory and will be used 
    // as configuration name
	public static final String TRON_HOOK_ID = "MyHook";
	

    // Hook receives configuration in constructor
	public MyHook(Config config) {
		super(config);
		
	}

    /**
    * Called on node start
    **/
    onStart(){
        // useful for initializing resources like db  ...
    }

    /**
    * Called on node stop
    **/
    onStop(){
        // useful for closing resources like db  ...
    }

    /**
    * Receives filtered blocks / transactions
    **/
    processBlocks(List<BlockModel> blocks) throws TronHookException{

        // itereate over blocks, transactions ...

        // hook configuration can be retrieve with following method
        getConfig.getIntValue('myhook_config')

    }

}

```