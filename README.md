# eth_dev_bootcamp

## PUBLIC KEY CRYPTOGRAPHY

### 1: Hash Message
```JS
const { keccak256 } = require("ethereum-cryptography/keccak");
const { utf8ToBytes } = require("ethereum-cryptography/utils");

function hashMessage(message) {
    return keccak256(utf8ToBytes(message))
}

module.exports = hashMessage;
```

### 2: Sign Message
sign a message using private key and reutrn a recover bit
```JS
const secp = require("ethereum-cryptography/secp256k1");
const hashMessage = require('./hashMessage');
const { toHex } = require("ethereum-cryptography/utils");

const PRIVATE_KEY = "6b911fd37cdf5c81d4c0adb1ab7fa822ed253ab0ad9aa18d77257c88b29b718e";

async function signMessage(msg) {
    return secp.sign(hashMessage(msg), PRIVATE_KEY, { recovered: true })
}

module.exports = signMessage;
```

### 3: Recover the Public Key
```JS
const secp = require("ethereum-cryptography/secp256k1");
const hashMessage = require("./hashMessage");

async function recoverKey(message, signature, recoveryBit) {
    return secp.recoverPublicKey(hashMessage(message), signature, recoveryBit)
}

module.exports = recoverKey;
```

### 4: Public Key to Address
Bitcoin and Ethereum both have a transformation process to take a public key and turn it into an address. For Bitcoin it includes a checksum and Base58 encoding. Ethereum's address transformation is quite a bit simpler, its address is the last 20 bytes of the hash of the public key.
```JS
//First step, you'll need to take the first byte off the public key. The first byte indicates the format of the key
const secp = require("ethereum-cryptography/secp256k1");
const { keccak256 } = require("ethereum-cryptography/keccak");

function getAddress(publicKey) {
    return keccak256(publicKey.slice(1)).slice(-20)
}

module.exports = getAddress;
```

### BUILD A MINER. 1: Add Transaction into Mempool
```JS
const SHA256 = require('crypto-js/sha256');
const TARGET_DIFFICULTY = BigInt(0x0fffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff);
const MAX_TRANSACTIONS = 10;

const mempool = [];
const blocks = [];

function addTransaction(transaction) {
    // TODO: add transaction to mempoole
    return mempool.push(transaction);
}

module.exports = {
    TARGET_DIFFICULTY,
    MAX_TRANSACTIONS,
    addTransaction, 
    mine, 
    blocks,
    mempool
};
```

### BUILD A MINER. 2: Mining Blocks
```JS
const SHA256 = require('crypto-js/sha256');
const TARGET_DIFFICULTY = BigInt(0x0fffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff);
const MAX_TRANSACTIONS = 10;

const mempool = [];
const blocks = [];

function addTransaction(transaction) {
    // TODO: add transaction to mempoole
    return mempool.push(transaction);
}

function mine() {
    // TODO: mine a block
    const block = { id: blocks.length }
    blocks.push(block);
}

module.exports = {
    TARGET_DIFFICULTY,
    MAX_TRANSACTIONS,
    addTransaction, 
    mine, 
    blocks,
    mempool
};
```
### BUILD A MINER. 3: Block Hash
```JS
const SHA256 = require('crypto-js/sha256');
const TARGET_DIFFICULTY = BigInt(0x0fffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff);
const MAX_TRANSACTIONS = 10;

const mempool = [];
const blocks = [];

function addTransaction(transaction) {
    // TODO: add transaction to mempoole
    return mempool.push(transaction);
}

function mine() {
    // TODO: mine a block
    const sha256 = SHA256(JSON.stringify({ id: blocks.length }));
    const block = { id: blocks.length, hash: sha256 }
    blocks.push(block);
}

module.exports = {
    TARGET_DIFFICULTY,
    MAX_TRANSACTIONS,
    addTransaction, 
    mine, 
    blocks,
    mempool
};
```
### BUILD A MINER. 4: Mine TX
```JS
const SHA256 = require('crypto-js/sha256');
const TARGET_DIFFICULTY = BigInt(0x0fffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff);
const MAX_TRANSACTIONS = 10;
const mempool = [];
const blocks = [];

function addTransaction(transaction) {
    mempool.push(transaction);
}

function mine() {
    let transactions = [];
    while (transactions.length < MAX_TRANSACTIONS && mempool.length > 0) {
        transactions.push(mempool.pop());
    }
    const block = { id: blocks.length, transactions }
    const hash = SHA256(JSON.stringify(block));
    blocks.push({ ...block, hash })
}

module.exports = {
    TARGET_DIFFICULTY,
    MAX_TRANSACTIONS,
    addTransaction,
    mine,
    mempool,
    blocks,
};
```
### BUILD A MINER. 4: Target Difficulty
```JS
const SHA256 = require('crypto-js/sha256');
const TARGET_DIFFICULTY = BigInt(0x0fffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff);
const MAX_TRANSACTIONS = 10;
const mempool = [];
const blocks = [];

function addTransaction(transaction) {
    mempool.push(transaction);
}

function mine() {
    let transactions = [];
    while (transactions.length < MAX_TRANSACTIONS && mempool.length > 0) {
        transactions.push(mempool.pop());
    }
    const block = { id: blocks.length, transactions}
    block.nonce = 0;
    let hash;
    while (true) {
        hash = SHA256(JSON.stringify(block)).toString();
        if (BigInt(`0x${hash}`) < TARGET_DIFFICULTY) {
            break;
        }
        block.nonce++;
    }
    blocks.push({ ...block, hash })
}

module.exports = {
    TARGET_DIFFICULTY,
    MAX_TRANSACTIONS,
    addTransaction,
    mine,
    mempool,
    blocks,
};
```
How is the block hash calculated?   
```
f ( data ) = hash
f ( index + previous hash + timestamp + data + nonce ) = hash
f ( 0 + "0" + 1508270000000 + "Welcome to Blockchain Demo 2.0!" + 604 ) = 000dc75a315c77a1f9c98fb6247d03dd18ac52632d7dc6a9920261d8109b37cf
```
![image](https://user-images.githubusercontent.com/24216536/227890774-56014b4d-9e66-4d74-be29-c2919f590ce9.png).   
