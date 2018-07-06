Ubuntu OS

Step1  
//Install node.js,the node .js version is v8.11.3. and npm version 5.6.0
```
curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
sudo apt-get install -y nodejs
```  

Step2  
//Install bitcoinABC's bitcoind (version is 0.17.2)
``` 
sudo apt-get install software-properties-common
sudo add-apt-repository ppa:bitcoin-abc/ppa
sudo apt-get update
sudo apt-get install bitcoind
sudo apt-get install libzmq3-dev build-essential
```  
Step3  
//create bitcoin.conf for bitcoinABC's bitcoind  
```
cd ~ && touch bitcoin.conf
vi bitcoin.conf
datadir=/home/[user]/bch_data
server=1
whitelist=127.0.0.1
txindex=1
addressindex=1
timestampindex=1
spentindex=1
zmqpubrawtx=tcp://127.0.0.1:28332
zmqpubhashblock=tcp://127.0.0.1:28332
rpcallowip=127.0.0.1
rpcuser=bch
rpcpassword=Aa123456
uacomment=bitcore
daemon=1
```
Step4  
//launch bitcoind and you will see the response "Bitcoin is starting" in terminal then check the debug.log in bch_data folder.
```
cd ~
bitcoind -conf=/home/[user]/bitcoin.conf
```

Step5  
//Install bitcore locally
```
cd ~
git clone -b v5.x https://github.com/bitpay/bitcore.git
cd bitcore
```
Step6  
//change package.json
```
{
  "name": "bitcore",
  "version": "5.0.0-beta.44",
  "description": "A platform to build bitcoin and blockchain-based applications.",
  "engines": {
    "node": ">=8.0.0"
  },
  "author": "BitPay <dev@bitpay.com>",
  "main": "index.js",
  "scripts": {
    "test": "./node_modules/.bin/mocha test/** --recursive",
    "build-deb": "./scripts/build-deb"
  },
  "bin": {
    "bitcore": "./bin/bitcore",
    "bitcored": "./bin/bitcored"
  },
  "keywords": [
    "bitcoin",
    "transaction",
    "address",
    "p2p",
    "ecies",
    "cryptocurrency",
    "blockchain",
    "payment",
    "bip21",
    "bip32",
    "bip37",
    "bip69",
    "bip70",
    "multisig"
  ],
  "repository": {
    "type": "git",
    "url": "https://github.com/bitpay/bitcore.git"
  },
  "dependencies": {
    "bitcore-lib": "5.0.0-beta.1",
    "bitcore-lib-cash": "bitpay/bitcore-lib-cash",
    "bitcore-p2p": "bitpay/bitcore-p2p",
    "bitcore-p2p-cash": "bitpay/bitcore-p2p-cash",
    "bitcore-node": "5.0.0-beta.44",
    "insight-api": "5.0.0-beta.44",
    "insight-ui": "bitpay/insight#v5.0.0-beta.44"
  },
  "license": "MIT",
  "devDependencies": {
    "chai": "^3.3.0",
    "mocha": "^2.3.3"
  }
}
```

Step7  
//install the library in bitcore folder  
```
cd ~/bitcore
npm install
```


Step8  
//create and edit bitcode-node.json
```
cd ~/bitcore && touch bitcore-node.json
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
        { "ip": { "v4": "127.0.0.1" } }
      ]
    },
    "fee": {
      "rpc": {
        "user": "bch",
        "pass": "Aa123456",
        "host": "localhost",
        "protocol": "http",
        "port": 8332
      }
    }
  }
}
```
**Step9 WATCH OUT THIS!!!!**  
//This is very important step to fix the bitcore's bug.  
//the bitcore-lib and bitcoinABC's version is not match,it will happen some configure problem like network(p2p).

//Part bitcoinABC     https://github.com/Bitcoin-ABC/bitcoin-abc/blob/master/src/chainparams.cpp  
//Please find the netMagic class CMainParams  
  netMagic[0] = 0xe3;  
  netMagic[1] = 0xe1;  
  netMagic[2] = 0xf3;  
  netMagic[3] = 0xe8;  

//Part bitcore-lib  
//**please change the networkMagic's value to 0xe3e1f3e8**
```
cd ~/bitcore/node_modules/bitcore-lib/lib  
vi networks.js 

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
```
Step10(options)
//Install pm2
```
cd ~
sudo npm install pm2 -g
```
//If the server reboot,pm2 will auto restart.
```
pm2 startup
sudo env PATH=$PATH:/usr/bin /usr/lib/node_modules/pm2/bin/pm2 startup systemd -u [user] --hp /home/[user]
pm2 save
```
//if you install pm2 please choose step7.1 to run the bitcoin-cash server.
Step 11.1
```
cd ~/bitcore
pm2 start ./bin/bitcored -- start
```
//if you didin't install pm2,please choose step7.2 to run the bitcoin-cash server.
Step11.2
```
cd ~/bitcore
./bin/bitcored start
```
//////Done!!!!///////
