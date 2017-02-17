# Required Packages

- [matlab-hex](https://github.com/cnanders/matlab-hex.git)

# About

Helper functions to convert between IEEE.754 64-bit and 32-bit hexadecimal strings and floating point numbers.  

MATLAB’s built-in hex2num and num2hex convert between a 64-bit IEEE.754 16-char hex and a decimal value (double).  This pakcage provides support for 32-bit IEEE.754 hexadecimal strings.

## The IEEE.754 standard is a special format

- [IEEE.754 Double-Precision Format Wikipedia](https://en.wikipedia.org/wiki/Double-precision_floating-point_format)
- [IEEE.754 Single-Precision Format Wikipedia](https://en.wikipedia.org/wiki/Single-precision_floating-point_format)

The hex string does not correspond to the standard big endian hex you would assume based on the base10 number, e.g.:

- 1     != 0x00000001 (32-bit hex)
- 10    != 0x0000000A (32-bit hex)
- 254   != 0x000000FE (32-bit hex)

The bits of a IEEE hex string have special meaning.  Step 1 is to
convert the hex string into its binary equivalent then starting on
the left group the bits in to sign/exponent/fraction blocks. 

For 32-bit IEEE.754 format:
- bit     1       (1-bit)     === sign
- bits    2-9     (8-bits)     === exponent
- bits    10-32   (23-bits)    === fraction
- Decimal value is -1^sign * (1 + fraction/2^23) * 2^(exponent-127)

For 64-bit IEEE.754 format:
- bit     1       (1-bit)     === sign
- bits    2-12    (11-bits)    === exponent
- bits    13-64   (52-bits)    === fraction  
- Decimal value is -1^sign * (1 + fraction/2^52) * 2^(exponent - 1023)

Example (32-bit)
- binary: 1 00000011 00000000000000000001011 (spaces to separate sign, exp, frac)
- sign = -1 (if first bit is 1, sign is negative)
- exponent = bin2dec('00000011') = 3
- fraction = bin2dec('00000000000000000001011') = 11
- value = -1 * (1 + 11/2^23) * 2^-124 = -4.7020e-38
- The hexadecimal value is '8180000B'

Example (32-bit)
- binary: 0 01111111 00000000000000000000000
- sign = 1
- exponent = bin2dec('01111111') = 127
- fraction = bin2dec('00000000000000000000000') = 0
- value = 1 * (1 + 0/2^23) * 2^(127 - 127) = 1
- Hex equivalent of '00111111100000000000000000000000' is '3F800000',
- Verified this is correct with Wikipedia 


