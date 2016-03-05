# 9.6-dbgen-xmls
xmls for 3DS 9.6 crypto titles

Using https://github.com/smealum/ninjhax2.x/blob/master/scripts/96crypto_dbgen.py to generate:
```
python 96crypto_dbgen.py decompressed_code.bin > tid.xml
```
`tid` would be something like 000400000014f100 (Animal Crossing: Happy Home Designer (USA))

`tid.xml` would then be copied to "mmap" at the root of the 3DS SD card.
