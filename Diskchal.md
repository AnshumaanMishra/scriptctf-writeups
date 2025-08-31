# Description:

Author: Connor Chang

I accidentally vanished my flag, can u find it for me

#forensics

# Solution:
The file given to us is called `stick.img`
This contains the snapshot of all the files on a disk at a given time.
This usually means we will need to either `mount it`, or use `sleuthkit`(or both).

The problem states that the required file was *vanished*. If this means deleted, simply mounting it won't help, as even if we mount it, the file remains hidden.

When a file is deleted, it isn't actually deleted, rather, its just hidden from access. Why? Because deletion of a file, isn't actually defined, deleting a file just deletes its reference, so it does not show up on regular file explorers. We cannot delete a file, we can only overwrite it with another value.

This is how recovery software work. They try to recover whatever is possible from the bits that have not been overwritten yet. If some bits are overwritten, they deliver a corrupted file.

Let us first try to mount the image. To mount the image we create a folder and then mount it onto it using 
```shell title=mount
mkdir ./stick     
sudo mount -o loop stick.img /mnt/stick
ls -la ./stick
```
Checking the disk structure, we find:
```sh title=list
./stick/:
total 6
drwxr-xr-x 2 root      root       512 Jan  1  1970 ./
drwxrwxr-x 4 anshumaan anshumaan 4096 Aug 30 18:17 ../
-rwxr-xr-x 1 root      root        76 Jul 18 03:57 notes.txt*
-rwxr-xr-x 1 root      root        56 Jul 18 03:57 random_thoughts.txt
```

Upon checking, we find that none of these files has the flag, which track since we know the flag has *vanished*.

This means, the file has been deleted. We will now analyse the addresses on the disk to check for a file.
To analyze the disk, we use the `sleuthkit` package.

1. We need to find the `filesystem` of the image. To do that we use the `file` command.
```sh title=file
$ file stick.img                                                            
stick.img: DOS/MBR boot sector, code offset 0x58+2, OEM-ID "mkfs.fat", sectors 49152 (volumes <=32 MB), Media descriptor 0xf8, sectors/track 32, heads 4, FAT (32 bit), sectors/FAT 378, reserved 0x1, serial number 0x8caae860, unlabeled
```

2. We find out that the `filesystem` is `fat32`.
3. Now we use the `fls` command of the `sleuthkit` package to check for stray files:
```sh title=fls
$ fls -f fat32 stick.img
r/r 4:	notes.txt
r/r 7:	random_thoughts.txt
r/r * 10:	secret_magic_collection.gz
v/v 773827:	$MBR
v/v 773828:	$FAT1
v/v 773829:	$FAT2
V/V 773830:	$OrphanFiles
```
4. The `*` next to the address 10 denotes that the file was deleted, but, fortunately, not overwritten.
5. We now access the contents of this file using the `icat` command:
```sh title=icat
icat -f fat32 stick.img 10 > secret_magic_collection.gz
```
6. This command redirects whatever it got to a file by the name of `secret_magic_collection.gz`
7. Now we unzip it and see the contents:
```sh title=getFlag
$ gunzip secret_magic_collection.gz                                         
$ cat secret_magic_collection
scriptCTF{1_l0v3_m461c_7r1ck5}
```

Viola!!!