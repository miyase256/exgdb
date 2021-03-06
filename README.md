ExGDB
=====

ExGDB - Extension for GDB

## New Classes:

* `Exgdb` -- This has many methods to coding debug automation script
* `ExgdbCmd` -- This has many methods that can execute as gdb command

## Methods of Exgdb:

* `read_int(addr)` -- Customized read_int of peda
* `read_int_bytes(addr, intsize=None)` -- Read bytes as intsize list
* `read_byte(addr, intsize=None)` -- Read one byte
* `read_bytes(addr, size)` -- Read bytes as any size list
* ... and all methods of peda and Pwngdb.

## Methods of ExgdbCmd:

* `ctn, c` -- Execute continue command
* `brk, b <symbol>` -- Execute break command
* `next, n, [count]` -- Execute next command
* `step, s [count]` -- Execute step command
* `nexti, ni [count]` -- Execute nexti command
* `stepi, si [count]` -- Execute stepi command
* `afterpc, af <count>` -- Show instructions after now program-counter
* `beforepc, bef <count>` -- Show instructions before now program-counter
* `grep <command> <regex>` -- Grep command output
* `allstack` -- Show all stack data
* `nuntil <regex>` -- Execute nexti command until given regexp
* `suntil <regex>` -- Execute stepi command until given regexp
* `nextcalluntil <regex>` -- Execute nextcall command until given regexp
* `stepcalluntil <regex>` -- Execute nextcall and step command until given regexp and given depth
* `infonow, inow` -- Show detail information of the instruction now specified program-counter
* `contextmode <mode>` -- Set context mode (e.g. `contextmode reg,code`, `contextmode infonow` )
* `infox <addr>` -- Customized xinfo command of peda
* `patch <addr> <value> [size]` -- Customized patch command of peda
* `parseheap` -- Customized parseheap command of Pwngdb
* ... and all commands of peda and Pwngdb.

## Installation

### 1. Clone:

```
$ git clone https://github.com/miyase256/exgdb.git /path/to/exgdb
```

"/path/to/" is any directory.

### 2. Export EXGDBPATH

```
$ cd /path/to/exgdb/
$ export EXGDBPATH=$PWD
$ echo "export EXGDBPATH=$PWD" >> ~/.bashrc
```

### 3. Install Plugin Manager (exgdbctl) :

```
$ cp -a $EXGDBPATH/bin/exgdbctl /usr/local/bin/
```

### 4. Install some plugins:

```
$ exgdbctl install peda # git clone https://github.com/longld/peda.git $EXGDBPATH/plugins/peda
$ exgdbctl install Pwngdb # git clone https://github.com/scwuaptx/Pwngdb.git $EXGDBPATH/plugins/Pwngdb
```

### 5. Prepare .gdbinit

```
$ echo "source $EXGDBPATH/gdbinit.py" >> ~/.gdbinit
```

## Usage of ExGDB

### Usage when just debugging:

```
$ gdb {any_binary}
gdb-peda$ start
gdb-peda$ contextmode infonow,code,stack
gdb-peda$ radvance call
gdb-peda$ grep 'pdisass' '.*call.*'
=> 0x402a2c:    call   0x40db00
   0x402a3b:    call   0x402840 <setlocale@plt>
   0x402a4a:    call   0x4024b0 <bindtextdomain@plt>
   0x402a54:    call   0x402470 <textdomain@plt>
```

### Usage as a library:

```
$ cat gdbrc.py
c.start() # c: ExgdbCmd(), e: Exgdb()
c.contextmode("infonow,code,stack")
c.radvance("call")
c.grep("pdisass", ".*call.*")
$ gdb {any_binary} -x gdbrc.py

...

=> 0x402a2c:    call   0x40db00
   0x402a3b:    call   0x402840 <setlocale@plt>
   0x402a4a:    call   0x4024b0 <bindtextdomain@plt>
   0x402a54:    call   0x402470 <textdomain@plt>
gdb-peda$
```

### Usage as a library when just debugging:

```
$ gdb {any_binary}
gdb-peda$ start
gdb-peda$ contextmode infonow,code,stack
gdb-peda$ radvance call
gdb-peda$ edit tmp.py # You must have set `$EDITOR` . And you can use `vim` or `emacs` instead of `editor` .
gdb-peda$ cat tmp.py
c.grep("pdisass", ".*call.*")
gdb-peda$ source tmp.py
=> 0x402a2c:    call   0x40db00
   0x402a3b:    call   0x402840 <setlocale@plt>
   0x402a4a:    call   0x4024b0 <bindtextdomain@plt>
   0x402a54:    call   0x402470 <textdomain@plt>
```

## Usage of Plugin Manager (exgdbctl)

```
$ exgdbctl -h
exgdbctl <command> [args]
command: list
         install <peda/Pwngdb/ANY PLUGIN URL>
         delete  <peda/Pwngdb/ANY PLUGIN NAME>
         update  <exgdb/peda/Pwngdb/ANY PLUGIN NAME>
         enable  <exgdb/peda/Pwngdb/ANY PLUGIN NAME>
         disable <exgdb/peda/Pwngdb/ANY PLUGIN NAME>
```

show plugins information

```
$ exgdbctl list
exgdb: enabled
peda: not installed!!
Pwngdb: not installed!!
```

You have to install peda and Pwngdb.

