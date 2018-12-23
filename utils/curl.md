[TOC]

# CURL Common Usages

[Reference](https://www.thegeekstuff.com/2012/04/curl-examples/)

## Download a single file

> Download a single file and print it into standard console output by default.

```bash
$ curl http://www.centos.org
<html>
<head><title>301 Moved Permanently</title></head>
<body bgcolor="white">
<center><h1>301 Moved Permanently</h1></center>
<hr><center>nginx/1.12.2</center>
</body>
</html>
```

> Use shell redirection to output into another file or pipe.
>

```bash
$ curl http://www.centos.org > centos.html
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   185  100   185    0     0    333      0 --:--:-- --:--:-- --:--:--   335
$ cat centos.html
<html>
<head><title>301 Moved Permanently</title></head>
<body bgcolor="white">
<center><h1>301 Moved Permanently</h1></center>
<hr><center>nginx/1.12.2</center>
</body>
</html>
```

## Specify target file name

## Save downloaded content to a specific file by using `-o/-O`

> 1. -o：specify target file name
> 2. -O：use default file name

```bash
$ curl -o mygettext.html http://www.gnu.org/software/gettext/manual/gettext.html
$ curl -O http://www.gnu.org/software/gettext/manual/gettext.html
```

## Fetch multiple files at a time

```bash
curl -O URL1 -O URL2
```

> If `CURL` is going to download multiple files from one single site, it will try to reuse the connection.

## Use `-L` to follow HTTP Location Header

> By default, `CURL` will not follow HTTP `Location` header. 

```bash
$ curl http://www.google.com
<HTML>
<HEAD>
    <meta http-equiv="content-type" content="text/html;charset=utf-8">
    <TITLE>302 Moved</TITLE>
</HEAD>
<BODY>
    <H1>302 Moved</H1>
    The document has moved
    <A HREF="http://www.google.com.hk/url?sa=p&amp;hl=zh-CN&amp;pref=hkredirect&amp;pval=yes&amp;q=http://www.google.com.hk/&amp;ust=1379402837567135amp;usg=AFQjCNF3o7umf3jyJpNDPuF7KTibavE4aA">here</A>.
</BODY>
</HTML>
```

> Use `-L` to force `CURL` to follow the redirection.
>

```bash
$ curl -L http://www.google.com
```

## Breakpoint continuation

> Use `-C` to continue/resume the download of a large file .
>

```bash
$ curl -O http://www.gnu.org/software/gettext/manual/gettext.html
##############             20.1%
$ curl -C - -O http://www.gnu.org/software/gettext/manual/gettext.html
###############            21.1%
```

## Bandwidth Limitation

> Use `--limit-rate` to limit the max bandwidth that `CURL` can occupy.
>

```bash
# Max downloading rate: 1000B/s
$ curl --limit-rate 1000B -O http://www.gnu.org/software/gettext/manual/gettext.html
```

## Download files that are updated within specific time

> Use `-z` to download a file updated with specific time.

```bash
# Download yy.html if it was update after Dec 21, 2011.
$ curl -z 21-Dec-11 http://www.example.com/yy.html
# Download yy.html if it was update before Dec 21, 2011.
$ curl -z -21-Dec-11 http://www.example.com/yy.html
```

## Authorization

> Use `-u` to provide username and password.
>

```bash
$ curl -u username:password URL
# A safer practice is to type the usename in the command line and then to type the password in the prompt. It can avoid leaks of passwords by look up the command history. 
$ curl -u username URL
```

## Download files from FTP Services

```bash
 # List all files and folders under public_html
 $ curl -u ftpuser:ftppass -O ftp://ftp_server/public_html/
 
 # Download xss.php
 curl -u ftpuser:ftppass -O ftp://ftp_server/public_html/xss.php
```

## Upload files to FTP Services

> Use ` -T` to upload specific local files to FTP services.
>

```bash
# Upload myfile.txt
$ curl -u ftpuser:ftppass -T myfile.txt ftp://ftp.testserver.com
# Upload multiple files
curl -u ftpuser:ftppass -T "{file1,file2}" ftp://ftp.testserver.com
# Get from stdin and then upload.
curl -u ftpuser:ftppass -T - ftp://ftp.testserver.com/myfile_1.txt
```

## List/Download using Ranges

```bash
$ curl   ftp://ftp.uk.debian.org/debian/pool/main/[a-z]/
```

## More information

> Use `-v`  and `-trace` to get more information about connection, request and response.
>

## Look up words in `dict.org`

```bash
# Look up the word bash
$ curl dict://dict.org/d:bash
# List all avaiable dicts
$ curl dict://dict.org/show:db
# Look up the word hash in folddoc dict.
$ curl dict://dict.org/d:bash:foldoc
```

## Set up proxy for `CURL`

> Use `-x` to set up proxy
>

```bash
$ curl -x proxysever.test.com:3128 http://google.co.in
```

## Store and use cookies

```bash
 # Save cookies into sugarcookies
 $ curl -D sugarcookies http://localhost/sugarcrm/index.php
 # Use saved cookie
 $ curl -b sugarcookies http://localhost/sugarcrm/index.php
```

## Transfer request data

> Use `--data/-d` to use POST method
>

```bash
# GET
$ curl -u username https://api.github.com/user?access_token=XXXXXXXXXX
# POST
$ curl -u username --data "param1=value1&param2=value" https://api.github.com
# Specify a file as the post data
$ curl --data @filename https://github.api.com/authorizations
```

> Use `--data-urlencode` to escape specific characters.
>

```bash
curl -d "value%201" http://hostname.com
curl --data-urlencode "value 1" http://hostname.com
```

> Use `-X` to specify other protocol method
>

```bash
curl -I -X DELETE https://api.github.cim
```

> Use `--form` to transfer form data

```bash
curl --form "fileupload=@filename.txt" http://hostname/resource
```