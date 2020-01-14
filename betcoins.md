# Betcoins - A trustless Bitcoin betting protocol

Betcoins is a protocol for trustless Bitcoin bets. It uses only OP-codes currently supported in Bitcoin Script. It allows any bet sizes, any discrete probability distributions and any payout sizes. It can be generalized to an election of a random leader.

## Naive Betting

### Simple Number Commitment
A number commitment enforces a spender to reveal some secret number `N`. For a coin flip `N` is simply 0 or 1. 

1. Choose secret number `N` in `{0,1}`
2. Choose secret nonce `R` which is `N + 16` bytes of randomness 
3. The public commitment is `C = SHA256( R )`

The following scriptPubKey enforces the spender to reveal `R` and thus `N`:
```
OP_DUP 
OP_SHA256 
	<commitment>
OP_EQUALVERIFY 
OP_SIZE 
OP_16
OP_SUB
OP_TOALTSTACK 
OP_DROP 
OP_FROMALTSTACK 
```

It leaves `N` on the stack. This is a handy primitive because further processing of `N` is possible using arithmetic operations.


Max size of `N` is the max scriptSig size which is 10000 bytes. https://bitcoin.stackexchange.com/questions/35878/is-there-a-maximum-size-of-a-scriptsig-scriptpubkey 
We discuss the exact boundaries in a later chapter. 

This primitive is also handy for mass-interaction with oracles.


### Naive Contract 
The obvious idea is that both Alice and Bob create a number commitment `Alice_N`, `Bob_N` and sum up their values to derive a common value `Sum_N = Alice_N + Bob_N`.
```
OP_DUP 
OP_SHA256 
	<Alice_commitment>
OP_EQUALVERIFY 
OP_SIZE 
OP_TOALTSTACK 
OP_DROP

OP_DUP 
OP_SHA256 
	<Bob_commitment>
OP_EQUALVERIFY 
OP_SIZE 
OP_TOALTSTACK 
OP_DROP 
	
OP_FROMALTSTACK  
OP_FROMALTSTACK 
OP_ADD
```
Spendable with the following scriptSig:
```
<Alice_R>
<Bob_R>
```
This script leaves `Sum_N` on the stack.

## Coin Flip
We want to simulate a coin flip having as outcome either 0 or 1. Both Alice and Bob provide one bit of entropy.
For Alice to express her choice she commits to either `Alice_N == 16` or `Alice_N != 16`. 
The script puts 16 on the stack and `OP_NUMEQUAL` with `Alice_N` to retrieve Alice's choice represented in one bit.

```
OP_DUP 
OP_SHA256 
	<Alice_commitment> 
OP_EQUALVERIFY 
OP_SIZE 
OP_16
OP_NUMEQUAL
OP_TOALTSTACK
OP_DROP
OP_FROMALTSTACK
```

Bob does the same and the result is combined with XOR. There is no opcode `OP_XOR`, so we use a workaround:

```
OP_DUP 
OP_SHA256 
	<Alice_commitment>  
OP_EQUALVERIFY 
OP_SIZE 
OP_TOALTSTACK
OP_DROP
	
OP_DUP 
OP_SHA256 
	<Bob_commitment>  
OP_EQUALVERIFY 
OP_SIZE 
OP_TOALTSTACK
OP_DROP
	
OP_FROMALTSTACK
OP_16
OP_NUMEQUAL

OP_FROMALTSTACK
OP_16
OP_NUMEQUAL

OP_ADD
OP_1
OP_EQUAL

OP_IF
	<Alice PubKey>
OP_ELSE
	<Bob PubKey>
OP_ENDIF
OP_CHECKSIGVERIFY
```

In the following we denote the above script with `OP_BET( C1, C2 )` which the winner can spend if he knows both R1 and R2.

## Withholding Attacks

We need to prevent data withholding attacks. We enforce revealing pre-images with timelocks.
We chain transactions such that an attacker has to reveal his pre-image or lose. 

### Naive Solution with a Collateral
A simple solution is using deposits. 

- Both Alice and Bob bet 1 BTC each. 
- They also each deposit 1 BTC each in a separate contract. 

Alice's collateral contract executes the following conditional:

- Alice can have her deposit back if she reveals her pre-image in time
- Bob can take her deposit after the lock timer runs out

Bob creates the inverse contract with Alice to mutually enforce revelation of their pre-images.
This is secure and simple but requires a 1:1 deposit. Can we achieve the same without a deposit ?

### Solution without Collateral
We discuss a solution without collaterals. 

- Alice and Bob can play the coin flip contract ( cooperative case )
- After some lock time:
	- Alice can execute her penalty transaction by revealing her pre-image
	- Bob can execute his penalty transaction by revealing his pre-image

Alice's penalty transaction implements:
- Alice can take all the money after some lock time
- Bob can finish the game by revealing his pre-image ( cooperative case )

Alice's penalty transaction is pre-signed by Bob and vice versa. This requires two more rounds of communication during setup.


### Solution with only one on-chain TX
We want to construct a solution with only one on-chain TX. We can use replace-by-fee and `nSequence` to remove the false-penalty case from the chain. 

- Alice and Bob can play the coin flip contract ( cooperative case )
- After some lock time:
	- Alice can execute her penalty transaction by revealing her pre-image
	- Bob can execute his penalty transaction by revealing his pre-image

Alice's penalty transaction implements:
- Alice can take all the money 
- Bob can replace her transaction and finish the game by revealing his pre-image

#### Limitations 
- If Alice is a miner she can execute her transaction without revealing her pre-image first to Bob

## Varying Payouts 
It is trivial to change the probability distribution and payouts. Instead of a 50:50 chance we can implement 75:25 simply by changing
```
OP_ADD
OP_1
OP_EQUAL 
```

to 

```
OP_ADD
OP_2
OP_EQUAL
```

It is also trivial to take the three outcomes of the algorithm 0,1,2 with respective probabilities 25 : 50 : 25, and map them to three different code branches to implement different games like flipping a coin twice.


# Further Research

## Multi-Party Betting
We want to construct bets between 3 or more people.

## Dice Roll 
We want to construct a regular dice with the outcomes 1 to 6. 

## Betting in Payment Channels 
- HTLCs can work as number commitments 
- repetitive bets 
- chaining bets across inputs


# References 
"Pre-Image Length Probabilistic Payments"
- [Lightning Network as a Directed Graph: Single-Funded Channel Network Topology
](https://www.youtube.com/watch?v=-lgYYz3y_hY)
