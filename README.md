
# About

Class to convert between IEEE.754 32-bit hexadecimal strings and single-precision floating point numbers (`single`).  MATLAB’s built-in hex2num and num2hex convert between a IEEE.754 64-bit 16-char hex and a double-precision decimal value (`double`).  This pakcage provides support for 32-bit IEEE.754 hexadecimal strings.

## The IEEE.754 standard is a special format

- [IEEE.754 Double-Precision Format Wikipedia](https://en.wikipedia.org/wiki/Double-precision_floating-point_format)
- [IEEE.754 Single-Precision Format Wikipedia](https://en.wikipedia.org/wiki/Single-precision_floating-point_format)

IEEE.754 hex strings do not correspond to the standard big endian hex you would assume based on the base10 int, e.g.:

<table>
	<tr>
		<th>Base 10 Int</th>
		<th>&nbsp;</th>
		<th>32-Bit Hex</th>
	</tr>
	<tr>
		<td>1</td>
		<td>!=</td>
		<td>0x00000001</td>
	</tr>
	<tr>
		<td>10</td>
		<td>!=</td>
		<td>0x0000000A</td>
	</tr>
	<tr>
		<td>254</td>
		<td>!=</td>
		<td>0x000000FE</td>
	</tr>
</table>

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

# Installation

This repo and its dependencies are namespaced packages.  See [Working With Packages in MATLAB](https://github.com/cnanders/matlab-package-notes) for help.

1. Clone this repo and the repos of all [dependencies](#dependencies) into your MATLAB project, preferably in a “vendor” directory.  See [Recommended Project Structure](#project-structure)

2. Add the namespaced packages to the MATLAB path, e.g., 

```
addpath('vendor/github/cnanders/matlab-hex/src');
addpath('vendor/github/cnanders/matlab-ieee/src');
```

<a name="dependencies"></a>
# Dependencies

- [matlab-hex](https://github.com/cnanders/matlab-hex.git)


# Hungarian Notation

This repo uses [MATLAB Hungarian notation](https://github.com/cnanders/matlab-hungarian) for variable names.  

<a name="project-structure"></a>
# Recommended Project Structure

- project
	- vendor
		- github
			- cnanders	
				- matlab-ieee **(this repo)**
				- matlab-hex **(dependency)**
	- file1.m
	- file2.m