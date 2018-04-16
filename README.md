 Blockchain APIs for Tealcoin Insight Explorer

[![NPM Package](https://img.shields.io/npm/v/tealcoin-explorer-api.svg?style=flat-square)](https://www.npmjs.org/package/tealcoin-explorer-api)
[![Build Status](https://img.shields.io/travis/bitpay/bitcore-explorers.svg?branch=master&style=flat-square)](https://travis-ci.org/bitpay/bitcore-explorers)
[![Coverage Status](https://img.shields.io/coveralls/bitpay/bitcore-explorers.svg?style=flat-square)](https://coveralls.io/r/bitpay/bitcore-explorers)

A module for [Tealcoin Insight Explorer](https://tealcoin-project.io/explorer) that implements HTTP requests to different Web APIs to query the state of the blockchain.

## Getting started

Be careful! When using this module, the information retrieved from remote servers may be compromised and not reflect the actual state of the blockchain.

```sh
npm install tealcoin-explorer-api
```

This API support for both tealcoin and bitcoin blockchain network. You can query both tealcoin and bitcoin blockchain data.
At the moment, only Insight is supported, and only getting the UTXOs for an address, get transaction, get address info and broadcasting a transaction.

```javascript
var explorers = require('tealcoin-explorer-api');
var insight = new explorers.Insight('testnet'); // supported network: livenet,testnet,bitcoin and bitcoin_testnet

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
var explorers = require('tealcoin-explorer-api');
var insight = new explorers.Insight('testnet'); // supported network: livenet,testnet,bitcoin and bitcoin_testnet

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
var explorers = require('tealcoin-explorer-api');
var insight = new explorers.Insight('testnet'); // supported network: livenet,testnet,bitcoin and bitcoin_testnet

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
var explorers = require('tealcoin-explorer-api');
var insight = new explorers.Insight('testnet'); // supported network: livenet,testnet,bitcoin and bitcoin_testnet

var insight = new Insight('testnet');
insight.broadcast(tx, function(err, returnedTxId) {
  if (err) {
    // Handle errors...
  } else {
    // Mark the transaction as broadcasted
  }
});
```

#### Create & send a Transaction example

```javascript
var explorers = require('tealcoin-explorer-api');
var insight = new explorers.Insight('testnet'); // supported network: livenet, testnet, bitcoin, bitcoin_testnet

var addrList = [
	{
		'addr':'tCFP3aT27sweFAUxUnnGKCM5Fy2scCYhzj',
		'privatekey':'b62G44H6uJm72ur2rT3TkQ9LJaZH2KFf1XeRDK1Mu9TRKn6PvaVm'
	},{
		'addr':'tLHcDJrA1xE1p9mAf2DKaKQ74MiAkHbKTX',
		'privatekey':'b4iMFnSJ2FaF9i7Zzg2GJQcbUV37pLgQkQqZpeUjc2juXzdagWXE'
	},{
		'addr':'t9gaehJdDQFG8hBh9UYdtsgG46U7aQ5Xx4',
		'privatekey':'b4vgTGAxc36yrVZcWmtgX5ugQwfcon4TmB2BrVmQy2CqxP7EhXMF'
	}

];

var fromAddr = addrList[0];
var toAddr = addrList[1];
var fee = 5000000;

var privateKey = new explorers.litecore_tealcoin_lib.PrivateKey(fromAddr.privatekey);
// console.log(privateKey.toWIF());

insight.getUtxos(fromAddr.addr, function(err, utxos) { // tealcoin testnet
  if (err) {
    console.log(err);
  } else {
	//console.log(utxos);
	var transaction = new explorers.litecore_tealcoin_lib.Transaction()
		.from(utxos)          // Feed information about what unspent outputs one can use
		.to(toAddr.addr, 50000000)  // Add an output with the given amount of satoshis
		.fee(fee)
		.change(fromAddr.addr)      // Sets up a change address where the rest of the funds will go
		.sign(privateKey)     // Signs all the inputs it can

	//console.log(transaction);
	var txSerialized = transaction.serialize(true);
	//console.log(txSerialized);
	//console.log(transaction.getChangeOutput());
	insight.broadcast(txSerialized, function(err, returnedTxId) {
	  if (err) {
		console.log(err);
	  } else {
		console.log("Success with txid: " + returnedTxId);
	  }
	});
  }
});
```

## Contributing

See [CONTRIBUTING.md](https://github.com/bitpay/bitcore/blob/master/CONTRIBUTING.md) on the main bitcore repo for information about how to contribute.

## License

Code released under [the MIT license](https://github.com/bitpay/bitcore/blob/master/LICENSE).

Copyright 2013-2015 BitPay, Inc. Bitcore is a trademark maintained by BitPay, Inc.

[bitcore]: http://github.com/bitpay/bitcore-explorers
