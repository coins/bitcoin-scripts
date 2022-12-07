# Constraints in Bitcoin Script 


- "The argument of `OP_IF` / `NOTIF` in P2WSH must be minimal" 
  - See [here](https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2016-August/013014.html), and [here](https://bips.xyz/141#new-script-semantics). 
  - _"An_ `OP_0NOTEQUAL` _may be used before_ `OP_IF` _or_ `OP_NOTIF` _to imitate the original behaviour (which may also re-enable the malleability vector depending on the exact script)."_
