# Tic Tac Toe in a Schnorr Signature

The game [Tic Tac Toe](https://en.wikipedia.org/wiki/Tic-tac-toe) represented in Bitcoin. This model requires only a single onchain transaction containing a single Schnorr signature. 
Scriptless scripts and the replace-by-fee mechanism are used to implement the full game logic.

Before the game starts the tree of possible moves is scaffolded out. There are 26830 different games. Every possible move is represented by a transaction with a 2-of-2 MuSig splitting the key among both players. 
All of these transactions compete to spend the same output. If any transaction hits the chain it gives the bitcoins to the current player (or refunds both players in case of a draw). There are no further scripts.
In the non-cooperative case older transactions are replaced by newer transactions via the replace-by-fee mechanism. 

<img src=tictactoe.png>

Before the game starts, each player traverses the tree of game states and signs every possible move of their opponent. The player subtracts his signature by his signature of the parent transaction that leads to this particular game state. 
These so-called *adapter signatures* form a tree which is exchanged upfront. This ensures a player can execute a transaction exactly if the opponent executed the preceding round. 

The game starts once the scaffold is complete. The two players take turns revealing signatures to each other. 
- In the cooperative case the players send the signatures to each other privately.
  - In this case only a single transaction has to be broadcasted to the bitcoin network.
- In the non-cooperative case players take turns replacing each others' transactions with the replace-by-fee mechanism (RBF).
  - There are at most 9 rounds in Tic Tac Toe.
  - An honest player can always update to the most recent state without going through intermediate states.
  - The fee of a newer transaction is always at least 1 sat/byte higher than in the preceding round.

Only one last transaction hits the chain and spends the output.


## Shortcomings 
- The game must end before an intermediate game state hits the chain. 
- Using RBF as consensus mechanism is risky. This update mechanism depends on the mempool of bitcoin miners. We assume no player cooperates with a miner. Also, players have to finish the game before an intermediate TX hits the chain.
