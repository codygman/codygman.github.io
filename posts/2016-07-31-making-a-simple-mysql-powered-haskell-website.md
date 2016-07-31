---
layout: post
title: Making a simple mysql powered haskell website
description: "Using Yesod and stack to make a mysql powered website"
category: 
tags: [Haskell]
---

( unfinished, no mysql yet :S )

1. [Install stack](http://docs.haskellstack.org/en/stable/README.html#how-to-install)
2. [Install docker]
3. Create your project
```shell
tmp λ stack new yesod-mysql-example yesod-mysql
Downloading template "yesod-mysql" to create project "yesod-mysql-example" in yesod-mysql-example/ ...
Writing default config file to: /tmp/yesod-mysql-example/stack.yaml
Basing on cabal files:
- /tmp/yesod-mysql-example/yesod-mysql-example.cabal

Checking against build plan lts-3.19

* Build plan did not match your requirements:
    yesod-test version 1.4.4 found
    - yesod-mysql-example requires >=1.5.0.1 && <1.6


Checking against build plan lts-3.18

* Build plan did not match your requirements:
    yesod-test version 1.4.4 found
    - yesod-mysql-example requires >=1.5.0.1 && <1.6


Checking against build plan nightly-2015-12-28
Selected resolver: nightly-2015-12-28
Wrote project config to: /tmp/yesod-mysql-example/stack.yaml
```
4. build your dependencies and turn on development server
```shell
yesod-mysql-example λ cd ..
tmp λ cd yesod-mysql-example; stack build && yesod devel
mysql-0.1.1.8: download
blaze-textual-0.2.1.0: download
blaze-textual-0.2.1.0: configure
pcre-light-0.4.0.4: download
blaze-textual-0.2.1.0: build
mysql-0.1.1.8: configure
blaze-textual-0.2.1.0: copy/register
pcre-light-0.4.0.4: configure
pcre-light-0.4.0.4: build
pcre-light-0.4.0.4: copy/register
Progress: 3
--  While building package mysql-0.1.1.8 using:
      /tmp/stack6322/mysql-0.1.1.8/.stack-work/dist/x86_64-linux/Cabal-1.22.5.0/setup/setup --builddir=.stack-work/dist/x86_64-linux/Cabal-1.22.5.0 configure --with-ghc=/home/cody/.stack/programs/x86_64-linux/ghc-7.10.3/bin/ghc --with-ghc-pkg=/home/cody/.stack/programs/x86_64-linux/ghc-7.10.3/bin/ghc-pkg --user --package-db=clear --package-db=global --package-db=/home/cody/.stack/snapshots/x86_64-linux/nightly-2015-12-28/7.10.3/pkgdb --libdir=/home/cody/.stack/snapshots/x86_64-linux/nightly-2015-12-28/7.10.3/lib --bindir=/home/cody/.stack/snapshots/x86_64-linux/nightly-2015-12-28/7.10.3/bin --datadir=/home/cody/.stack/snapshots/x86_64-linux/nightly-2015-12-28/7.10.3/share --libexecdir=/home/cody/.stack/snapshots/x86_64-linux/nightly-2015-12-28/7.10.3/libexec --sysconfdir=/home/cody/.stack/snapshots/x86_64-linux/nightly-2015-12-28/7.10.3/etc --docdir=/home/cody/.stack/snapshots/x86_64-linux/nightly-2015-12-28/7.10.3/doc/mysql-0.1.1.8 --htmldir=/home/cody/.stack/snapshots/x86_64-linux/nightly-2015-12-28/7.10.3/doc/mysql-0.1.1.8 --haddockdir=/home/cody/.stack/snapshots/x86_64-linux/nightly-2015-12-28/7.10.3/doc/mysql-0.1.1.8 --dependency=base=base-4.8.2.0-0d6d1084fbc041e1cded9228e80e264d --dependency=bytestring=bytestring-0.10.6.0-c60f4c543b22c7f7293a06ae48820437 --dependency=containers=containers-0.5.6.2-e59c9b78d840fa743d4169d4bea15592
    Process exited with code: ExitFailure 1
    Logs have been written to: /tmp/yesod-mysql-example/.stack-work/logs/mysql-0.1.1.8.log

    [1 of 1] Compiling Main             ( /tmp/stack6322/mysql-0.1.1.8/Setup.lhs, /tmp/stack6322/mysql-0.1.1.8/.stack-work/dist/x86_64-linux/Cabal-1.22.5.0/setup/Main.o )
    Linking /tmp/stack6322/mysql-0.1.1.8/.stack-work/dist/x86_64-linux/Cabal-1.22.5.0/setup/setup ...
    Configuring mysql-0.1.1.8...
    setup: The program 'mysql_config' is required but it could not be found
```
5. No really, install mysql dependencies

I partially made this mistake on purpose. On ubuntu I do this to fix it:

```shell
yesod-mysql-example λ  sudo apt-get install mysql-server
[sudo] password for cody: 
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following extra packages will be installed:
  libaio1 libdbd-mysql-perl libdbi-perl libhtml-template-perl libmysqlclient18 libterm-readkey-perl
  mysql-client-5.6 mysql-client-core-5.6 mysql-common mysql-server-5.6 mysql-server-core-5.6
Suggested packages:
  libmldbm-perl libnet-daemon-perl libsql-statement-perl libipc-sharedcache-perl mailx tinyca
The following NEW packages will be installed:
  libaio1 libdbd-mysql-perl libdbi-perl libhtml-template-perl libmysqlclient18 libterm-readkey-perl
  mysql-client-5.6 mysql-client-core-5.6 mysql-common mysql-server mysql-server-5.6
  mysql-server-core-5.6
0 upgraded, 12 newly installed, 0 to remove and 45 not upgraded.
Need to get 21.5 MB of archives.
After this operation, 154 MB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://us.archive.ubuntu.com/ubuntu/ wily/main libaio1 amd64 0.3.110-1 [6,454 B]
Get:2 http://us.archive.ubuntu.com/ubuntu/ wily-updates/main mysql-common all 5.6.27-0ubuntu1 [14.5 kB]
Get:3 http://us.archive.ubuntu.com/ubuntu/ wily-updates/main libmysqlclient18 amd64 5.6.27-0ubuntu1 [699 kB]
Get:4 http://us.archive.ubuntu.com/ubuntu/ wily/main libdbi-perl amd64 1.633-1 [772 kB]
Get:5 http://us.archive.ubuntu.com/ubuntu/ wily/main libdbd-mysql-perl amd64 4.028-2 [88.6 kB]
Get:6 http://us.archive.ubuntu.com/ubuntu/ wily/main libterm-readkey-perl amd64 2.33-1 [28.1 kB]
Get:7 http://us.archive.ubuntu.com/ubuntu/ wily-updates/main mysql-client-core-5.6 amd64 5.6.27-0ubuntu1 [4,078 kB]
Get:8 http://us.archive.ubuntu.com/ubuntu/ wily-updates/main mysql-client-5.6 amd64 5.6.27-0ubuntu1 [5,319 kB]
Get:9 http://us.archive.ubuntu.com/ubuntu/ wily-updates/main mysql-server-core-5.6 amd64 5.6.27-0ubuntu1 [4,633 kB]
Get:10 http://us.archive.ubuntu.com/ubuntu/ wily-updates/main mysql-server-5.6 amd64 5.6.27-0ubuntu1 [5,744 kB]
Get:11 http://us.archive.ubuntu.com/ubuntu/ wily/main libhtml-template-perl all 2.95-2 [60.4 kB]
Get:12 http://us.archive.ubuntu.com/ubuntu/ wily-updates/main mysql-server all 5.6.27-0ubuntu1 [8,804 B]
Fetched 21.5 MB in 1min 2s (345 kB/s)
debconf: unable to initialize frontend: Dialog
debconf: (Dialog frontend will not work on a dumb terminal, an emacs shell buffer, or without a controlling terminal.)
debconf: falling back to frontend: Readline
Preconfiguring packages ...
Configuring mysql-server-5.6
----------------------------

While not mandatory, it is highly recommended that you set a password for the MySQL administrative 
"root" user.

If this field is left blank, the password will not be changed.

New password for the MySQL "root" user:  




Repeat password for the MySQL "root" user: 


Password input error

The two passwords you entered were not the same. Please try again.

While not mandatory, it is highly recommended that you set a password for the MySQL administrative 
"root" user.

If this field is left blank, the password will not be changed.

New password for the MySQL "root" user: 




Selecting previously unselected package libaio1:amd64.
(Reading database ... 199769 files and directories currently installed.)
Preparing to unpack .../libaio1_0.3.110-1_amd64.deb ...
Unpacking libaio1:amd64 (0.3.110-1) ...
Selecting previously unselected package mysql-common.
Preparing to unpack .../mysql-common_5.6.27-0ubuntu1_all.deb ...
Unpacking mysql-common (5.6.27-0ubuntu1) ...
Selecting previously unselected package libmysqlclient18:amd64.
Preparing to unpack .../libmysqlclient18_5.6.27-0ubuntu1_amd64.deb ...
Unpacking libmysqlclient18:amd64 (5.6.27-0ubuntu1) ...
Selecting previously unselected package libdbi-perl.
Preparing to unpack .../libdbi-perl_1.633-1_amd64.deb ...
Unpacking libdbi-perl (1.633-1) ...
Selecting previously unselected package libdbd-mysql-perl.
Preparing to unpack .../libdbd-mysql-perl_4.028-2_amd64.deb ...
Unpacking libdbd-mysql-perl (4.028-2) ...
Selecting previously unselected package libterm-readkey-perl.
Preparing to unpack .../libterm-readkey-perl_2.33-1_amd64.deb ...
Unpacking libterm-readkey-perl (2.33-1) ...
Selecting previously unselected package mysql-client-core-5.6.
Preparing to unpack .../mysql-client-core-5.6_5.6.27-0ubuntu1_amd64.deb ...
Unpacking mysql-client-core-5.6 (5.6.27-0ubuntu1) ...
Selecting previously unselected package mysql-client-5.6.
Preparing to unpack .../mysql-client-5.6_5.6.27-0ubuntu1_amd64.deb ...
Unpacking mysql-client-5.6 (5.6.27-0ubuntu1) ...
Selecting previously unselected package mysql-server-core-5.6.
Preparing to unpack .../mysql-server-core-5.6_5.6.27-0ubuntu1_amd64.deb ...
Unpacking mysql-server-core-5.6 (5.6.27-0ubuntu1) ...
Processing triggers for man-db (2.7.4-1) ...
Setting up mysql-common (5.6.27-0ubuntu1) ...
update-alternatives: using /etc/mysql/my.cnf.fallback to provide /etc/mysql/my.cnf (my.cnf) in auto mode
Selecting previously unselected package mysql-server-5.6.
(Reading database ... 200141 files and directories currently installed.)
Preparing to unpack .../mysql-server-5.6_5.6.27-0ubuntu1_amd64.deb ...
debconf: unable to initialize frontend: Dialog
debconf: (Dialog frontend will not work on a dumb terminal, an emacs shell buffer, or without a controlling terminal.)
debconf: falling back to frontend: Readline
Configuring mysql-server-5.6
----------------------------

While not mandatory, it is highly recommended that you set a password for the MySQL administrative 
"root" user.

If this field is left blank, the password will not be changed.

New password for the MySQL "root" user: 


Unpacking mysql-server-5.6 (5.6.27-0ubuntu1) ...


Selecting previously unselected package libhtml-template-perl.
Preparing to unpack .../libhtml-template-perl_2.95-2_all.deb ...
Unpacking libhtml-template-perl (2.95-2) ...
Selecting previously unselected package mysql-server.
Preparing to unpack .../mysql-server_5.6.27-0ubuntu1_all.deb ...
Unpacking mysql-server (5.6.27-0ubuntu1) ...
Processing triggers for man-db (2.7.4-1) ...
Processing triggers for ureadahead (0.100.0-19) ...
ureadahead will be reprofiled on next reboot
Processing triggers for systemd (225-1ubuntu9) ...
Setting up libaio1:amd64 (0.3.110-1) ...
Setting up libmysqlclient18:amd64 (5.6.27-0ubuntu1) ...
Setting up libdbi-perl (1.633-1) ...
Setting up libdbd-mysql-perl (4.028-2) ...
Setting up libterm-readkey-perl (2.33-1) ...
Setting up mysql-client-core-5.6 (5.6.27-0ubuntu1) ...
Setting up mysql-client-5.6 (5.6.27-0ubuntu1) ...
Setting up mysql-server-core-5.6 (5.6.27-0ubuntu1) ...
Setting up mysql-server-5.6 (5.6.27-0ubuntu1) ...
debconf: unable to initialize frontend: Dialog
debconf: (Dialog frontend will not work on a dumb terminal, an emacs shell buffer, or without a controlling terminal.)
debconf: falling back to frontend: Readline
Configuring mysql-server-5.6
----------------------------

While not mandatory, it is highly recommended that you set a password for the MySQL administrative 
"root" user.

If this field is left blank, the password will not be changed.

New password for the MySQL "root" user: 


update-alternatives: using /etc/mysql/mysql.cnf to provide /etc/mysql/my.cnf (my.cnf) in auto mode


Setting up libhtml-template-perl (2.95-2) ...
Setting up mysql-server (5.6.27-0ubuntu1) ...
Processing triggers for libc-bin (2.21-0ubuntu4) ...
Processing triggers for ureadahead (0.100.0-19) ...
Processing triggers for systemd (225-1ubuntu9) ..
```
6. Forgot another dep, Install libmysqlclient-dev
```shell
yesod-mysql-example λ sudo apt-get install libmysqlclient-dev 
[sudo] password for cody: 
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following NEW packages will be installed:
  libmysqlclient-dev
0 upgraded, 1 newly installed, 0 to remove and 45 not upgraded.
Need to get 998 kB of archives.
After this operation, 6,295 kB of additional disk space will be used.
Get:1 http://us.archive.ubuntu.com/ubuntu/ wily-updates/main libmysqlclient-dev amd64 5.6.27-0ubuntu1 [998 kB]
Fetched 998 kB in 0s (1,368 kB/s)
debconf: unable to initialize frontend: Dialog
debconf: (Dialog frontend will not work on a dumb terminal, an emacs shell buffer, or without a controlling terminal.)
debconf: falling back to frontend: Readline
Selecting previously unselected package libmysqlclient-dev.
(Reading database ... 200245 files and directories currently installed.)
Preparing to unpack .../libmysqlclient-dev_5.6.27-0ubuntu1_amd64.deb ...
Unpacking libmysqlclient-dev (5.6.27-0ubuntu1) ...
Processing triggers for man-db (2.7.4-1) ...
Setting up libmysqlclient-dev (5.6.27-0ubuntu1) ...
```
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
