---
layout: post
title: Making a simple mysql powered haskell website
description: "Using Yesod and stack to make a mysql powered website"
category: 
tags: [Haskell]
---

# Step 0 - Install development dependencies

### [Install Stack](http://docs.haskellstack.org/en/stable/README.html#how-to-install)

### [Install Docker](http://docs.haskellstack.org/en/stable/README.html#how-to-install)

### Install MySQL client tools
Below are instructions or links for installing MySQL on Windows, OSX, and Ubuntu.

##### Windows

[Windows instructions to install mysql_config (untested)](http://dev.mysql.com/doc/refman/5.7/en/mysql-installer.html)

##### OSX (untested)


```
$ brew install mysql
$ # if you still have the issue, try explicitly adding the mysql bin folder to your path
$ export PATH=$PATH:/usr/local/mysql/bin
```

##### Ubuntu

```shell
/tmp/yesod-mysql-example λ sudo apt-get install libmysqlclient-dev 
[sudo] password for cody: 
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following additional packages will be installed:
  libmysqlclient20 mysql-common
The following NEW packages will be installed:
  libmysqlclient-dev libmysqlclient20 mysql-common
0 upgraded, 3 newly installed, 0 to remove and 121 not upgraded.
Need to get 0 B/1,982 kB of archives.
After this operation, 11.6 MB of additional disk space will be used.
Do you want to continue? [Y/n] y
debconf: unable to initialize frontend: Dialog
debconf: (Dialog frontend will not work on a dumb terminal, an emacs shell buffer, or without a controlling terminal.)
debconf: falling back to frontend: Readline
Selecting previously unselected package mysql-common.
(Reading database ... 223824 files and directories currently installed.)
Preparing to unpack .../mysql-common_5.7.13-0ubuntu0.16.04.2_all.deb ...
Unpacking mysql-common (5.7.13-0ubuntu0.16.04.2) ...
Selecting previously unselected package libmysqlclient20:amd64.
Preparing to unpack .../libmysqlclient20_5.7.13-0ubuntu0.16.04.2_amd64.deb ...
Unpacking libmysqlclient20:amd64 (5.7.13-0ubuntu0.16.04.2) ...
Selecting previously unselected package libmysqlclient-dev.
Preparing to unpack .../libmysqlclient-dev_5.7.13-0ubuntu0.16.04.2_amd64.deb ...
Unpacking libmysqlclient-dev (5.7.13-0ubuntu0.16.04.2) ...
Processing triggers for libc-bin (2.23-0ubuntu3) ...
Processing triggers for man-db (2.7.5-1) ...
Setting up mysql-common (5.7.13-0ubuntu0.16.04.2) ...
update-alternatives: using /etc/mysql/my.cnf.fallback to provide /etc/mysql/my.cnf (my.cnf) in auto mode
Setting up libmysqlclient20:amd64 (5.7.13-0ubuntu0.16.04.2) ...
Setting up libmysqlclient-dev (5.7.13-0ubuntu0.16.04.2) ...
Processing triggers for libc-bin (2.23-0ubuntu3) ...
```


### Spin up a mysql server to connect to

```
~/codygman.github.io:develop*? λ docker run -p 5432:5432 -docker run -p 5432:5432 --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:5.7.13-name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:5.7.13
... SNIP ...
2016-07-31T06:18:16.529512Z 0 [Warning] 'db' entry 'sys mysql.sys@localhost' ignored in --skip-name-resolve mode.
2016-07-31T06:18:16.529533Z 0 [Warning] 'proxies_priv' entry '@ root@localhost' ignored in --skip-name-resolve mode.
2016-07-31T06:18:16.531784Z 0 [Warning] 'tables_priv' entry 'sys_config mysql.sys@localhost' ignored in --skip-name-resolve mode.
2016-07-31T06:18:16.537936Z 0 [Note] Event Scheduler: Loaded 0 events
2016-07-31T06:18:16.538114Z 0 [Note] mysqld: ready for connections.
Version: '5.7.13'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server (GPL)
```

## Install PCRE

##### [Windows PCRE Install instructions](http://gnuwin32.sourceforge.net/packages/pcre.htm) (untested)

##### OSX PCRE Install Instructions

```shell
# OSX developer tools should be sufficient
xcode-select --install
# but if not...
brew install pcre
```

##### Ubuntu Install Instructions

```shell
/tmp/yesod-mysql-example λ sudo apt-get install libpcre3-dev 
[sudo] password for cody: 
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following packages were automatically installed and are no longer required:
  libcdio-cdda1 libcdio-paranoia1 libexpat1-dev libpython-all-dev libpython-dev libpython2.7-dev linux-headers-4.4.0-21
  linux-headers-4.4.0-21-generic linux-image-4.4.0-21-generic linux-image-extra-4.4.0-21-generic python2.7-dev
Use 'sudo apt autoremove' to remove them.
The following additional packages will be installed:
  libpcre32-3 libpcrecpp0v5
The following NEW packages will be installed:
  libpcre3-dev libpcre32-3 libpcrecpp0v5
0 upgraded, 3 newly installed, 0 to remove and 121 not upgraded.
Need to get 676 kB of archives.
After this operation, 2,979 kB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://us.archive.ubuntu.com/ubuntu xenial/main amd64 libpcrecpp0v5 amd64 2:8.38-3.1 [15.2 kB]
Get:2 http://us.archive.ubuntu.com/ubuntu xenial/main amd64 libpcre32-3 amd64 2:8.38-3.1 [136 kB]
Get:3 http://us.archive.ubuntu.com/ubuntu xenial/main amd64 libpcre3-dev amd64 2:8.38-3.1 [525 kB]
Fetched 676 kB in 0s (1,102 kB/s)
debconf: unable to initialize frontend: Dialog
debconf: (Dialog frontend will not work on a dumb terminal, an emacs shell buffer, or without a controlling terminal.)
debconf: falling back to frontend: Readline
Selecting previously unselected package libpcrecpp0v5:amd64.
(Reading database ... 256415 files and directories currently installed.)
Preparing to unpack .../libpcrecpp0v5_2%3a8.38-3.1_amd64.deb ...
Unpacking libpcrecpp0v5:amd64 (2:8.38-3.1) ...
Selecting previously unselected package libpcre32-3:amd64.
Preparing to unpack .../libpcre32-3_2%3a8.38-3.1_amd64.deb ...
Unpacking libpcre32-3:amd64 (2:8.38-3.1) ...
Selecting previously unselected package libpcre3-dev:amd64.
Preparing to unpack .../libpcre3-dev_2%3a8.38-3.1_amd64.deb ...
Unpacking libpcre3-dev:amd64 (2:8.38-3.1) ...
Processing triggers for libc-bin (2.23-0ubuntu3) ...
Processing triggers for man-db (2.7.5-1) ...
Setting up libpcrecpp0v5:amd64 (2:8.38-3.1) ...
Setting up libpcre32-3:amd64 (2:8.38-3.1) ...
Setting up libpcre3-dev:amd64 (2:8.38-3.1) ...
Processing triggers for libc-bin (2.23-0ubuntu3) ...
```

# Step 1 - Create and build project

```shell
/tmp λ stack new yesod-mysql-example yesod-mysql
Downloading template "yesod-mysql" to create project "yesod-mysql-example" in yesod-mysql-example/ ...
Looking for .cabal or package.yaml files to use to init the project.
Using cabal packages:
- yesod-mysql-example/yesod-mysql-example.cabal

Selecting the best among 8 snapshots...

* Matches lts-6.9

Selected resolver: lts-6.9
Initialising configuration using resolver: lts-6.9
Total number of user packages considered: 1
Writing configuration to file: yesod-mysql-example/stack.yaml
All done.
```

Then build your dependencies and turn on development server.

```shell
/tmp/yesod-mysql-example λ stack build 
No packages found in snapshot which provide a "hsc2hs" executable, which is a build-tool dependency of "skein"
Missing build-tools may be caused by dependencies of the build-tool being overridden by extra-deps.
This should be fixed soon - see this issue https://github.com/commercialhaskell/stack/issues/595

crypto-cipher-types-0.0.9: configure
free-4.12.4: download
crypto-cipher-types-0.0.9: build
crypto-random-0.0.9: configure
crypto-random-0.0.9: build
pcre-light-0.4.0.4: configure
pcre-light-0.4.0.4: build
free-4.12.4: configure
crypto-cipher-types-0.0.9: copy/register
free-4.12.4: build
cipher-aes-0.2.11: configure
crypto-random-0.0.9: copy/register
cipher-aes-0.2.11: build
pcre-light-0.4.0.4: copy/register
shakespeare-2.0.9: download
shakespeare-2.0.9: configure
mysql-simple-0.2.2.5: download
shakespeare-2.0.9: build
mysql-simple-0.2.2.5: configure
mysql-simple-0.2.2.5: build
cipher-aes-0.2.11: copy/register
cprng-aes-0.6.1: download
cprng-aes-0.6.1: configure
cprng-aes-0.6.1: build
cprng-aes-0.6.1: copy/register
nonce-1.0.2: download
nonce-1.0.2: configure
nonce-1.0.2: build
mysql-simple-0.2.2.5: copy/register
nonce-1.0.2: copy/register
silently-1.2.5: download
silently-1.2.5: configure
silently-1.2.5: build
simple-sendfile-0.2.25: download
simple-sendfile-0.2.25: configure
silently-1.2.5: copy/register
simple-sendfile-0.2.25: build
free-4.12.4: copy/register
skein-1.0.9.4: download
skein-1.0.9.4: configure
adjunctions-4.3: download
simple-sendfile-0.2.25: copy/register
skein-1.0.9.4: build
adjunctions-4.3: configure
keys-3.11: download
adjunctions-4.3: build
keys-3.11: configure
keys-3.11: build
skein-1.0.9.4: copy/register
adjunctions-4.3: copy/register
clientsession-0.9.1.2: download
clientsession-0.9.1.2: configure
kan-extensions-4.2.3: download
clientsession-0.9.1.2: build
kan-extensions-4.2.3: configure
keys-3.11: copy/register
kan-extensions-4.2.3: build
stm-chans-3.0.0.4: download
stm-chans-3.0.0.4: configure
clientsession-0.9.1.2: copy/register
stringsearch-0.3.6.6: download
stm-chans-3.0.0.4: build
stringsearch-0.3.6.6: configure
stringsearch-0.3.6.6: build
kan-extensions-4.2.3: copy/register
stm-chans-3.0.0.4: copy/register
pointed-4.2.0.2: download
pointed-4.2.0.2: configure
pointed-4.2.0.2: build
tagstream-conduit-0.5.5.3: download
tagstream-conduit-0.5.5.3: configure
tagstream-conduit-0.5.5.3: build
pointed-4.2.0.2: copy/register
tf-random-0.5: using precompiled package
QuickCheck-2.8.2: using precompiled package
alex-3.1.7: download
alex-3.1.7: configure
shakespeare-2.0.9: copy/register
unix-time-0.3.6: download
stringsearch-0.3.6.6: copy/register
alex-3.1.7: build
unix-time-0.3.6: configure
vault-0.3.0.6: download
unix-time-0.3.6: build
vault-0.3.0.6: configure
vault-0.3.0.6: build
tagstream-conduit-0.5.5.3: copy/register
unix-time-0.3.6: copy/register
authenticate-1.3.3.2: download
authenticate-1.3.3.2: configure
vault-0.3.0.6: copy/register
fast-logger-2.4.6: download
authenticate-1.3.3.2: build
fast-logger-2.4.6: configure
vector-algorithms-0.7.0.1: download
fast-logger-2.4.6: build
vector-algorithms-0.7.0.1: configure
alex-3.1.7: copy/register
vector-algorithms-0.7.0.1: build
language-javascript-0.6.0.8: download
language-javascript-0.6.0.8: configure
fast-logger-2.4.6: copy/register
language-javascript-0.6.0.8: build
monad-logger-0.3.19: download
monad-logger-0.3.19: configure
monad-logger-0.3.19: build
authenticate-1.3.3.2: copy/register
vector-instances-3.3.1: download
vector-instances-3.3.1: configure
vector-instances-3.3.1: build
monad-logger-0.3.19: copy/register
persistent-2.2.4.1: download
persistent-2.2.4.1: configure
persistent-2.2.4.1: build
vector-instances-3.3.1: copy/register
wai-3.2.1.1: download
wai-3.2.1.1: configure
wai-3.2.1.1: build
wai-3.2.1.1: copy/register
wai-logger-2.2.7: download
wai-logger-2.2.7: configure
wai-logger-2.2.7: build
wai-logger-2.2.7: copy/register
word8-0.1.2: download
word8-0.1.2: configure
word8-0.1.2: build
word8-0.1.2: copy/register
http2-1.6.1: download
http2-1.6.1: configure
http2-1.6.1: build
language-javascript-0.6.0.8: copy/register
hjsmin-0.2.0.1: download
hjsmin-0.2.0.1: configure
hjsmin-0.2.0.1: build
hjsmin-0.2.0.1: copy/register
wai-extra-3.0.16.1: download
wai-extra-3.0.16.1: configure
wai-extra-3.0.16.1: build
http2-1.6.1: copy/register
warp-3.2.7: download
warp-3.2.7: configure
warp-3.2.7: build
persistent-2.2.4.1: copy/register
persistent-mysql-2.3.0.2: download
persistent-mysql-2.3.0.2: configure
persistent-mysql-2.3.0.2: build
wai-extra-3.0.16.1: copy/register
persistent-template-2.1.8.1: download
persistent-template-2.1.8.1: configure
persistent-template-2.1.8.1: build
persistent-mysql-2.3.0.2: copy/register
xss-sanitize-0.3.5.7: download
xss-sanitize-0.3.5.7: configure
warp-3.2.7: copy/register
xss-sanitize-0.3.5.7: build
wai-app-static-3.1.5: download
wai-app-static-3.1.5: configure
wai-app-static-3.1.5: build
xss-sanitize-0.3.5.7: copy/register
yesod-core-1.4.22: download
yesod-core-1.4.22: configure
yesod-core-1.4.22: build
persistent-template-2.1.8.1: copy/register
wai-app-static-3.1.5: copy/register
yesod-core-1.4.22: copy/register
yesod-static-1.5.0.3: download
yesod-newsfeed-1.6: download
yesod-newsfeed-1.6: configure
yesod-persistent-1.4.0.5: download
yesod-newsfeed-1.6: build
yesod-static-1.5.0.3: configure
yesod-static-1.5.0.3: build
yesod-persistent-1.4.0.5: configure
yesod-persistent-1.4.0.5: build
yesod-newsfeed-1.6: copy/register
yesod-persistent-1.4.0.5: copy/register
yesod-form-1.4.7.1: download
yesod-form-1.4.7.1: configure
yesod-form-1.4.7.1: build
yesod-static-1.5.0.3: copy/register
yesod-form-1.4.7.1: copy/register
yesod-auth-1.4.13.3: download
yesod-auth-1.4.13.3: configure
yesod-auth-1.4.13.3: build
vector-algorithms-0.7.0.1: copy/register
mono-traversable-0.10.2: download
mono-traversable-0.10.2: configure
mono-traversable-0.10.2: build
yesod-auth-1.4.13.3: copy/register
yesod-1.4.3: download
yesod-1.4.3: configure
yesod-1.4.3: build
yesod-1.4.3: copy/register
mono-traversable-0.10.2: copy/register
mutable-containers-0.3.3: download
mutable-containers-0.3.3: configure
chunked-data-0.2.0: download
mutable-containers-0.3.3: build
chunked-data-0.2.0: configure
chunked-data-0.2.0: build
chunked-data-0.2.0: copy/register
mutable-containers-0.3.3: copy/register
conduit-combinators-1.0.4: download
conduit-combinators-1.0.4: configure
classy-prelude-0.12.8: download
conduit-combinators-1.0.4: build
classy-prelude-0.12.8: configure
classy-prelude-0.12.8: build
classy-prelude-0.12.8: copy/register
conduit-combinators-1.0.4: copy/register
classy-prelude-conduit-0.12.8: download
classy-prelude-conduit-0.12.8: configure
classy-prelude-conduit-0.12.8: build
classy-prelude-conduit-0.12.8: copy/register
classy-prelude-yesod-0.12.8: download
classy-prelude-yesod-0.12.8: configure
classy-prelude-yesod-0.12.8: build
classy-prelude-yesod-0.12.8: copy/register
yesod-mysql-example-0.0.0: configure
Configuring yesod-mysql-example-0.0.0...
yesod-mysql-example-0.0.0: build
Preprocessing library yesod-mysql-example-0.0.0...
[ 1 of 10] Compiling Model            ( Model.hs, .stack-work/dist/x86_64-linux/Cabal-1.22.5.0/build/Model.o )
[ 2 of 10] Compiling Settings         ( Settings.hs, .stack-work/dist/x86_64-linux/Cabal-1.22.5.0/build/Settings.o )
[ 3 of 10] Compiling Settings.StaticFiles ( Settings/StaticFiles.hs, .stack-work/dist/x86_64-linux/Cabal-1.22.5.0/build/Settings/StaticFiles.o )
[ 4 of 10] Compiling Import.NoFoundation ( Import/NoFoundation.hs, .stack-work/dist/x86_64-linux/Cabal-1.22.5.0/build/Import/NoFoundation.o )
[ 5 of 10] Compiling Foundation       ( Foundation.hs, .stack-work/dist/x86_64-linux/Cabal-1.22.5.0/build/Foundation.o )
[ 6 of 10] Compiling Import           ( Import.hs, .stack-work/dist/x86_64-linux/Cabal-1.22.5.0/build/Import.o )
[ 7 of 10] Compiling Handler.Common   ( Handler/Common.hs, .stack-work/dist/x86_64-linux/Cabal-1.22.5.0/build/Handler/Common.o )
[ 8 of 10] Compiling Handler.Home     ( Handler/Home.hs, .stack-work/dist/x86_64-linux/Cabal-1.22.5.0/build/Handler/Home.o )
[ 9 of 10] Compiling Handler.Comment  ( Handler/Comment.hs, .stack-work/dist/x86_64-linux/Cabal-1.22.5.0/build/Handler/Comment.o )
[10 of 10] Compiling Application      ( Application.hs, .stack-work/dist/x86_64-linux/Cabal-1.22.5.0/build/Application.o )
In-place registering yesod-mysql-example-0.0.0...
Preprocessing executable 'yesod-mysql-example' for
yesod-mysql-example-0.0.0...
[1 of 1] Compiling Main             ( app/main.hs, .stack-work/dist/x86_64-linux/Cabal-1.22.5.0/build/yesod-mysql-example/yesod-mysql-example-tmp/Main.o )
Linking .stack-work/dist/x86_64-linux/Cabal-1.22.5.0/build/yesod-mysql-example/yesod-mysql-example ...
yesod-mysql-example-0.0.0: copy/register
Installing library in
/tmp/yesod-mysql-example/.stack-work/install/x86_64-linux/lts-6.9/7.10.3/lib/x86_64-linux-ghc-7.10.3/yesod-mysql-example-0.0.0-AAzwVCdSrzMDlwTpvxrfz9
Installing executable(s) in
/tmp/yesod-mysql-example/.stack-work/install/x86_64-linux/lts-6.9/7.10.3/bin
Registering yesod-mysql-example-0.0.0...
Completed 58 action(s).
```

Almost forgot about installing yesod-bin for the development server that reloads as you modify files:

```shell
/tmp/yesod-mysql-example λ stack install yesod-bin
http-reverse-proxy-0.4.3: download
tar-0.5.0.3: download
project-template-0.2.0: download
http-reverse-proxy-0.4.3: configure
ghc-paths-0.1.0.9: download
http-reverse-proxy-0.4.3: build
project-template-0.2.0: configure
project-template-0.2.0: build
ghc-paths-0.1.0.9: configure
project-template-0.2.0: copy/register
http-reverse-proxy-0.4.3: copy/register
warp-tls-3.2.2: download
ghc-paths-0.1.0.9: build
tar-0.5.0.3: configure
ghc-paths-0.1.0.9: copy/register
tar-0.5.0.3: build
warp-tls-3.2.2: configure
warp-tls-3.2.2: build
warp-tls-3.2.2: copy/register
tar-0.5.0.3: copy/register
yesod-bin-1.4.18.2: download
yesod-bin-1.4.18.2: configure
yesod-bin-1.4.18.2: build
yesod-bin-1.4.18.2: copy/register
Completed 6 action(s).
Copying from /home/cody/.stack/snapshots/x86_64-linux/lts-6.9/7.10.3/bin/yesod to /home/cody/.local/bin/yesod
Copying from /home/cody/.stack/snapshots/x86_64-linux/lts-6.9/7.10.3/bin/yesod-ar-wrapper to /home/cody/.local/bin/yesod-ar-wrapper
Copying from /home/cody/.stack/snapshots/x86_64-linux/lts-6.9/7.10.3/bin/yesod-ghc-wrapper to /home/cody/.local/bin/yesod-ghc-wrapper
Copying from /home/cody/.stack/snapshots/x86_64-linux/lts-6.9/7.10.3/bin/yesod-ld-wrapper to /home/cody/.local/bin/yesod-ld-wrapper

Copied executables to /home/cody/.local/bin:
- yesod
- yesod-ar-wrapper
- yesod-ghc-wrapper
- yesod-ld-wrapper
```


# Step 2 - Start Development Server

```
/tmp/yesod-mysql-example λ stack exec yesod devel 
Yesod devel server. Type 'quit' to quit
Application can be accessed at:

http://localhost:3000
https://localhost:3443
If you wish to test https capabilities, you should set the following variable:
  export APPROOT=https://localhost:3443

Config file path source is default config file.
Config file /home/cody/.cabal/config not found.
Writing default configuration to /home/cody/.cabal/config
Warning: The package list for 'hackage.haskell.org' does not exist. Run 'cabal
update' to download it.
Resolving dependencies...
Configuring yesod-mysql-example-0.0.0...
Forcing recompile for ./Model.hs because of config/models
Forcing recompile for ./Foundation.hs because of config/routes
Forcing recompile for ./Foundation.hs because of templates/default-layout-wrapper.hamlet
Forcing recompile for ./Foundation.hs because of templates/default-layout.hamlet
Forcing recompile for ./Handler/Home.hs because of templates/homepage.hamlet
Forcing recompile for ./Handler/Home.hs because of templates/homepage.julius
Forcing recompile for ./Handler/Home.hs because of templates/homepage.lucius
Rebuilding application... (using cabal)
Starting development server...
Starting devel application
devel.hs: ConnectionError {errFunction = "connect", errNumber = 1045, errMessage = "Access denied for user 'yesod-mysql-example'@'localhost' (using password: YES)"}
Exit code: ExitFailure 1
```

Open up `config/settings.yml` and update the database section to look like this:

```yaml
database:
  user:     "_env:MYSQL_USER:root"
  password: "_env:MYSQL_PASSWORD:my-secret-pw"
  host:     "_env:MYSQL_HOST:localhost"
  port:     "_env:MYSQL_PORT:5432"
  # See config/test-settings.yml for an override during tests
  database: "_env:MYSQL_DATABASE:yesod-mysql-example"
  poolsize: "_env:MYSQL_POOLSIZE:10"
```

==================================================>>>>>>>>> Everything after this line needs updated/checked

7. Build dependencies

I already have a lot of things installed with stack, so I only had to install 4 dependencies. It's likely you'll have to install many more. Here is the command/output:

```
yesod-mysql-example λ stack build && yesod devel
mysql-0.1.1.8: configure
mysql-0.1.1.8: build
mysql-0.1.1.8: copy/register
mysql-simple-0.2.2.5: download
mysql-simple-0.2.2.5: configure
mysql-simple-0.2.2.5: build
mysql-simple-0.2.2.5: copy/register
persistent-mysql-2.3.0.2: download
persistent-mysql-2.3.0.2: configure
persistent-mysql-2.3.0.2: build
persistent-mysql-2.3.0.2: copy/register
yesod-mysql-example-0.0.0: configure
Configuring yesod-mysql-example-0.0.0...
yesod-mysql-example-0.0.0: build
Preprocessing library yesod-mysql-example-0.0.0...
[ 1 of 10] Compiling Model            ( Model.hs, .stack-work/dist/x86_64-linux/Cabal-1.22.5.0/build/Model.o )
[ 2 of 10] Compiling Settings         ( Settings.hs, .stack-work/dist/x86_64-linux/Cabal-1.22.5.0/build/Settings.o )
[ 3 of 10] Compiling Settings.StaticFiles ( Settings/StaticFiles.hs, .stack-work/dist/x86_64-linux/Cabal-1.22.5.0/build/Settings/StaticFiles.o )
[ 4 of 10] Compiling Import.NoFoundation ( Import/NoFoundation.hs, .stack-work/dist/x86_64-linux/Cabal-1.22.5.0/build/Import/NoFoundation.o )
[ 5 of 10] Compiling Foundation       ( Foundation.hs, .stack-work/dist/x86_64-linux/Cabal-1.22.5.0/build/Foundation.o )
[ 6 of 10] Compiling Import           ( Import.hs, .stack-work/dist/x86_64-linux/Cabal-1.22.5.0/build/Import.o )
[ 7 of 10] Compiling Handler.Common   ( Handler/Common.hs, .stack-work/dist/x86_64-linux/Cabal-1.22.5.0/build/Handler/Common.o )
[ 8 of 10] Compiling Handler.Home     ( Handler/Home.hs, .stack-work/dist/x86_64-linux/Cabal-1.22.5.0/build/Handler/Home.o )
[ 9 of 10] Compiling Handler.Comment  ( Handler/Comment.hs, .stack-work/dist/x86_64-linux/Cabal-1.22.5.0/build/Handler/Comment.o )
[10 of 10] Compiling Application      ( Application.hs, .stack-work/dist/x86_64-linux/Cabal-1.22.5.0/build/Application.o )
In-place registering yesod-mysql-example-0.0.0...
Preprocessing executable 'yesod-mysql-example' for
yesod-mysql-example-0.0.0...
[1 of 1] Compiling Main             ( app/main.hs, .stack-work/dist/x86_64-linux/Cabal-1.22.5.0/build/yesod-mysql-example/yesod-mysql-example-tmp/Main.o )
Linking .stack-work/dist/x86_64-linux/Cabal-1.22.5.0/build/yesod-mysql-example/yesod-mysql-example ...
yesod-mysql-example-0.0.0: copy/register
Installing library in
/tmp/yesod-mysql-example/.stack-work/install/x86_64-linux/nightly-2015-12-28/7.10.3/lib/x86_64-linux-ghc-7.10.3/yesod-mysql-example-0.0.0-2juE52fMYvUCp4cRRyhZwW
Installing executable(s) in
/tmp/yesod-mysql-example/.stack-work/install/x86_64-linux/nightly-2015-12-28/7.10.3/bin
Registering yesod-mysql-example-0.0.0...
Completed 4 action(s).
```
8. Install yesod-bin for development server
```shell
yesod-mysql-example λ stack install yesod-bin 
Copying from /home/cody/.stack/snapshots/x86_64-linux/nightly-2015-12-28/7.10.3/bin/yesod to /home/cody/.local/bin/yesod
Copying from /home/cody/.stack/snapshots/x86_64-linux/nightly-2015-12-28/7.10.3/bin/yesod-ar-wrapper to /home/cody/.local/bin/yesod-ar-wrapper
Copying from /home/cody/.stack/snapshots/x86_64-linux/nightly-2015-12-28/7.10.3/bin/yesod-ghc-wrapper to /home/cody/.local/bin/yesod-ghc-wrapper
Copying from /home/cody/.stack/snapshots/x86_64-linux/nightly-2015-12-28/7.10.3/bin/yesod-ld-wrapper to /home/cody/.local/bin/yesod-ld-wrapper

Copied executables to /home/cody/.local/bin:
- yesod-ld-wrapper
- yesod-ghc-wrapper
- yesod-ar-wrapper
- yesod
```
9. Create database
```shell
yesod-mysql-example λ mysql -uroot 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 4
Server version: 5.6.27-0ubuntu1 (Ubuntu)

Copyright (c) 2000, 2015, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> create database yesodmysqlexample;
create database yesodmysqlexample;
Query OK, 1 row affected (0.00 sec)

```

10. Update config/settings.yml with your database information

Your file should look like this:

```yml
# Values formatted like "_env:ENV_VAR_NAME:default_value" can be overridden by the specified environment variable.
# See https://github.com/yesodweb/yesod/wiki/Configuration#overriding-configuration-values-with-environment-variables

static-dir:     "_env:STATIC_DIR:static"
host:           "_env:HOST:*4" # any IPv4 host
port:           "_env:PORT:3000" # NB: The port `yesod devel` uses is distinct from this value. Set the `yesod devel` port from the command line.
ip-from-header: "_env:IP_FROM_HEADER:false"

# Default behavior: determine the application root from the request headers.
# Uncomment to set an explicit approot
#approot:        "_env:APPROOT:http://localhost:3000"

# Optional values with the following production defaults.
# In development, they default to the inverse.
#
# development: false
# detailed-logging: false
# should-log-all: false
# reload-templates: false
# mutable-static: false
# skip-combining: false

# NB: If you need a numeric value (e.g. 123) to parse as a String, wrap it in single quotes (e.g. "_env:PGPASS:'123'")
# See https://github.com/yesodweb/yesod/wiki/Configuration#parsing-numeric-values-as-strings

database:
  user:     "_env:MYSQL_USER:root"
  password: "_env:MYSQL_PASSWORD:"
  host:     "_env:MYSQL_HOST:localhost"
  port:     "_env:MYSQL_PORT:3306"
  database: "_env:MYSQL_DATABASE:yesodmysqlexample"
  poolsize: "_env:MYSQL_POOLSIZE:10"

copyright: Insert copyright statement here
#analytics: UA-YOURCODE
```

. Recompile on filechange

```shell
yesod-mysql-example λ stack build --file-watch
ExitSuccess
Type help for available commands. Press enter to force a rebuild.
help 
Unknown command: "help ". Try 'help'
help

help: display this help
quit: exit
build: force a rebuild
watched: display watched files
watched
/home/cody/.stack/programs/x86_64-linux/ghc-7.10.3/lib/ghc-7.10.3/include/ghcversion.h
/tmp/yesod-mysql-example/.stack-work/dist/x86_64-linux/Cabal-1.22.5.0/build/autogen/cabal_macros.h
/tmp/yesod-mysql-example/Application.hs
/tmp/yesod-mysql-example/Foundation.hs
/tmp/yesod-mysql-example/Handler/Comment.hs
/tmp/yesod-mysql-example/Handler/Common.hs
/tmp/yesod-mysql-example/Handler/Home.hs
/tmp/yesod-mysql-example/Import.hs
/tmp/yesod-mysql-example/Import/NoFoundation.hs
/tmp/yesod-mysql-example/Model.hs
/tmp/yesod-mysql-example/Settings.hs
/tmp/yesod-mysql-example/Settings/StaticFiles.hs
/tmp/yesod-mysql-example/app/main.hs
/tmp/yesod-mysql-example/config/favicon.ico
/tmp/yesod-mysql-example/config/models
/tmp/yesod-mysql-example/config/robots.txt
/tmp/yesod-mysql-example/config/routes
/tmp/yesod-mysql-example/config/settings.yml
/tmp/yesod-mysql-example/stack.yaml
/tmp/yesod-mysql-example/templates/default-layout-wrapper.hamlet
/tmp/yesod-mysql-example/templates/default-layout.hamlet
/tmp/yesod-mysql-example/templates/homepage.hamlet
/tmp/yesod-mysql-example/templates/homepage.julius
/tmp/yesod-mysql-example/templates/homepage.lucius
/tmp/yesod-mysql-example/test/Spec.hs
/tmp/yesod-mysql-example/yesod-mysql-example.cabal
/usr/include/stdc-predef.h
```
10. Start development server

```shell
yesod-mysql-example λ stack exec -- yesod devel 
Yesod devel server. Type 'quit' to quit
Application can be accessed at:

http://localhost:3000
https://localhost:3443
If you wish to test https capabilities, you should set the following variable:
  export APPROOT=https://localhost:3443

Resolving dependencies...
Configuring yesod-mysql-example-0.0.0...
Forcing recompile for ./Model.hs because of config/models
Forcing recompile for ./Foundation.hs because of config/routes
Forcing recompile for ./Foundation.hs because of templates/default-layout-wrapper.hamlet
Forcing recompile for ./Foundation.hs because of templates/default-layout.hamlet
Forcing recompile for ./Handler/Home.hs because of templates/homepage.hamlet
Forcing recompile for ./Handler/Home.hs because of templates/homepage.julius
Forcing recompile for ./Handler/Home.hs because of templates/homepage.lucius
Rebuilding application... (using cabal)
Starting development server...
Starting devel application
Migrating: CREATe TABLE `user`(`id` BIGINT NOT NULL AUTO_INCREMENT PRIMARY KEY,`ident` TEXT CHARACTER SET utf8 NOT NULL,`password` TEXT CHARACTER SET utf8 NULL)
29/Dec/2015:23:28:29 -0600 [Debug#SQL] CREATe TABLE `user`(`id` BIGINT NOT NULL AUTO_INCREMENT PRIMARY KEY,`ident` TEXT CHARACTER SET utf8 NOT NULL,`password` TEXT CHARACTER SET utf8 NULL); []
Migrating: CREATe TABLE `email`(`id` BIGINT NOT NULL AUTO_INCREMENT PRIMARY KEY,`email` TEXT CHARACTER SET utf8 NOT NULL,`user_id` BIGINT NULL REFERENCES `user`,`verkey` TEXT CHARACTER SET utf8 NULL)
Migrating: CREATe TABLE `comment`(`id` BIGINT NOT NULL AUTO_INCREMENT PRIMARY KEY,`message` TEXT CHARACTER SET utf8 NOT NULL,`user_id` BIGINT NULL REFERENCES `user`)
Migrating: ALTER TABLE `user` ADD CONSTRAINT `unique_user` UNIQUE(`ident`(200))
Mi29/Dec/2015:23:28:29 -0600 [Debug#SQL] CREATe TABLE `email`(`id` BIGINT NOT NULL AUTO_INCREMENT PRIMARY KEY,`email` TEXT CHARACTER SET utf8 NOT NULL,`user_id` BIGINT NULL REFERENCES `user`,`verkey` TEXT CHARACTER SET utf8 NULL); []
29/Dec/2015:23:28:29 -0600 [Debug#SQL] CREATe TABLE `comment`(`id` BIGINT NOT NULL AUTO_INCREMENT PRIMARY KEY,`message` TEXT CHARACTER SET utf8 NOT NULL,`user_id` BIGINT NULL REFERENCES `user`); []
29/Dec/2015:23:28:31 -0600 [Debug#SQL] ALTER TABLE `user` ADD CONSTRAINT `unique_user` UNIQUE(`ident`(200)); []
grating: ALTER TABLE `email` ADD CONSTRAINT `unique_email` UNIQUE(`email`(200))
Migrating: ALTER TABLE `email` ADD CONSTRAINT `email_user_id_fkey` FOREIGN KEY(`user_id`) REFERENCES `user`(`id`)
Mig29/Dec/2015:23:28:31 -0600 [Debug#SQL] ALTER TABLE `email` ADD CONSTRAINT `unique_email` UNIQUE(`email`(200)); []
29/Dec/2015:23:28:31 -0600 [Debug#SQL] ALTER TABLE `email` ADD CONSTRAINT `email_user_id_fkey` FOREIGN KEY(`user_id`) REFERENCES `user`(`id`); []
rating: ALTER TABLE `comment` ADD CONSTRAINT `comment_user_id_fkey` FOREIGN KEY(`user_id`) REFERENCES `user`(`id`)
Devel application launched: http://localhost:3000
29/Dec/2015:23:28:32 -0600 [Debug#SQL] ALTER TABLE `comment` ADD CONSTRAINT `comment_user_id_fkey` FOREIGN KEY(`user_id`) REFERENCES `user`(`id`); []
```

11. Make a comment form

Let's import withLargeInput, modify your imports section to look like this:

```haskell
import Import
import Yesod.Form.Bootstrap3 (BootstrapFormLayout (..), renderBootstrap3,
                              withSmallInput, withLargeInput)
import Text.Julius (RawJS (..))
```

Right below sampleForm, create a UserComment datatype and commentForm:

```haskell
data UserComment = UserComment { commentText :: Text } deriving Show

commentAForm :: AForm Handler UserComment
commentAForm = UserComment
    <$> areq textField "comment-text" Nothing 

commentForm :: Html -> MForm Handler (FormResult UserComment, Widget)
commentForm = renderBootstrap3 BootstrapBasicForm commentAForm
```

12. Modify getHomeR and postHomeR

See if you can spot the modifications(hint look for commentForm):

```haskell
getHomeR :: Handler Html
getHomeR = do
    (formWidget, formEnctype) <- generateFormPost sampleForm
    (commentFormWidget, commentFormEnctype) <- generateFormPost commentForm

    let submission = Nothing :: Maybe (FileInfo, Text)
        handlerName = "getHomeR" :: Text

    let userComment = Nothing :: Maybe UserComment

    defaultLayout $ do
        let (commentFormId, commentTextareaId, commentListId) = commentIds
        aDomId <- newIdent
        setTitle "Welcome To Yesod!"
        $(widgetFile "homepage")

postHomeR :: Handler Html
postHomeR = do
    ((result, formWidget), formEnctype) <- runFormPost sampleForm
    ((commentResult, commentFormWidget), commentFormEnctype) <- runFormPost commentForm
    let handlerName = "postHomeR" :: Text
        submission = case result of
            FormSuccess res -> Just res
            _ -> Nothing
        userComment = case (commentResult :: FormResult UserComment) of
          FormSuccess res -> Just res
          _ -> Nothing

    defaultLayout $ do
        let (commentFormId, commentTextareaId, commentListId) = commentIds
        aDomId <- newIdent
        setTitle "Welcome To Yesod!"
        $(widgetFile "homepage")
```

13. Update the homepage.hamlet template

We can see that the trivial form example looks like this:

```haskell
<section.page-header>
  <h2>Forms

  <div>
    This is an example trivial Form. Read the
    <a href="http://www.yesodweb.com/book/forms">Forms chapter<span class="glyphicon glyphicon-bookmark"></span></a> #
    on the yesod book to learn more about them.
    $maybe (info,con) <- submission
      <div .message .alert .alert-success>
        Your file's type was <em>#{fileContentType info}</em>. You say it has: <em>#{con}</em>
    <form method=post action=@{HomeR}#form enctype=#{formEnctype}>
      ^{formWidget}
      <button .btn .btn-primary type="submit">
         Send it! <span class="glyphicon glyphicon-upload"></span>
```

We'll just copy that and paste the copy right under it, then modify it to look like this:

```haskell
<section.page-header>
  <h2>My comment form
  $maybe (uc) <- userComment
      <div .message .alert .alert-success>
        Your comment: #{commentText uc}
  <form method=post action=@{HomeR}#form enctype=#{commentFormEnctype}>
    ^{commentFormWidget}
    <button .btn .btn-primary type="submit">
      Comment
```

14. Write comments to the database

To be continued
