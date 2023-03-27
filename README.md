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
```JS

```
