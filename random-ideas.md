# Random Ideas

List of random ideas about using Bitcoin Script in uncommon ways.

## Fast Multiplication using a Residue Number System

Using a [residue number system](https://en.wikipedia.org/wiki/Residue_number_system) we can multiply numbers of more than 2 bytes just by using lookup tables. 

E.g. the multiplication tables for the residue system `4, 9, 25, 7, 11` would cost 892 items in lookup tables.

Given that table, a multiplication would cost around 36 opcodes. Addition around the same.
but greater than and less than would become extremely expensive


## Programming a UTXO after it was created 

in theory, you can have programs which are expressed entirely of functions defined by lookup tables. the opcodes of your program then become the addresses of the corresponding lookup tables. 

that means you can provide the program in the unlocking script in form of such addresses. and that means you can designate someone who can Lamport-sign the program and thus, program an output's spending conditions after the UTXO was created.
