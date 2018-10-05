# AES
Repository for Intro to Sec AES Crypto assignment

Notes: 
	Our program seems to be encrypting/decrypting properly using 128 bit keys, but we were unable to get the 256 bit version working properly.

To run:
	If haven't compiled yet:
	1.	$ gcc AES.c -o aes

	2.  To encrypt:  ./aes --keysize $KEYSIZE --keyFile $KEYFILE --inputFile $INPUTFILE --outputfile $OUTFILENAME --mode encrypt	

	3. 	To decrypt:  ./aes --keysize $KEYSIZE --keyFile $KEYFILE --inputFile $INPUTFILE --outputfile $OUTFILENAME --mode decrypt

	The arguments MUST be in this order for our program to work*

	When encrypting/decrypting, the output is written to the output file $OUTFILENAME
	If going to run multiple tests using the same outputfile names, then need to remove the previous output file of that name


Algorithm: 
	Program first reads in arguments then, depending on the size of the key, conducts the appropriate key expansion and saves the expanded key to a variable. 

	Key Expansion: 
	The 128 bit version of key expansion creates 176 bytes of the expanded key, 16 bytes at a time, using a combination of shifts, byte shifts, byte substitutions, and XOR's to create the bytes.

	The 256 bit version of the key expantion creates 240 bytes for the expanded key using the same methods as the 128 bit version, but with an extra Byte Substitution step in between the usual steps.

	Padding:
	We are using ZeroLength padding, so when padding is necessary we pad the bytes with 0's with the last byte being the number of bytes that we padded with

	Encryption:
	Our encryption method first reads in 16 bytes of the input and saves it to a temporary array called temp, checks the size of the keysize, then begins the appropriate encryption process depending on the key size.
	Depending on the keysize, the process does a different amount of rounds to encrypt the 16 bytes.
	An initial AddRoundkey is performed, adding the initial 16 bytes of the expanded key before starting rounds.
	Rounds: 10 rounds are done for 128 bit keys, 14 rounds are done for the 256 bit keys. The last round is done differently than those before it for both sizes of keys. 
	First step of a round (that isn't the final one) is a SubBytes operation, followed by a ShiftRows, then a mixColumns operation, and finally an AddroundKey, which adds a different portion of the expanded key for each round.
	The last round only consists of a SubBytes, a ShiftRows, and an AddRoundKey that adds the final 16 bytes of the expanded key.
	The encrypted bytes are then written to the intended output file 16 bytes at a time.

	Decryption: 
	Uses the same methods as encryption except in different order, and the expanded key is added differently, and it removes padding (if there is any). 
	The initial AddRoundKey adds the final 16 bytes of the round key to the state
	We then begin to start rounds, which start with the Inverse ShiftRows, followed by the Inverse SubBytes, an AddRoundkey that adds the expanded key starting from the end moving towards the beginning, then an Inverse MixColumn.
	The final round consists of an Inverse ShiftRows, followed by an Inverse SubBytes, and ending with an AddRoundKey adding the first 16 bytes of the expanded key.
	Before ending, the decryption method checks to see if there is any padding by looking at the 16th byte and checking if the appropriate amount of bytes are 0's. If there is padding, then it just doesn't add the padded bytes to the output file.


	SubBytes: 
	Our subBytes method simply replaces a byte with a byte in a byte in the SBOX lookup table, where the initial byte is used as an index for that lookup table.

	ShiftRows:
	Shift rows shifts the rows of the 16 byte block to the left. The 0th row shifts 0 times, the 1st row shifts 1 time, the 2nd row shifts 2 times, the 3rd row shifts 3 times. We are using a 1-d array for storing our bytes, so the indexes look a bit strange here.

	0 4  8 12				0  4  8  12
	1 5  9 13		-->		5  9  13 1
	2 6 10 14		-->     10 14 2  6
	3 7 11 15				15 3  7  11

	mixColumn:
	Our mixColumns makes use of multiple lookup tables in order to do the "multiplication".
	The byte is used as an index into the appropriate lookup table, then for the "addition" it is XOR'd with another multiplication and two other bytes.
	Following this formula: s_0= ({02}*s0) + ({03}*s1) + s2 + s3
	The numbers within curly braces say which lookup table to use the number its multiplied by is the byte that should be used as an index. 

	AddRoundKey:
	Our AddRoundKey function simply XOR's each byte of the expanded key to the state that holds the encrypted messaged.

	InverseShiftRows:
	Inverse shift rows follows the same formula for the regular shiftRows but instead of shifting to the left, shifts to the right.

	InverseMix Columns:
	Inverse Mix Columns also follows a similar formula to MixColumns but the lookup tables used are different.
