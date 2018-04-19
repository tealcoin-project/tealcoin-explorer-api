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

#### Get UTXOs

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

#### Create & send a Transaction

```javascript
var explorers = require('tealcoin-explorer-api');
var useNetwork = 'testnet'; // supported network: livenet, testnet, bitcoin, bitcoin_testnet
var insight = new explorers.Insight(useNetwork);

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
	},{
		'addr':'tMxzJ8kUkFUJPssgC2y7XrFCavaujKWvTL',
		'privatekey':'8jdMiNDh4XrHGncGwJnFp2SKPSoj8x15QwctpRpEuS12oxBKRoH'
	},{
		'addr':'tJXgQZSpm4U87dnPxsaxyL1NSxAaBz2ivE',
		'privatekey':'b9JJZd93vK9qriasqSEmze756nvazmrhtoLgLPzyS1rf77oHAYQu'
	},{
		'addr':'tS7SqUMfhmkwKHeu8qr17CKXQE5wpcgHm1',
		'privatekey':'6PfRigXELtB4H52gHnssPLM9BoCquZN725MvKamFb7Hv1mXnFjyX2L84Ei', // bip38 encrypted
		'bip38Passphrase': 'testingtesting'
	}

];

var fee = 5000000;
var fromAddr = addrList[5];
var toAddr = addrList[0];

if(explorers.bip38.verify(fromAddr.privatekey)) {
	if(typeof fromAddr.bip38Passphrase !== 'undefined') {
		fromAddr.privatekey = explorers.bip38.decrypt(fromAddr.privatekey, fromAddr.bip38Passphrase, function(status) {
			console.log(status);
		}).privateKey;
	} else {
		console.log('Error: No bip38Passphrase...');
		return;
	}
}

var err = explorers.litecore_tealcoin_lib.PrivateKey.getValidationError(fromAddr.privatekey,useNetwork);
if(err) {
	console.log(err);
	return;
}

var privateKey = new explorers.litecore_tealcoin_lib.PrivateKey(fromAddr.privatekey,useNetwork);
// console.log(privateKey.toWIF());
// console.log(privateKey.toAddress().toString());
if(privateKey.toAddress().toString() !== fromAddr.addr) {
	console.log('Error: Private Key not match!');
	return;
}

insight.getUtxos(fromAddr.addr, function(err, utxos) { // tealcoin testnet
  if (err) {
    console.log(err);
  } else {
	//console.log(utxos);
	var transaction = new explorers.litecore_tealcoin_lib.Transaction()
		.from(utxos)          // Feed information about what unspent outputs one can use
		.to(toAddr.addr, 100000000)  // Add an output with the given amount of satoshis
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

## Building the Browser Bundle

To build a tealcoin-explorer-api full bundle for the browser:

```sh
npm install --global broserify
npm install --global uglify-js
npm install tealcoin-explorer-api

cd tealcoin-explorer-api
browserify --require ./index.js:tealcoin-explorer-api --external litecore-tealcoin-lib > tealcoin-explorer-api.js
uglifyjs --compress --mangle --rename tealcoin-explorer-api.js > tealcoin-explorer-api.min.js
```

This will generate files named `tealcoin-explorer-api.js` and `tealcoin-explorer-api.min.js`.

Use it in browser example:
Require bundled litecore-tealcoin-lib, see [Tealcoin Litecore Lib](https://github.com/tealcoin-project/litecore-tealcoin-lib) for bundle instructions.

```
<html>
	<body>
	</body>
	<script src='./litecore-tealcoin-lib.min.js'></script>
	<script src='./tealcoin-explorer-api.min.js'></script>
	<script type="text/javascript">
		document.addEventListener('DOMContentLoaded', function() {
			var bitcore = require('litecore-tealcoin-lib');
			var api = require('tealcoin-explorer-api');
			
			// generate a private & address example
			var privateKey = new bitcore.PrivateKey('testnet');
			var wif = privateKey.toWIF();

			var address = privateKey.toAddress();

			console.log(privateKey.toString());
			console.log(wif);
			console.log(address.toString());

			// get UTXOs
			var insight = new api.Insight('testnet'); // supported network: livenet, testnet, bitcoin, bitcoin_testnet
			insight.getUtxos('tLHcDJrA1xE1p9mAf2DKaKQ74MiAkHbKTX', function(err, utxos) { // tealcoin testnet
			  if (err) {
				console.log(err);
			  } else {
				console.log(utxos);
			  }
			});
		}, false);
	</script>
</html>

```

## Contributing

See [CONTRIBUTING.md](https://github.com/bitpay/bitcore/blob/master/CONTRIBUTING.md) on the main bitcore repo for information about how to contribute.

## License

Code released under [the MIT license](https://github.com/bitpay/bitcore/blob/master/LICENSE).

Copyright 2013-2015 BitPay, Inc. Bitcore is a trademark maintained by BitPay, Inc.

[bitcore]: http://github.com/bitpay/bitcore-explorers
