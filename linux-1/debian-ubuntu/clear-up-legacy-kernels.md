# Clean up legacy kernels

## Check current kernel version

```bash
$ uname -a
```

## Check which kernels have been installed

```bash
$ dpkg --get-selections | grep linux
```

## Remove legacy kernels

```bash
$ sudo apt-get remove linux-image-*.*.*-**
$ sudo apt-get remove linux-headers-*.*.*-**
```

