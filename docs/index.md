 Blockchain APIs for Tealcoin Insight Explorer

A module for [Tealcoin Insight Explorer](https://tealcoin-project.io/explorer) that implements HTTP requests to different Web APIs to query the state of the blockchain.

## Getting started

Be careful! When using this module, the information retrieved from remote servers may be compromised and not reflect the actual state of the blockchain.

```sh
npm install tealcoinjs-explorer
```

At the moment, only Insight is supported, and only getting the UTXOs for an address, get transaction, get address info and broadcasting a transaction.

```javascript
var explorers = require('tealcoinjs-explorer');
var insight = new explorers.Insight('testnet');

insight.getUtxos('tKpkm7WGLWdEaKUU9UCvyb917jE2nxLDCB', function(err, utxos) {
  if (err) {
    // Handle errors...
  } else {
    // Maybe use the UTXOs to create a transaction
  }
});
```

#### Get Address Info

```javascript
var explorers = require('tealcoinjs-explorer');
var insight = new explorers.Insight('testnet');

insight.address('fmLYw2BuhCQ9T1pyJZkYXi8pCq7bAvfN1a', function(err, addrinfo) {
  if (err) {
    console.log('e:'+err);
  } else {
    console.log(addrinfo);
  }
});
```

#### Get Transaction

```javascript
var explorers = require('tealcoinjs-explorer');
var insight = new explorers.Insight('testnet');

insight.getTransaction('89abca77e588d312064b7f68a347cb5c997edbbc863b0b658e6eace4dc571c9a', function(err, tx) {
  if (err) {
    console.log(err);
  } else {
    console.log(tx);
  }
});
```

#### Broadcasting a Transaction

```javascript
var explorers = require('tealcoinjs-explorer');
var insight = new explorers.Insight('testnet');

var insight = new Insight('testnet');
insight.broadcast(tx, function(err, returnedTxId) {
  if (err) {
    // Handle errors...
  } else {
    // Mark the transaction as broadcasted
  }
});
```

## Contributing

See [CONTRIBUTING.md](https://github.com/bitpay/bitcore/blob/master/CONTRIBUTING.md) on the main bitcore repo for information about how to contribute.

## License

Code released under [the MIT license](https://github.com/bitpay/bitcore/blob/master/LICENSE).

Copyright 2013-2015 BitPay, Inc. Bitcore is a trademark maintained by BitPay, Inc.

[bitcore]: http://github.com/bitpay/bitcore-explorers
