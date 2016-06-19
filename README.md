# tw
Trailing whitespace detector `tw` command-line utility scans a file and
tests each line for whitespace characters at its end.  The utility processes
only one line at a time and therefore can be used with files that contain
arbitrarily many lines.

## Usage
The utility accepts one or zero arguments: when the argument count is zero,
`stdin` is used, otherwise the argument is used as a path to a file.

### Synopsis
```
tw [file]
```

## Return code
The `tw` utility returns `1` if any lines with trailing whitespace were found,
`0` otherwise.

## Example
Following example examines two files - `stdlib.h` and `stdio.h` - that were
shipped with the FreeBSD operating system version 10.0. Apparently, `stdlib.h`
contains one line with trailing whitespace, which is further proved by a small
`sed` script that quotes the incriminated line.

```sh
$ freebsd-version
10.0-RELEASE
$ tw /usr/include/stdlib.h
258
$ echo $?
1
$ sed -n '258s/\(.*\)/"\1"/p' /usr/include/stdlib.h
"__uint32_t "
$ tw /usr/include/stdio.h
$ echo $?
0
```

## Performance & memory
As mentioned above, the `tw` utility processes each file line by line and
therefore is able to work with files containing arbitrary large number of
files. To test this on an extreme example, the following commands find and
examine the largest header file found by default in the FreeBSD installation:

```sh
$ find /usr -type f -name '*.h' -exec wc -l {} \; | sort -nr -k1 | head -n 3
   83644 /usr/src/sys/contrib/dev/ath/ath_hal/ar9300/osprey_reg_map_macro.h
   66519 /usr/src/sys/contrib/dev/ath/ath_hal/ar9300/scorpion_reg_map_macro.h
   42380 /usr/src/sys/dev/cxgbe/common/t4_regs.h

$ du -h /usr/src/sys/contrib/dev/ath/ath_hal/ar9300/osprey_reg_map_macro.h | cut -f1
3.9M
```

To measure the impact on performance and memory consumption, the `-s` option
can be passed to the Real-time Scheduler:

```sh
$ tw +RTS -s -RTS /usr/src/sys/contrib/dev/ath/ath_hal/ar9300/osprey_reg_map_macro.h

```



## Build & install
To compile the utility, use the simple `make` command:
```sh
$ make
$ ls -l ./tw
-rwxr-xr-x  1 root  wheel  1877008 Jun 19 02:52 ./tw 
```

## License
2-clause BSD license. For more information please consult the
[LICENSE](LICENSE.md) file. In the case that you need a different license, feel
free to contact me.

## Author
Daniel Lovasko (daniel.lovasko@gmail.org)
