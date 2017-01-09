Storj Share
===========

[![Build Status](https://img.shields.io/travis/Storj/storjshare-daemon.svg?style=flat-square)](https://travis-ci.org/Storj/storjshare-daemon)
[![Coverage Status](https://img.shields.io/coveralls/Storj/storjshare-daemon.svg?style=flat-square)](https://coveralls.io/r/Storj/storjshare-daemon)
[![NPM](https://img.shields.io/npm/v/storjshare-daemon.svg?style=flat-square)](https://www.npmjs.com/package/storjshare-daemon)
[![License](https://img.shields.io/badge/license-AGPL3.0-blue.svg?style=flat-square)](https://raw.githubusercontent.com/Storj/storjshare-daemon/master/LICENSE)

Daemon + CLI for farming data on the Storj network, suitable for standalone
use or inclusion in other packages.

## Installation

Make sure you have the following prerequisites installed:

* Git
* Node.js LTS
* NPM
* Python 2.7
* GCC/G++/Make

Install the package globally using Node Package Manager:

```
npm install -g storj-share
```

## Usage (CLI)

Once installed, you will have access to the `storjshare` program, so start by 
asking it for some help.

```
storjshare --help

  Usage: storjshare [options] [command]


  Commands:

    start       start a farming node
    stop        stop a farming node
    restart     restart a farming node
    status      check status of node(s)
    logs        tail the logs for a node
    create      create a new configuration
    destroy     kills the farming node
    killall     kills all shares and stops the daemon
    daemon      starts the daemon
    help [cmd]  display help for [cmd]

  Options:

    -h, --help     output usage information
    -V, --version  output the version number
```

You can also get more detailed help for a specific command.

```
storjshare help create

Usage: storjshare-create [options]

  Options:

    -h, --help                 output usage information
    -a, --sjcx <addr>          specify the sjcx address (required)
    -s, --storage <path>       specify the storage path
    -l, --logfile <path>       specify the logfile path
    -k, --privkey <privkey>    specify the private key
    -o, --outfile <writepath>  write config to path
```

## Usage (Programmatic)

The Storj Share daemon uses a local [dnode](https://github.com/substack/dnode) 
server to handle RPC message from the CLI and other applications. Assuming the 
daemon is running, your program can communicate with it using this interface. 
The example that follows is using Node.js, but dnode is implemented in many
[other languages](https://github.com/substack/dnode#dnode-in-other-languages).

```js
const dnode = require('dnode');
const daemon = dnode.connect(45015);

daemon.on('remote', (rpc) => {
  // rpc.start(configPath, callback);
  // rpc.stop(nodeId, callback);
  // rpc.restart(nodeId, callback);
  // rpc.status(callback);
  // rpc.destroy(nodeId, callback);
  // rpc.killall();
});
```

## Configuring the Daemon

The Storj Share daemon loads configuration from anywhere the 
[rc](https://www.npmjs.com/package/rc) package can read it. The first time you 
run the daemon, it will create a directory in `$HOME/.config/storjshare`, so 
the simplest way to change the daemon's behavior is to create a file at 
`$HOME/.config/storjshare/config` containing the following:

```json
{
  "daemonRpcPort": 45015,
  "daemonRpcAddress": "127.0.0.1",
  "daemonLogFilePath": "",
  "daemonLogVerbosity": 3
}
```

Modify these parameters to your liking, see `example/daemon.config.json` for 
detailed explanation of these properties.

## Migrating from [`storjshare-cli`](https://github.com/storj/storjshare-cli)

Storj Share provides a simple method for creating new shares, but if you were 
previously using the `storjshare-cli` package superceded by this one, you'll 
want to migrate your configuration to the new format. To do this, first you'll 
need to dump your private key **before** installing this package.

> If you accidentally overwrote your old `storjshare-cli` installation with 
> this package, don't worry - just reinstall the old package to dump the key, 
> then reinstall this package.

### Step 0: Dump Your Private Key

You can print your cleartext private key from storjshare-cli, using the 
`dump-key` command:

```
storjshare dump-key
 [...]  > Unlock your private key to start storj  >  ********

 [info]   Cleartext Private Key:
 [info]   ======================
 [info]   4154e85e87b323611cba45ab1cd51203f2508b1da8455cdff8b641cce827f3d6
 [info]   
 [info]   (This key is suitable for importing into Storj Share GUI)
```

If you are using a custom data directory, be sure to add the `--datadir <path>`
option to be sure you get the correct key. Also be sure to note your defined 
payout address and data directory.

### Step 1: Install Storj Share and Create Config

Now that you have your private key, you can generate a new configuration file. 
To do this, first install the `storj-share` package globally and use the 
`create` command. You'll need to remove the `storjshare-cli` package first, so 
make sure you perform the previous step for all shared drives before 
proceeding forward.

```
npm remove -g storjshare-cli
npm install -g storj-share
```

Now that you have Storj Share installed, use the `create` command to generate 
your configuration.

```
storjshare create -k 4154e8... -a 1K1rPg... -s <datadir> -o <writepath>
```

This will generate your configuration file given the parameters you passed in, 
write the file to the path following the `-o` option, and open it in your text 
editor. Here, you can make other changes to the configuration following the 
detailed comments in the generated file.

### Step 2: Use The New Configuration

Now that you have successfully migrated your configuration file, you can use 
it to start the share.

```
storjshare start --config path/to/config.json

  * daemon is not running, starting...

  * starting share with config at path/to/config.json
```

## License

Storj Share - Daemon + CLI for farming data on the Storj network.  
Copyright (C) 2017 Storj Labs, Inc

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU Affero General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU Affero General Public License for more details.

You should have received a copy of the GNU Affero General Public License
along with this program.  If not, see http://www.gnu.org/licenses/.

