# Lookup Tables and Static Functions

Using `OP_PICK` to implement lockup tables. `OP_PICK`: _The item n back in the stack is copied to the top._



This is particularily useful if we want to randomly access multiple elements in sequence, since we can reuse the lockup table without using any more opcodes. The following example is a lookup table for powers of two.

```sh

btcdeb "[ 

	7				# An arbitrary index is on the stack
	TOALTSTACK

	
	# The elements of our lookup table are put onto the stack
	524288
	262144
	131072
	65536

	32768
	16384
	8192
	4096

	2048
	1024
	512
	256

	128
	64
	32
	16
	
	8
	4
	2
	1

	FROMALTSTACK
	OP_PICK

	# Now the element at index=7 is on the stack. It is 2^7 = 128


	TOALTSTACK

	# At the end of the program we have to delete our lookup table
	OP_2DROP OP_2DROP 
	OP_2DROP OP_2DROP
	OP_2DROP OP_2DROP 
	OP_2DROP OP_2DROP
	OP_2DROP OP_2DROP

	FROMALTSTACK
# ]"
```

As soon as the lookup table is on the stack, we can efficiently use it from anywhere in the script. Using `OP_DEPTH` we can even handle dynamic stack sizes to perform the required "pointer arithmetic" for the offset to be used with `OP_PICK`. In this example, the address of `pow2` is `24`.






## Multiple Lookup Tables and Function Calls

Here is an example of two different lookup tables, which allow to mimic function calls. The position of the lookup table in the stack becomes the function name.

```sh
btcdeb "[ 

	7 					# An arbitrary index is on the stack
	3 					# Annother arbitrary index is on the stack

	# Put them on the altstack for later
	TOALTSTACK
	TOALTSTACK



	######### Function Definitions #########

	# The lookup table for pow2
	524288
	262144
	131072
	65536

	32768
	16384
	8192
	4096

	2048
	1024
	512
	256

	128
	64
	32
	16
	
	8
	4
	2
	1


	# The lookup table for get_prime
	89
	83
	79
	73
	
	71
	67
	61
	59
	
	53
	47
	43
	41
	
	37
	31
	29
	23
	
	19
	17
	13
	11
	
	7
	5
	3
	2


	############# Examples of Function Calls ##########


	# Get the first argument onto the stack
	FROMALTSTACK

	# Our first 'function call' is pow2. 24 is its address and 'function name'.
	24 ADD PICK

	# Now the result pow2(7) = 2**7 = 128 is on the stack


	# Get the second argument onto the stack 
	FROMALTSTACK
	SWAP
	TOALTSTACK


	# Our second 'function call' is get_prime. Its address is 0. So no addition needed for this 'name'
	PICK	

	# We use the result as an argument for another call of is get_prime
	PICK


	# And with that result we call again pow2
	24 ADD PICK

	
	# Now we have pow2( get_prime(get_prime( input_b )) ) on the stack
	TOALTSTACK

	



	######## Boilerplate to Cleanup ########

	# At the end of the program we have to drop our lookup table to clean up the stack
	2DROP 2DROP 
	2DROP 2DROP
	2DROP 2DROP 
	2DROP 2DROP
	2DROP 2DROP

	2DROP 2DROP 
	2DROP 2DROP
	2DROP 2DROP 
	2DROP 2DROP
	2DROP 2DROP
	2DROP 2DROP


	# Push the results of our function calls back on the stack
	FROMALTSTACK
	FROMALTSTACK	

# ]"
```

## Dynamic Function Names 
In our previous example we assumed to make all function calls at the same stack height. The following script accounts for calls at dynamic stack heights:

```
<argument>
OP_DEPTH <fn_address> SUB ADD PICK
```

A bit more convenient is to use negative numbers for the `<fn_address>` such that it can be the top stack item:
```
<argument>
<fn_address> OP_DEPTH ADD ADD PICK
```

Note that `<fn_address>` is a number and this allows to even dynamically define the function to be called. 

<!-- TODO: ## Functions with Multiple Arguments -->




