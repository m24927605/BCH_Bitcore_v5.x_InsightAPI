Ubuntu OS

Step1  
> Install node.js,the node .js version is v8.11.3. and npm version 5.6.0
```
curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
sudo apt-get install -y nodejs
```  

Step2  
> Install bitcoinABC's bitcoind (version is 0.17.2)
``` 
sudo apt-get install software-properties-common
sudo add-apt-repository ppa:bitcoin-abc/ppa
sudo apt-get update
sudo apt-get install bitcoind
sudo apt-get install libzmq3-dev build-essential
```  
Step3  
> create bitcoin.conf for bitcoinABC's bitcoind  
```
cd ~ && touch bitcoin.conf
vi bitcoin.conf
datadir=/home/[user]/bch_data
server=1
whitebind=127.0.0.1:8333
whitelist=127.0.0.1
txindex=1
addressindex=1
timestampindex=1
spentindex=1
reindex=1
zmqpubrawtx=tcp://127.0.0.1:28332
zmqpubhashtx=tcp://127.0.0.1:28333
zmqpubhashblock=tcp://127.0.0.1:28334
zmqpubrawblock=tcp://127.0.0.1:28335
rpcallowip=127.0.0.1
rpcport=8332
rpcuser=[rpcuser]
rpcpassword=[rpcpassword]
uacomment=bitcore
daemon=1
```

Step4  
> launch bitcoind and you will see the response "Bitcoin is starting" in terminal then check the debug.log in bch_data folder.
```
cd ~
bitcoind -conf=/home/[user]/bitcoin.conf
```

Step5  
> Install bitcore locally
```
cd ~
git clone https://github.com/bitpay/bitcore-node.git
cd bitcore-node
```
Step6  
> change package.json
```
{
  "name": "bitcore-node",
  "description": "Full node with extended capabilities using Bitcore and Bitcoin Core",
  "engines": {
    "node": ">=8.0.0"
  },
  "author": "BitPay <dev@bitpay.com>",
  "version": "5.0.0-beta.44",
  "main": "./index.js",
  "repository": "git://github.com/bitpay/bitcore-node.git",
  "homepage": "https://github.com/bitpay/bitcore-node",
  "bugs": {
    "url": "https://github.com/bitpay/bitcore-node/issues"
  },
  "bin": {
    "bitcore-node": "./bin/bitcore-node"
  },
  "scripts": {
    "test": "NODE_ENV=test mocha -R spec --recursive test"
  },
  "tags": [
    "bitcoin",
    "bitcoind",
    "bcoin",
    "bitcoin full node",
    "bitcoin index",
    "block explorer",
    "wallet backend"
  ],
  "dependencies": {
    "async": "^2.5.0",
    "bcoin": "bitpay/bcoin#v1.0.0-beta.14+cash",
    "bitcoind-rpc": "^0.6.0",
    "bitcore-lib": "5.0.0-beta.1",
    "bitcore-p2p": "5.0.0-beta.1",
    "insight-api": "5.0.0-beta.44",
    "bn.js": "^4.11.8",
    "body-parser": "^1.13.3",
    "colors": "^1.1.2",
    "commander": "^2.8.1",
    "errno": "^0.1.4",
    "express": "^4.13.3",
    "leveldown": "^2.0.0",
    "levelup": "^2.0.0",
    "liftoff": "^2.2.0",
    "lodash": "^4.17.4",
    "lru-cache": "^4.1.1",
    "memwatch-next": "^0.3.0",
    "mkdirp": "0.5.0",
    "path-is-absolute": "^1.0.0",
    "socket.io": "^1.4.5",
    "socket.io-client": "^1.4.5",
    "xxhash": "^0.2.4"
  },
  "devDependencies": {
    "chai": "^3.5.0",
    "coveralls": "^2.11.9",
    "istanbul": "^0.4.3",
    "jshint": "^2.9.2",
    "jshint-stylish": "^2.1.0",
    "mocha": "3.2.0",
    "proxyquire": "^1.3.1",
    "rimraf": "^2.4.2",
    "sinon": "^1.15.4"
  },
  "license": "MIT"
}
```

Step7  
> install the library
```
cd ~/bitcore
npm install
```


