# Hooks

A hook is a task that will be executed each for each block on TRON blockchain. This block can be a new block or a block for which the transactions matches a rule or a set of rules you defined.

## Available hooks


### NotificationHook

NotificationHook lets you receive notifications (via HTTP or WS) when the rules have matching facts (block or transactions)

- http notification: you must provide a callback url that will be use to make a POST request with all the rules that were 'true' as well as the associated facts data (blocks or transactions)

**Configuration to add in application.[env].conf**
```
hook="org.tronhook.hook.NotificationHook"

NotificationHook{
	httpcallbackurl="<http_url>"
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
TODO