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
```  
Step3
//Install bitcore locally
```
sudo apt-get install libzmq3-dev build-essential
git clone -b v5.x https://github.com/bitpay/bitcore.git
cd bitcore
npm install
```
Step4
//bitcore create bitcoin cash project
```
cd ~
./bitcore/bin/bitcore create bitcoin-cash-node
cd bitcoin-cash-node
npm install
cd node_modules
git clone -b cash https://github.com/bitpay/insight-api.git
cd insight-api
npm install
```
Step5
//edit bitcode-node.json
```
cd ~/bitcoin-cash-node
vi bitcore-node.json

{
  "version": "5.0.0-beta.44",
  "network": "livenet",
  "port": 3001,
  "services": [
    "insight-api",
    "address",
    "block",
    "db",
    "fee",
    "header",
    "mempool",
    "p2p",
    "timestamp",
    "transaction",
    "web"
  ],
  "datadir":"[where you want to store the node data]",
  "servicesConfig": {
    "insight-api": {
      "cwdRequirePath": "node_modules/insight-api"
    }
  }
}
```
Step6(options)
//Install pm2
```
cd ~
sudo npm install pm2 -g
```
//If the server reboot,pm2 will auto restart.
```
pm2 startup
sudo env PATH=$PATH:/usr/bin /usr/lib/node_modules/pm2/bin/pm2 startup systemd -u ubuntu --hp /home/ubuntu
pm2 save
```
//if you install pm2 please choose step7.1 to run the bitcoin-cash server.
Step 7.1
```
cd ~/bitcoin-cash-node
pm2 start ~/bitcore/bin/bitcore -- start
```
//if you didin't install pm2,please choose step7.2 to run the bitcoin-cash server.
Step7.2
```
cd ~/bitcoin-cash-node
~/bitcore/bin/bitcore start
```
//////Done!!!!///////

Check Something
```
bitcoin-cash-node/package.json

{
  "description": "A full Bitcoin node build with Bitcore",
  "repository": "https://github.com/user/project",
  "license": "MIT",
  "readme": "README.md",
  "dependencies": {
    "bitcore-lib": "^v5.0.0-beta.1",
    "bitcore-node": "^5.0.0-beta.44"
  }
}
```