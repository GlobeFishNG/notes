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

## Remove obselote packages

```bash
$ sudo aptitude search ?obsolete
$ sudo aptitude purge ~o
```

## Remove log files

```bash
$ sudo apt-get install ncdu
$ sudo ncdu /var/log
```

## Disk Occupation GUI

```text
$ baobab
```

## Remove high-capacity packages

```text
$ sudo apt-get install debian-goodies
$ dpigs -H
```

