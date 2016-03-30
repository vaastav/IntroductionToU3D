# A Beginner Introduction to U3D Files

This post will serve as a simple introduction to the U3D file format specificaton for any person who needs to write an output or input stream for U3D objects or is genuinely interested in 3D formats.

This post will also contain an annotated U3D file an other important things which are needed to understand the file format and what all needs to be done to actually get your 3D data embedded into a U3D file that can later be embedded in a PDF file.

**NOTE This post follows the U3D file format specification which can be found at [File Specification](http://www.ecma-international.org/publications/files/ECMA-ST/ECMA-363%204th%20Edition.pdf)**

## What is U3D?

Before we start looking at the U3D file format, for those who don't know what U3D is, this is an introduction section for U3D.
U3D is a compressed file format standard for 3D omputer graphics data.
The goal of U3D is to create a universal standarad for 3D data of all kinds, so as to make data exchange easier.

Personally, for my job, I have only used 3D data to write meshes with corresponding textures to a U3D object. Although, you can write a lot more cool stuff with U3D like animation data.

### Supported Elements

If you plan to embed, your U3D objects in a PDF, then you should read this [document](http://www.adobe.com/content/dam/Adobe/en/devnet/acrobat/pdfs/U3DElements.pdf) first as Adobe Acrobat only supports a subset of the elements that U3D provides. 
Additionaly, Adobe also doesn't support some of the texture modes like those of texture generation but I will talk about them later when I get into the details.

## External Library

As part of the consortium, Intel released a library which is available on sourceforge.

## File Format Essentials

The bytes in the file are written in Little-Endian order. If you don't know what endianness is, here is a [link](https://www.cs.umd.edu/class/sum2003/cmsc311/Notes/Data/endian.html).

The U3D file is made up of 3 different blocks.

- File Header Block
- Declaration Block
- Continuation Block

Each block must be 32-bit or 4-byte aligned with each other. This basically means a new block must **ONLY** start at an address which is divisible by 4. For more on aligned addresses and alignment, please read [this](http://www.songho.ca/misc/alignment/dataalign.html).

### What is a Block?

All Blocks in U3D have the same structure.

+ 4 bytes : BlockType - Identifies the kind of data present in this block
+ 4 bytes : DataSize - Size of the data section in bytes. **Does not include any padding**
+ 4 bytes : MetaDataSize - Size of the meta data section in bytes. **Does not include any padding**
+ *DataSize* bytes : Data - Interpreted w.r.t to the block type
+ 0-3 bytes : Padding. Added to maintain 32-bit alignment for the start of the metadata section
+ *MetaDataSize* bytes : Metadata - An array of Key/Value Pairs. I personally have never used metadata and this is very much optional as you can just put the MetaDataSize to be 0, but I will try in the future and update the post.
+ 0-3 bytes : Padding. Added to maintain 32-bit alignment for the start of the next block.

**NOTE: Any padding byte must have the value 0x00.**

## File Header Block

Here is the file header block from the U3D file in terms of HEX values in little endian form.
For the ease of understanding, I have broken down the block into lines of 12 bytes and will go through it line by line.
```
55 33 44 00 20 00 00 00 00 00 00 00
00 00 00 00 0C 00 00 00 29 01 00 00
7F 02 00 00 00 00 00 00 6A 00 00 00 
00 00 00 00 00 00 F0 3F
```
Line 1 : 55 33 44 00 20 00 00 00 00 00 00 00

**55 33 44 00** : This value is 0x00443355. The BlockType of the File Header Block. It is also the FileIdentifier of the file. This is what you would call a magic number.

**20 00 00 00** : This value is 32 base 10. This is the dataSize of the file header block. The file header block will always have this data size.

**00 00 00 00** : This value is 0. This is the metadataSize. As the file header has no metadata with it, this is 0.

**00 00**       : This is the major version of the file format. This is supposed to be 0.

**00 00**       : This is the minor version of the file format. This is supposed to be 0.

**0C 00 00 00** : The profile identifier value.

**29 01 00 00** : The declaration size.

**7F 02 00 00 00 00 00 00** : The file size.

**6A 00 00 00** : The character encoding of all the strings in the file. This value corresponds to UTF-8.

**00 00 00 00 00 00 F0 3F** : Units scaling Factor

The first 12 bytes of the file header block are the same as every block in the U3D block.

The next 4 bytes of the file header block are the file format version. The major version(the first 2 bytes) is supposed to be 0 while minor version(the next 2 bytes) can be any number.

The next 4 bytes is the profile identifier. This is used to identify optional features in the file. The options can be combined by a bitwise OR operation. 

The options are as follows:

+ 0x00000000 : No optional features used

+ 0x00000002 : Uses extensibility features. This indicates that the file may use New Object Type blocks.

+ 0x00000004 : No compression mode. This means that file doesn't contain any compressed values.

+ 0x00000008 : Defined units. This indicates that the objects in the file are defined with units.

The next 4 bytes is the declaration size which is the total size of all the declaration blocks in the file

The next 8 bytes is the total file size of the whole file.

The next 4 bytes is the character encoding of all the strings in the file. This value is the MIB enum value which can be found at http://www.iana
.org/assignments/character-sets 

The next 8 bytes are the units scaling factor. This value converts every value in the file to meters. This value is only present if the Profile Identifier has Defined Units flag set.

## Node Modifier Chain Block

Modifier Chain Blocks are used to contain the declaration blocks for an object and its modifiers.

**Note** The data size for the modifier chain block includes the size of the contained modifier declaration blocks.
