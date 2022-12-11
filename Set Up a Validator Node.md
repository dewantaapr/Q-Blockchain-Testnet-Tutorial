# Set Up a Validator Node

[Original Documentation](https://docs.qtestnet.org/how-to-setup-validator/)

[Twitter](https://twitter.com/QBlockchain)

[Discord](https://discord.gg/d46ba3fcF9)

[Medium](https://medium.com/q-blockchain)

[Reddit](https://www.reddit.com/r/QBlockchain/)

## **Specification Requirement**

Minimum Operating System: ***Ubuntu 16.04***

Recommended Operating System: ***Ubuntu 18.04 or above***



## **Initial Set Up**



### **Install Dependency**

```
apt install git docker docker-compose
```

### **Download The Package**

```
git clone https://gitlab.com/q-dev/testnet-public-tools.git
```

### **Create a Wallet**


#### **Set Your Password**

```
cd testnet-public-tools/testnet-validator
mkdir keystore
nano keystore/pwd.txt
```

Write your Password in `pwd.txt`

You can check your wallet's password using this command

```
cat keystore/pwd.txt
```

#### **Create the Wallet**

Run this command

```
docker run --entrypoint="" --rm -v $PWD:/data -it qblockchain/q-client:testnet geth account new --datadir=/data --password=/data/keystore/pwd.txt
```

If successful, you will see this

```
Your new key was generated

Public address of the key:   0x00000000000000000000000000000 (Your Public Address)
Path of the secret key file: /data/keystore/UTC--0000-00-00000-00-00.0000000--000000000000000000000000000000 (Your Public Address)

- You can share your public address with anyone. Others need it to interact with you.
- You must NEVER share the secret key with anyone! The key controls access to your funds!
- You must BACKUP your key file! Without the key, it's impossible to access account funds!
- You must REMEMBER your password! Without the password, it's impossible to decrypt the key!
```

*Tips: Copy and save the key in a safe place, because you will need that key if you want to recover your account.*

#### **Export The Private Key**

```
cd testnet-public-tools
chmod +x run-js-tools-in-docker.sh
./run-js-tools-in-docker.sh
npm install
chmod +x extract-geth-private-key.js
node extract-geth-private-key.js
node extract-geth-private-key Wallet_Address ../testnet-validator/ Password
```

Change `Wallet_Address` with your Wallet Address (without leading 0x) and change the `Password` with your Password

Example:

```
node extract-geth-private-key 1bc00bbbffa3731513e104556765c3306d669e32 ../testnet-validator/ Password12345678
```

You can find the `.txt` file of your Private Key in this directory
`/testnet-public-tools/js-tools`

### **Claim The Faucet**

You can claim the faucet in this [site](https://faucet.qtestnet.org/).

*Note: If an error occurs, keep trying until it works*

### **Configuration**

To run the node, there need to be several files that are configured and adapted to the data you have.

First, you need to go to the right directory.

```
cd $HOME/testnet-public-tools/testnet-validator
```

#### `.env` Configuration

Run this command

```
nano .env
```

You will see this

```
# docker image for q client
QCLIENT_IMAGE=qblockchain/q-client:1.2.2

# your q address here (without leading 0x)
ADDRESS=00000000000000000000000000000000

# your public IP address here
IP=000.00.000.000
```

**Make sure** your docker image for q client is the latest, right now at ver 1.2.2

**Put** your `Public Wallet Address` (Without leading 0x) and your `VPS IP Address`

#### **`config.json` Configuration**

Run this command

```
nano config.json
```

You will see this

```
{
  "address": "000000000000000000000000000000",
  "password": "0000000000",
  "keystoreDirectory": "/data",
  "rpc": "https://rpc.qtestnet.org"
}
```

**Put** your `Public Wallet Address` (Without leading 0x) and your `Password`


### **Put the Stake in Your Validator Contract**

Run this command

```
cd $HOME/testnet-public-tools/testnet-validator
docker run --rm -v $PWD:/data -v $PWD/config.json:/build/config.json qblockchain/js-interface:testnet validators.js
```


### **Register Your Validator**

Run this command

```
cd $HOME/testnet-public-tools/testnet-validator
nano docker-compose.yaml
```

You will see this
```
services:
  testnet-validator-node:
    image: $QCLIENT_IMAGE
    entrypoint: ["geth", "--ethstats=NAMA_VALIDATOR:TESTNET_ACCESS_KEY@stats.qtestnet.org", "--datadir=/data", "--nat=extip:$IP", "--port=$EXT_PORT", "--unlock=$ADDRESS",  "--password=/data/keystor>
    volumes:
      - ./keystore:/data/keystore
      - ./additional:/data/additional
      - testnet-validator-node-data:/data
    ports:
      - $EXT_PORT:$EXT_PORT/tcp
      - $EXT_PORT:$EXT_PORT/udp
    restart: unless-stopped

volumes:
  testnet-validator-node-data:
```

In the `entrypoint` section, after `"geth"` there is `"--ethstats:=VALIDATOR_NAME:TESTNET_ACCESS_KEY@stats.qtestnet.org"`

Change `VALIDATOR_NAME` with your own Validator Name

And change `TESTNET_ACCESS_KEY` with `the key` that you can find in `#🔑|testnet-key` in the [QBlockchain's Discord Server](https://discord.gg/d46ba3fcF9)


### **Run Your Validator**

Run this command

```
docker-compose up -d
```
