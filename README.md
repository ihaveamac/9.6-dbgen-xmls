# \*hax 2.7 mmap XML repository for 9.6-crypto titles
This is a repository containing XML files with custom memory mapping for use with \*hax 2.7. They are game-specific offsets primarily for titles that are using [seed encryption](https://3dbrew.org/wiki/Filesystem_services#SEEDDB) added in 9.6.0-24.

## Using the XMLs
1. To quickly download all the XMLs, click "Clone or download" then "[Download ZIP](https://github.com/ihaveamac/9.6-dbgen-xmls/archive/master.zip)" near the top of this page.
2. Place the `mmap` folder at the root of your 3DS SD card.
3. Place `boot.3dsx` on at the root of the SD card, replacing any existing one.

<img src="https://github.com/ihaveamac/ihaveamac.github.io/raw/master/downloadzip.png" width="360" height="186">  
[`new_boot.3dsx`](https://github.com/ihaveamac/9.6-dbgen-xmls/blob/master/new_boot.3dsx) is rehosted from the [Homebrew Launcher site](http://smealum.github.io/3ds/) and starter kit, and is required to launch titles with these XMLs.

## Probably Frequently Asked Questions
#### I keep getting a red screen when trying to use a 9.6+ title
* You might not be using the latest Homebrew Launcher build in the starter kit or this repository. mashers's Grid Launcher currently don't support custom memmaps.
* You might not have the XML for the title. Search for its name or title ID in the repository.
* Check to see that you're using \*hax 2.7(the version is usually displayed when starting it). Update the payload and try again.
* The XML might not exist in the repo yet.

#### I keep getting a yellow screen when trying to use a 9.6+ title
* \*hax's default addresses are sometimes incorrect and can cause a 100% reliable crash. Custom memmaps could be created to fix these titles([see this comment thread](https://github.com/ihaveamac/9.6-dbgen-xmls/commit/9b80e89de4ce0e8c03cd03deb23b7a6548d6b2da#commitcomment-17429710)).

#### HANS keeps crashing when trying to use a 9.6+ title
* HANS currently doesn't support custom memmaps at this time.

## Generating the XMLs
A full guide on how to decrypt and extract a game to generate an XML can be found at the wiki: https://github.com/ihaveamac/9.6-dbgen-xmls/wiki

If you already know how to decrypt and extract a game, use [`96crypto_dbgen.py`](https://github.com/smealum/ninjhax2.x/blob/master/scripts/96crypto_dbgen.py) to generate:
```bash
python 96crypto_dbgen.py decompressed_code.bin > tid.xml
```
`tid` would be something like 000400000014F100 (Animal Crossing: Happy Home Designer (USA))

`tid.xml` would then be copied to "mmap" on the SD card.

To add to the repository, add the game title and region to the first line of the XML, like this:
```xml
<!-- Animal Crossing: Happy Home Designer (USA) -->
```
Create a pull request to add a file.
