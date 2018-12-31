# Rules
![TronHook node](/images/rule_engine.png)

TronHook comes with a rule engine that allows you to filter some blocks and or transactions so that your hook can process only the data that you need.

Rules can be added dynamically at runtime through TronHook [node's API](node?id=api) without any restart required.

Rules are expression where you can use logical / relational operators in order to test conditions [See the available operators](/rules?id=rule-expression) on a [model](/model ':ignore')

## Rules examples
Here are some examples of rules submitted to the rule engine's API with `curl`:

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
repeat | [once\|always] if "once" the rule will be removed the first time it matches a fact

!> All those fields are mandatory when submitting a new rule to the engine's [API](/node?id=api)

## Rule expression

A rule is an expression with one or multiple operators applied on transactions or block model. You can access any field of these model in order to test your conditions.

!> You can check the fieds and methods that can be used in the rule expression [here](/model ':ignore')

### Relational operators
The relational operators supported are:

 - equal (`==`)
 - not equal (`!=`)
 - less than (`<`)
 - less than or equal (`<=`)
 - greater than (`>`)
 - greater than or equal (`>=`)


### Logical operators

The logical operators that are supported are:

- and (`and`)
- or (`or`)
- not (`not`)

### Helpers method

With relational and logical operators you can express almost any rule you want, but sometimes a rule can be a little bit tricky to express. For example when you want to test if a value is part of an array, the expression can look like this

```
type==4 and not contract.votes.?[voteAddress == 'TGzz8gjYiYRqpfmDwnLxfgPuLVNmpCswVp'].isEmpty()
```

This expression would test if a vote to a specific address exists in the vote list. 

To simplify the expression some helpers are avaible: you can check directly into the model documentation for the available helpers you can use.

For exemple previous expression could be written like the following:

```
type==4 and contract.hasVoteTo('TGzz8gjYiYRqpfmDwnLxfgPuLVNmpCswVp')
```

## Rule errors

When you write a rule, it might contain some mistake or errors. When the rule engine catch an error that is due to an invalid or eronous expression, it will automatically disable the rule. 

Invalid rules can be checked with the following request: `/rules?valid=false` . It will return the list of rules in error with the error message