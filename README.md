# *hax 2.7 XML repository for 9.6-crypto titles
This is a repository containing XML files for use with *hax 2.7. They are game-specific offsets for titles that are using [seed encryption](https://3dbrew.org/wiki/Filesystem_services#SEEDDB) added in 9.6.0-24. These XML files should be placed at "mmap" at the root of your 3DS SD card.

## Generating the XMLs

A full guide on how to decrypt and extract a game to generate an XML can be found at the wiki: https://github.com/ihaveamac/9.6-dbgen-xmls/wiki

If you already know how to decrypt and extract a game, use [`96crypto_dbgen.py`](https://github.com/smealum/ninjhax2.x/blob/master/scripts/96crypto_dbgen.py) to generate:
```bash
python 96crypto_dbgen.py decompressed_code.bin > tid.xml
```
`tid` would be something like 000400000014F100 (Animal Crossing: Happy Home Designer (USA))

`tid.xml` would then be copied to "mmap" on the SD card.

To add to the repository, add the game title to the first line of the XML, like this:
```xml
<!-- Animal Crossing: Happy Home Designer (USA) -->
```
Create a pull request to add a file.
