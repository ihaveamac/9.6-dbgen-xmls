# \*hax 2.7 XML repository for 9.6-crypto titles
This is a repository containing XML files for use with *hax 2.7. They are game-specific offsets for titles that are using 9.6 encryption. These XML files should be placed at "mmap" at the root of your 3DS SD card.

## Generating the XMLs

### Requirements

- [Python 2.7](https://www.python.org/downloads/release/python-2711/)
- A 3DS with 9.2.0-20 or lower, or using arm9loaderhax
- [Decrypt9 by Archshift](https://github.com/archshift/Decrypt9), [Decrypt9WIP by d0k3](https://github.com/d0k3/Decrypt9) or [Decrypt9UI by Shadowtrance](https://github.com/shadowtrance/Decrypt9)
- [3dstool](https://github.com/dnasdw/3dstool) and [ctrtool](https://github.com/profi200/Project_CTR)
- A rom of the game that uses 9.6 crypto
- [This modified script](https://gist.github.com/ihaveamac/304bb69e98fc4ce2d5c9) of ncchinfo_gen.py
- Smealum's [96crypto_dbgen.py](https://github.com/smealum/ninjhax2.x/blob/master/scripts/96crypto_dbgen.py) script to generate the actual xmls
- Certain key files (these files can not be linked here, you'll have to search for them, and the size of them should be exactly 16 bytes):
 - `slot0x25keyX.bin` (SHA-256: 7e878dde92938e4c717dd53d1ea35a75633f5130d8cfd7c76c8f4a8fb87050cd) (only needed when decrypting Secure2 if your 3DS is below version 7.0 or if you're using arm9loaderhax)
 - `slot0x18keyX.bin` (SHA-256: 76c76b655db85219c5d35d517ffaf7a43ebad66e31fbdd5743925937a893ccfc) (only needed when decrypting Secure3 on certain New3DS exclusive titles if you're using a Old3DS or if you're using arm9loaderhax)
 - `slot0x1BkeyX.bin` (SHA-256: 9a201e7c3737f3722e5b578d11837f197ca65bf52625b2690693e4165352c6bb) (only needed when decrypting Secure4 on certain New3DS exclusive titles on any console)
- `seeddb.bin` with the seed of the game (only needed if the rom came from the eShop and it isn't cryptofixed)
- A basic knowledge on how to use a terminal/command line

### Decrypting the rom

#### Preparations

If your rom is a .3ds, then just run `ncchinfo_gen_exefs.py` on it.
```
python ncchinfo_gen_exefs.py rom.3ds
```
It should generate a file named `ncchinfo.bin`. If the script only prints "Done!" or the `ncchinfo.bin` size is 16 bytes, then your rom may not be valid.

If your rom is a .cia, then you will have to extract the contents out of the CIA and then run `ncchinfo_gen_exefs.py` on the main content.
```
ctrtool --contents=contents rom.cia
```
This should create files starting with the same name specified in the `--contents` argument, if so, run `ncchinfo_gen_exefs.py` on `contents.0000.xxxxxxxx` (replace `xxxxxxxx` with the value in the filename).
If nothing was extracted, then the CIA your using may be invalid.
Also, if `ncchinfo_gen_exefs.py` only prints "Done!" or the `ncchinfo.bin` size is 16 bytes, then the cia your might be encrypted or something is wrong with it.

#### Generating the XORpads

Now it's time to generate the xorpads using Decrypt9, you need to place the `ncchinfo.bin`, `seeddb.bin` (only if rom is from the eShop), and the necessary KeyX files in the SD card, for the KeyX files place them in root of the SD card, and for `ncchinfo.bin` and `seeddb.bin`, if your using Archshift's Decrypt9 also place them in root of your SD card, if your using d0k3's Decrypt9WIP or Shadowtrance's Decrypt9UI place them inside `/Decrypt9` directory located in the root of SD card.
Start Decrypt9 from whatever entrypoint on your 3DS, go to XORpad Generator Options and select NCCH Padgen. If everything goes well, this will generate the XORpads to decrypt the rom exefs.
The XORpads will be placed in the root of SD card when using Archshift's Decrypt9, or inside `/Decrypt9` in the SD card when using d0k3's Decrypt9WIP or Shadowtrance's Decrypt9UI.

#### Applying the XORpads on the exefs

With the xorpads you can now start decrypting the rom exefs, if your rom is a .3ds, then you will have to extract the main partition, this can be done by running `3dstool`, if you have a .cia you can skip this command.
```
3dstool -xvt0f cci 0.cxi rom.3ds
```
This will extract the main partition to `0.cxi`, if you're working with a .cia you already have extracted the main content previously (`contents.0000.xxxxxxxx`) and you have to replace `0.cxi` with that file on the next command, now it's time to get the actual decrypted exefs with `3dstool` using the xorpads, so you may want to move them to the same directory as the rom.
If you only have one xorpad (.Main.exefs_norm.xorpad) run `3dstool` like this:
```
3dstool -xvtf cxi 0.cxi --exefs exefs.bin --exefs-xor 000400000XXXXX00.Main.exefs_norm.xorpad
```
If you have two xorpads (.Main.exefs_norm.xorpad and .Main.exefs_7x.xorpad) run `3dstool` like this instead:
```
3dstool -xvtf cxi 0.cxi --exefs exefs.bin --exefs-xor 000400000XXXXX00.Main.exefs_norm.xorpad --exefs-top-xor 000400000XXXXX00.Main.exefs_7x.xorpad
```
(Replace the crosses with the title id.)
If no mistakes where made or nothing failed, you should now have a decrypted `exefs.bin`.

### Extract code.bin and generate xml

Now this is the easy part, extract and decompress the `code.bin` can be done by running `ctrtool`:
```
ctrtool -t exefs --exefsdir=exefs --decompresscode exefs.bin
```
This should create a directory named exefs with the `code.bin` in it.
Now it's time to generate the xml by running smealum's `96crypto_dbgen.py` script.
```
python 96crypto_dbgen.py exefs/code.bin > 000400000XXXXX00.xml
```
(Replace the crosses with actual title id.)
This will print the offsets in to the xml.