```
$ exgdbctl install peda
Cloning into '/home/miyase256/src/github.com/miyase256/exgdb/plugins/peda'...
remote: Enumerating objects: 351, done.
remote: Total 351 (delta 0), reused 0 (delta 0), pack-reused 351
Receiving objects: 100% (351/351), 279.51 KiB | 580.00 KiB/s, done.
Resolving deltas: 100% (220/220), done.
[+]install successful
exgdb: enabled
peda: enabled
Pwngdb: not installed!!
$ exgdbctl install Pwngdb
Cloning into '/home/miyase256/src/github.com/miyase256/exgdb/plugins/Pwngdb'...
remote: Enumerating objects: 29, done.
remote: Counting objects: 100% (29/29), done.
remote: Compressing objects: 100% (21/21), done.
remote: Total 451 (delta 14), reused 20 (delta 8), pack-reused 422
Receiving objects: 100% (451/451), 196.36 KiB | 430.00 KiB/s, done.
Resolving deltas: 100% (284/284), done.
[+]install successful
exgdb: enabled
peda: enabled
Pwngdb: enabled
```

You can install any plugin from repository url

```
$ exgdbctl install https://github.com/miyase256/sample_plugin
Cloning into '/home/miyase256/src/github.com/miyase256/exgdb/plugins/sample_plugin'...
remote: Enumerating objects: 4, done.
remote: Counting objects: 100% (4/4), done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 4 (delta 0), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (4/4), done.
[+]install successful
exgdb: enabled
peda: enabled
Pwngdb: enabled
sample_plugin: enabled
```

delete a plugin

```
$ exgdbctl delete sample_plugin
[+]deleting /home/miyase256/src/github.com/miyase256/exgdb/plugins/sample_plugin
[+]delete successful
exgdb: enabled
peda: enabled
Pwngdb: enabled
```


disable a plugin

```
$ exgdbctl disable peda
[+]disabled peda
exgdb: enabled
peda: disabled
Pwngdb: enabled
```

enable a plugin

```
$ exgdbctl enable peda
[+]enabled peda
exgdb: enabled
peda: enabled
Pwngdb: enabled
```

## Add New Plugin

### 1. Prepare new plugin:

You need "{any name}.py" and "export_to_exgdb.py".

- {any name}.py: main script of your plugin.
- export_to_exgdb.py: setattr your functions to Exgdb or ExgdbCmd

```
$ pwd
/path/to/myplugin
$ ls
myplugin.py export_to_exgdb.py
$ cat export_to_exgdb.py
cmds = [cmd for cmd in dir(MyPlugin) if callable(getattr(MyPlugin, cmd))]
for cmd in cmds:
    if not cmd.startswith("_"):
        cmd_obj = getattr(MyPlugin, cmd)
        setattr(ExgdbCmd, cmd, cmd_obj)
$ git remote -v
https://github.com/username/myplugin.git
$ git push origin master
```

### 2. Install your plugin:

```
$ exgdbctl install https://github.com/username/myplugin.git
[+]install successful
exgdb: enabled
peda: enabled
Pwngdb: enabled
myplugin: enabled
```

### 3. Edit $EXGDBPATH/gdbinit.py

```
$ cat $EXGDBPATH/gdbinit.py

・・・

peda_is_enabled = os.path.exists("%s/peda" % pluginpath)
pwngdb_is_enabled = os.path.exists("%s/Pwngdb" % pluginpath)
#yourplugin_is_enabled = os.path.exists("%s/yourplugin" % pluginpath)

if peda_is_enabled:
    __file__ = "%s/peda/peda.py" % pluginpath
    gdb.execute("source %s/peda/peda.py" % pluginpath)
if pwngdb_is_enabled:
    sys.path.insert(0, "%s/Pwngdb/angelheap" % pluginpath)
    gdb.execute("source %s/Pwngdb/pwngdb.py" % pluginpath)
    gdb.execute("source %s/Pwngdb/angelheap/gdbinit.py" % pluginpath)
    gdb.execute("source %s/Pwngdb/angelheap/command_wrapper.py" % pluginpath)
    gdb.execute("source %s/Pwngdb/angelheap/angelheap.py" % pluginpath)
#if yourplugin_is_enabled:
#    gdb.execute("source %s/yourplugin/gdbinit.py" % pluginpath)

・・・

$ vim $EXGDBPATH/gdbinit.py
$ cat $EXGDBPATH/gdbinit.py

・・・

peda_is_enabled = os.path.exists("%s/peda" % pluginpath)
pwngdb_is_enabled = os.path.exists("%s/Pwngdb" % pluginpath)
myplugin_is_enabled = os.path.exists("%s/myplugin" % pluginpath)

if peda_is_enabled:
    __file__ = "%s/peda/peda.py" % pluginpath
    gdb.execute("source %s/peda/peda.py" % pluginpath)
if pwngdb_is_enabled:
    sys.path.insert(0, "%s/Pwngdb/angelheap" % pluginpath)
    gdb.execute("source %s/Pwngdb/pwngdb.py" % pluginpath)
    gdb.execute("source %s/Pwngdb/angelheap/gdbinit.py" % pluginpath)
    gdb.execute("source %s/Pwngdb/angelheap/command_wrapper.py" % pluginpath)
    gdb.execute("source %s/Pwngdb/angelheap/angelheap.py" % pluginpath)
if myplugin_is_enabled:
    gdb.execute("source %s/myplugin/myplugin.py" % pluginpath)

・・・

```

You can issue Pull Request such as this patch.

## If you have any demands or questions

Please ask everything [here](https://twitter.com/miyase256)
