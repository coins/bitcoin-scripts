

# Selecting an Element from a Map

## Selecting from an Map: Solution 1

```
btcdeb "[ 
	

	0x02			# On the stack is the index we want to access
	TOALTSTACK

	IF
		IF
				0x0d 0x04		# Element at index 4
		ELSE
				0x11 0x03		# Element at index 3
		ENDIF
	ELSE
		IF
				0x13 0x02		# Element at index 2
		ELSE
			IF
				0x17 0x01		# Element at index 1
			ELSE
				0x1d 0x00		# Element at index 0
			ENDIF
		ENDIF
	ENDIF


	# Verify that the hint was correct
	FROMALTSTACK
	EQUALVERIFY


	# We're done. The corresponding element is on the top stack

#]" 0x01 0x 		# The hint provides the binary representation of the index
```


## Selecting from an Map: Solution 2

The following script selects an element from a map. The input can select an element from an array with five elements. In this example, the fourth element (the element at index `0x03`), is selected. So we will have 23 on the stack. 

```
btcdeb "[ 

	OP_DUP
	OP_0NOTEQUAL OP_NOTIF
		13 0
	OP_ENDIF

	OP_1SUB
	OP_DUP
	OP_0NOTEQUAL OP_NOTIF
		17 0
	OP_ENDIF

	OP_1SUB
	OP_DUP
	OP_0NOTEQUAL OP_NOTIF
		19 0
	OP_ENDIF

	OP_1SUB
	OP_DUP
	OP_NOTIF
		23 0
	OP_ENDIF

	OP_1SUB
	OP_NOTIF
		29
	OP_ENDIF

	OP_NIP

# ]" 0x03
```

This can be easily generalized for maps of tuples.
