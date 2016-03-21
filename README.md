# \*hax 2.7 XML repository for 9.6-crypto titles
This is a repository containing XML files for use with *hax 2.7. They are game-specific offsets for titles that are using 9.6 encryption. These XML files should be placed at "mmap" at the root of your 3DS SD card.

## Generating the XMLs

### Requirements

- [Python 2.7](https://www.python.org/downloads/release/python-2711/)
- A 3DS with 9.2.0-20 or lower, or using arm9loaderhax
- [Decrypt9 by Archshift](https://github.com/archshift/Decrypt9), [Decrypt9WIP by d0k3](https://github.com/d0k3/Decrypt9) or [Decrypt9UI by Shadowtrance](https://github.com/shadowtrance/Decrypt9)
- [3dstool](https://github.com/dnasdw/3dstool) and [ctrtool](https://github.com/profi200/Project_CTR)
- A rom of the game that uses 9.6 crypto ([list of games with their respective seeds and title ids](http://pastebin.com/zNM8zYwa)), just make sure the game has not been patched by the community of hackers, for example rom hacks sometimes have a modified code.bin so don't use them.
- [This modified script](https://gist.github.com/ihaveamac/304bb69e98fc4ce2d5c9) of ncchinfo_gen.py
- Smealum's [96crypto_dbgen.py](https://github.com/smealum/ninjhax2.x/blob/master/scripts/96crypto_dbgen.py) script to generate the actual XMLs
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
Also, if `ncchinfo_gen_exefs.py` only prints "Done!" or the `ncchinfo.bin` size is 16 bytes, then the CIA your might be encrypted or something is wrong with it. If your CIA was generated from CDN content using FunkyCIA then it's encrypted.

##### Dealing with encrypted CIA files

If your CIA is encrypted and you can't generate a valid `ncchinfo.bin` because of that, then you'll have to decrypt the CIA directly using Decrypt9, and there are two methods of decrypting the CIA.

###### Method 1 - Deep CIA Decryption

This method is easy and will allow you to skip most of the upcoming parts, but can be time consuming if the CIA is big.

Place the CIA inside the `/D9Game` directory on the root of the SD card, the `seeddb.bin` inside `/Decrypt9` and the necessary KeyX files on the root of the SD.
Start Decrypt9 from whatever entrypoint on your 3DS, go to Game Decryptor Options and select CIA Decryptor (deep), after that you'll have to wait until it's finished.
If it fails, then there's something wrong with your CIA.

But if it's successful, then, since you have decrypted everything, you might as well extract the contents and the exefs.
```
ctrtool --contents=contents rom.cia

ctrtool --exefsdir=exefs --decompresscode contents.0000.xxxxxxxx
```
(Don't forget to replace `xxxxxxxx` with the value in the filename)
This should extract the decompressed `code.bin` out of the exefs on the main content and you can skip everything below up to "Extract code.bin and generate XML" and skip the first command.
* `--decompresscode` may not be needed here, however we don't know how older versions of ctrtool will react to it missing.

###### Method 2 - Shallow CIA Decryption

This method is also easy and it can be less time consuming, but you still have to do everything comming after this part.

Place the CIA inside the `/D9Game` directory on the root of the SD card.
Start Decrypt9 from whatever entrypoint on your 3DS, go to Game Decryptor Options and select CIA Decryptor (shallow), after that you'll have to wait until it's finished.
If it fails, then there's something wrong with your CIA.

But if it's successful, then extract the contents from the CIA and run `ncchinfo_gen_exefs.py` again.
```
ctrtool --contents=contents rom.cia

python ncchinfo_gen_exefs.py contents.0000.xxxxxxxx
```
(Don't forget to replace `xxxxxxxx` with the value in the filename)
This should create a file named `ncchinfo.bin`.
If `ncchinfo_gen_exefs.py` only prints "Done!" or the `ncchinfo.bin` size is 16 bytes, then there's something is wrong with your CIA.

#### Generating the XORpads

Now it's time to generate the xorpads using Decrypt9. First, you'll need to place both `ncchinfo.bin` and `seeddb.bin` (`seeddb.bin` is needed only if rom is from the eShop) inside the `/Decrypt9` directory located in the root of the SD card and the necessary KeyX files in the root of the SD card.
Start Decrypt9 from whatever entrypoint on your 3DS, go to XORpad Generator Options and select NCCH Padgen. If everything goes well, this will generate the XORpads to decrypt the rom exefs.
The XORpads will be placed inside `/Decrypt9` in the SD card.

#### Applying the XORpads on the exefs

With the xorpads you can now start decrypting the rom exefs. If your rom is a .3ds, you will have to extract the main partition. This can be done by running `3dstool` (if you have a .cia you can skip this command).
```
3dstool -xvt0f cci 0.cxi rom.3ds
```
This will extract the main partition to `0.cxi`, if you're working with a .cia you already have extracted the main content previously (`contents.0000.xxxxxxxx`) and you will have to replace `0.cxi` with that file on the next command. Now it's time to get the actual decrypted exefs with `3dstool` using the xorpads, so you may want to move them to the same directory as the rom.
If you only have one xorpad (.Main.exefs_norm.xorpad) run `3dstool` like this:
```
3dstool -xvtf cxi 0.cxi --exefs exefs.bin --exefs-xor 000400000XXXXX00.Main.exefs_norm.xorpad
```
If you have two xorpads (.Main.exefs_norm.xorpad and .Main.exefs_7x.xorpad) run `3dstool` like this instead:
```
3dstool -xvtf cxi 0.cxi --exefs exefs.bin --exefs-xor 000400000XXXXX00.Main.exefs_norm.xorpad --exefs-top-xor 000400000XXXXX00.Main.exefs_7x.xorpad
```
(Replace the crosses with the title id.)
If no mistakes were made or nothing failed, you should now have a decrypted `exefs.bin`.

### Extract code.bin and generate XML

Now this is the easy part, extract and decompress the `code.bin` can be done by running `ctrtool`:
```
ctrtool -t exefs --exefsdir=exefs --decompresscode exefs.bin
```
This should create a directory named exefs with the `code.bin` in it.
Now it's time to generate the XML by running smealum's `96crypto_dbgen.py` script.
```
python 96crypto_dbgen.py exefs/code.bin > 000400000XXXXX00.xml
```
(Replace the crosses with actual title id.)
This will print the offsets into the XML.

What's next? Well, technically, you're done, just place the XML on `/mmap` on root of the SD card.

But, if you can handle editing an XML, why don't you add a comment with the name of the game and region. Just add a line on the start of the XML that looks like this:
```
<!-- Name of Game (Region) -->
```
For example, if the game was "Animal Crossing: Happy Home Designer" the USA version, it would look like this:
```
<!-- Animal Crossing: Happy Home Designer (USA) -->
```

And while you're at it, why not make a pull request on Github, adding missing XMLs, if you know how. But if you don't how to make a pull request, there a lot of documentation on [Github help page](https://help.github.com/) with may help you.