Step8  
> create and edit bitcode-node.json
```
cd ~/bitcore-node && touch bitcore-node.json
vi bitcore-node.json

{
  "network": "livenet",
  "port": 3001,
  "datadir": "/home/[user]/bch_data",
  "services": [
    "p2p",
    "db",
    "header",
    "block",
    "mempool",
    "address",
    "transaction",
    "timestamp",
    "fee",
    "insight-api",
    "web"
  ],
  "servicesConfig": {
    "insight-api": {
      "disableRateLimiter": true,
      "enableCache": true
    },
    "p2p": {
      "peers": [
        {
          "ip": {
            "v4": "127.0.0.1"
          },
          "port": 8333
        }
      ]
    },
    "fee": {
      "rpc": {
        "user": [rpcuser],
        "pass": [rpcpassword],
        "host": "localhost",
        "protocol": "http",
        "port": 8332,
        "zmqpubrawtx":"tcp://127.0.0.1:28332",
        "zmqpubhashtx":"tcp://127.0.0.1:28333",
        "zmqpubhashblock":"tcp://127.0.0.1:28334",
        "zmqpubrawblock":"tcp://127.0.0.1:28335"
      }
    }
  }
}
```
**Step9 NOTICE THIS!!!!**  
> This is very important step to fix the bitcore-node's bug.  
the bitcore-lib and bitcoinABC's version is not match,it will happen some configure problem like network(p2p).

> **BitcoinABC livenet part**      
https://github.com/Bitcoin-ABC/bitcoin-abc/blob/master/src/chainparams.cpp  
Please find the netMagic class CMainParams  
...  
  netMagic[0] = 0xe3;  
  netMagic[1] = 0xe1;  
  netMagic[2] = 0xf3;  
  netMagic[3] = 0xe8;  
...  

> bitcore-lib part   
**please change the networkMagic's value to 0xe3e1f3e8**  
```
cd ~/bitcore-node/node_modules/bitcore-lib/lib  
vi networks.js 
...  
addNetwork({
  name: 'livenet',
  alias: 'mainnet',
  pubkeyhash: 0x00,
  privatekey: 0x80,
  scripthash: 0x05,
  xpubkey: 0x0488b21e,
  xprivkey: 0x0488ade4,
  networkMagic: 0xe3e1f3e8,
  port: 8333,
  dnsSeeds: [
    'seed.bitcoin.sipa.be',
    'dnsseed.bluematt.me',
    'dnsseed.bitcoin.dashjr.org',
    'seed.bitcoinstats.com',
    'seed.bitnodes.io',
    'bitseed.xf2.org'
  ]
}); 
...  
```
> **BitcoinABC testnet part**     
https://github.com/Bitcoin-ABC/bitcoin-abc/blob/master/src/chainparams.cpp  
Please find the netMagic class CMainParams  
...  
  netMagic[0] = 0xf4;  
  netMagic[1] = 0xe5;  
  netMagic[2] = 0xf3;  
  netMagic[3] = 0xf4;  
...

> Part bitcore-lib  
**please change the networkMagic's value to 0xf4e5f3f4**
```
cd ~/bitcore-node/node_modules/bitcore-lib/lib  
vi networks.js 
...  
var TESTNET = {
  PORT: 18333,
  NETWORK_MAGIC: BufferUtil.integerAsBuffer(0xf4e5f3f4),
  DNS_SEEDS: [
    'testnet-seed.bitcoin.petertodd.org',
    'testnet-seed.bluematt.me',
    'testnet-seed.alexykot.me',
    'testnet-seed.bitcoin.schildbach.de'
  ]
};
...
```

> **BitcoinABC regtestnet part**       
https://github.com/Bitcoin-ABC/bitcoin-abc/blob/master/src/chainparams.cpp  
Please find the netMagic class CMainParams  
...  
  netMagic[0] = 0xda;  
  netMagic[1] = 0xb5;  
  netMagic[2] = 0xbf;  
  netMagic[3] = 0xfa;  
...

>Part bitcore-lib  
**please change the networkMagic's value to 0xdab5bffa**
```
cd ~/bitcore-node/node_modules/bitcore-lib/lib  
vi networks.js 
...
var REGTEST = {
  PORT: 18444,
  NETWORK_MAGIC: BufferUtil.integerAsBuffer(0xdab5bffa),
  DNS_SEEDS: []
};
...
```

Step10(options)  
> Install pm2
```
cd ~
sudo npm install pm2 -g
```
> If the server reboot,pm2 will auto restart.
```
pm2 startup
sudo env PATH=$PATH:/usr/bin /usr/lib/node_modules/pm2/bin/pm2 startup systemd -u [user] --hp /home/[user]
pm2 save
```
> if you install pm2 please choose step7.1 to run the bitcoin-cash server.
Step 11.1
```
cd ~/bitcore-node
pm2 start ./bin/bitcore-node -- start
```
> if you didin't install pm2,please choose step7.2 to run the bitcoin-cash server.
Step11.2
```
cd ~/bitcore-node
./bin/bitcore-node start
```
**Done!!!!**
