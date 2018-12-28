# Rules
TronHook comes with a rule engine that allows you to filter some blocks and or transactions so that your hook can process only the data that you need.

Rules can be added dynamically at runtime through TronHook [node's API](node?id=api) without any restart required.

Here are some examples of rules:

**Match all  transaction of type "TransferContract"**
```bash
curl -X POST "localhost:7171/rule" -H 'Content-Type: application/json' -d'
{
	"id":"contract_trigger",
	"rule":"type==1",
	"context":"transaction",
	"repeat":"always"
}
'
```
**Match a  transaction once with specific hash and tx is confirmed**
```bash
curl -X POST "localhost:7171/rule" -H 'Content-Type: application/json' -d'
{
	"id":"contract_trigger",
	"rule":"hash=='5a2e60fe45906668fa59fd9f9d414209106f51fd52ac7176104bc0b31c66dfff' and confirmed==true",
	"context":"transaction",
	"repeat":"once"
}
'
```

**Match all  transactions of type "TriggerContract" with a  specific contract address**
```bash
curl -X POST "localhost:7171/rule" -H 'Content-Type: application/json' -d'
{
	"id":"contract_trigger",
	"rule":"tx.type==31 and contract.contractAddress=='TEEXEWrkMFKapSMJ6mErg39ELFKDqEs6w3'",
	"context":"transaction",
	"repeat":"always"
}
'
```

## Rule specification
Field | Description
-------- | -----
id | Rule unique id
rule | Rule expression
context | [transaction\|block] indicates on which type of fact (block or transaction) should this rule be applied
repeat | [once\|forever] if "once" the rule will be removed the first time it matches a fact

## Rule expression

## Rule errors
