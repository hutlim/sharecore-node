# Setting up Development Environment

## Install Node.js

Install Node.js by your favorite method, or use Node Version Manager by following directions at https://github.com/creationix/nvm

```bash
nvm install v4
```

## Fork and Download Repositories

To develop sharecore-node:

```bash
cd ~
git clone git@github.com:<yourusername>/sharecore-node.git
git clone git@github.com:<yourusername>/sharecore-lib.git
```

To develop sharecoin or to compile from source:

```bash
git clone git@github.com:<yourusername>/sharecoin.git
git fetch origin <branchname>:<branchname>
git checkout <branchname>
```
**Note**: See sharecoin documentation for building sharecoin on your platform.


## Install Development Dependencies

For Ubuntu:
```bash
sudo apt-get install libzmq3-dev
sudo apt-get install build-essential
```
**Note**: Make sure that libzmq-dev is not installed, it should be removed when installing libzmq3-dev.


For Mac OS X:
```bash
brew install zeromq
```

## Install and Symlink

```bash
cd sharecore-lib
npm install
cd ../sharecore-node
npm install
```
**Note**: If you get a message about not being able to download sharecoin distribution, you'll need to compile sharecoind from source, and setup your configuration to use that version.


We now will setup symlinks in `sharecore-node` *(repeat this for any other modules you're planning on developing)*:
```bash
cd node_modules
rm -rf sharecore-lib
ln -s ~/sharecore-lib
rm -rf bitcoind-rpc
ln -s ~/bitcoind-rpc
```

And if you're compiling or developing sharecoin:
```bash
cd ../bin
ln -sf ~/sharecoin/src/sharecoind
```

## Run Tests

If you do not already have mocha installed:
```bash
npm install mocha -g
```

To run all test suites:
```bash
cd sharecore-node
npm run regtest
npm run test
```

To run a specific unit test in watch mode:
```bash
mocha -w -R spec test/services/bitcoind.unit.js
```

To run a specific regtest:
```bash
mocha -R spec regtest/bitcoind.js
```

## Running a Development Node

To test running the node, you can setup a configuration that will specify development versions of all of the services:

```bash
cd ~
mkdir devnode
cd devnode
mkdir node_modules
touch sharecore-node.json
touch package.json
```

Edit `sharecore-node.json` with something similar to:
```json
{
  "network": "livenet",
  "port": 3001,
  "services": [
    "sharecoind",
    "web",
    "insight-api",
    "insight-ui",
    "<additional_service>"
  ],
  "servicesConfig": {
    "sharecoind": {
      "spawn": {
        "datadir": "/home/<youruser>/.sharecoin",
        "exec": "/home/<youruser>/sharecoin/src/sharecoind"
      }
    }
  }
}
```

**Note**: To install services [insight-api](https://github.com/bitpay/insight-api) and [insight-ui](https://github.com/bitpay/insight-ui) you'll need to clone the repositories locally.

Setup symlinks for all of the services and dependencies:

```bash
cd node_modules
ln -s ~/sharecore-lib
ln -s ~/sharecore-node
ln -s ~/insight-api
ln -s ~/insight-ui
```

Make sure that the `<datadir>/sharecoin.conf` has the necessary settings, for example:
```
server=1
whitelist=127.0.0.1
txindex=1
addressindex=1
timestampindex=1
spentindex=1
zmqpubrawtx=tcp://127.0.0.1:29443
zmqpubhashblock=tcp://127.0.0.1:29443
rpcallowip=127.0.0.1
rpcuser=sharecoin
rpcpassword=local321
```

From within the `devnode` directory with the configuration file, start the node:
```bash
../sharecore-node/bin/sharecore-node start
```
