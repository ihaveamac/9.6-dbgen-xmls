# *hax 2.7 XML repository for 9.6-crypto titles
This is a repository containing XML files for use with *hax 2.7. They are game-specific offsets for titles that are using 9.6 encryption. These XML files should be placed at "mmap" at the root of your 3DS SD card.

## Generating the XMLs

### Requirements

- [Python 2.7](https://www.python.org)
- A 3DS with a version that allows for exploiting the arm9
- [Decrypt9 by Archshift](https://github.com/archshift/Decrypt9), [Decrypt9 WIP by d0k3](https://github.com/d0k3/Decrypt9) or [Decrypt9 UI by Shadowtrance](https://github.com/shadowtrance/Decrypt9)
- [3dstool](https://github.com/dnasdw/3dstool) and [ctrtool](https://github.com/profi200/Project_CTR)
- A rom of the game that uses 9.6 crypto
- [This modified script](https://gist.github.com/ihaveamac/304bb69e98fc4ce2d5c9) of ncchinfo_gen.py
- Certain key files (these files can not be linked here):
 - `slot0x25keyX.bin` (SHA-256: 7e878dde92938e4c717dd53d1ea35a75633f5130d8cfd7c76c8f4a8fb87050cd) (only needed when decrypting Secure2 if your 3DS in below version 7.0 or if you're using arm9loaderhax)
 - `slot0x18keyX.bin` (SHA-256: 76c76b655db85219c5d35d517ffaf7a43ebad66e31fbdd5743925937a893ccfc) (only needed when decrypting Secure3 on certain New3DS exclusive titles if you're using a Old3DS or if you're using arm9loaderhax)
 - `slot0x1BkeyX.bin` (SHA-256: 9a201e7c3737f3722e5b578d11837f197ca65bf52625b2690693e4165352c6bb) (only needed when decrypting Secure4 on certain New3DS exclusive titles on any console)
- A basic knowledge on how to use a terminal

### Decrypting the rom

If your rom is a .3ds, then just run `ncchinfo_gen_exefs.py` on it.
```
python ncchinfo_gen_exefs.py rom.3ds
```
It should generate a file named `ncchinfo.bin`. If the script only prints "Done!" or the `ncchinfo.bin` size is 16 bytes, then something is not right about the rom you're using.

If your rom is a .cia, then you will have to extract the contents out of the cia and then run `ncchinfo_gen_exefs.py` on the main content.
```
ctrtool --contents="filename" rom.cia
```
This should create files starting with the same name specified in the `--contents` argument, if so, run `ncchinfo_gen_exefs.bin` on `filename.0000.xxxxxxxx` (replace "filename" with the value of the --contents argument, and the x with whatever value is in the filename).
If nothing was 

