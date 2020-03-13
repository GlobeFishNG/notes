# Clean up packages

## Remove remnant configuration 

```bash
$ dpkg --list | grep "^rc"
$ dpkg --list | grep "^rc" | cut -d " " -f 3
$ dpkg --list | grep "^rc" | cut -d " " -f 3 | xargs sudo dpkg --purge
```

## Remove orphan packages

```bash
$ sudo apt-get install deborphan
$ deborphan | xargs sudo apt-get purge -y
```

