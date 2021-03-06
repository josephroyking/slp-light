# slp-light

slp-light is a lightweight library for creating a slp-token transaction. It has few exposed methods to make sure it is as 
easy as possible to create a token transaction, hiding all complexity from the user. 

# Installation
```bash
npm install slp-light
```

# Examples

### Fetching utxos
To fetch utxos use the method `retrieveUtxos()` which lets the user select the source of the utxos. Here is an 
example using public available apis from [Bitcoin.com](https://rest.bitcoin.com).

Using `BitcoinComRetrieverImpl`
```ts
import { Utxo } from './src/Utxo'; 
import { retrieveBchUtxos, retrieveSlpUtxos } from './src/index'; 
import { BitcoinComBchRetriever, BitcoinComSlpRetriever } from './src/facade/bitcoincom/';

let bch: Utxo[] = await retrieveBchUtxos({cashAddress: "bitcoincash:qzm4u38umtw6ak4was24r6ucerkzzxqr5s2328xm0r", wif: "WIF"}, new BitcoinComBchRetriever());
let slp: Utxo[] = await retrieveSlpUtxos({cashAddress: "bitcoincash:qzm4u38umtw6ak4was24r6ucerkzzxqr5s2328xm0r", wif: "WIF"}, new BitcoinComSlpRetriever());
```

Using custom `UtxoRetrieverFacade`. When using a custom retriever make sure the source only allows ***valid slp-transactions***. Since the library
does not have its own validation it relies on the utxo source for validation.
```ts
import { retrieveBchUtxos, retrieveSlpUtxos } from './src/index'; 
import {Address, Utxo } from './src/Utxo'; 
import { BchUtxoRetrieverFacade } from './src/facade/UtxoRetrieverFacade'; 

const retriever: BchUtxoRetrieverFacade = {
        getBchUtxosFromAddress(address: Address): Promise<Utxo[]> {
            // Custom utxo retriever code goes here
            return Promise.resolve([]);
        }
}
const utxos: Utxo[] = await retrieveBchUtxos({cashAddress: "bitcoincash:qzm4u38umtw6ak4was24r6ucerkzzxqr5s2328xm0r", wif: "WIF"}, retriever);
```

### Selecting utxos
```ts
import { selectUtxos } from './src/index'; 
import { SelectedUtxos } from './src/index'; 
import BigNumber from 'bignumber.js'; 
import {Utxo } from './src/Utxo'; 

const utxos: Utxo[] = [];
const selectUtxo: SelectedUtxos = selectUtxos(new BigNumber("5"), "323437d4c86b00874c3b00cd454ab6ffb3226130fde09747009cf270caedddcf", utxos);
```


### Creating transaction
```ts
import { createRawTx } from './src/index'; 
import BigNumber from 'bignumber.js'; 
import { SelectedUtxos } from './src/utxo'; 

const mySelectedUtxos: SelectedUtxos = null;
const rawTx = createRawTx(new BigNumber("5"), "dcf128f7f836f369d339963685e91b105cf7982d8977d09f6a776329a6e290e7",
        "bitcoincash:qrve2j5h2f8hy9hlptu7ejltzf4m7fwees60qss5f4",
        "bitcoincash:qzm4u38umtw6ak4was24r6ucerkzzxqr5s2328xm0r",
        mySelectedUtxos);
```

### Broadcast Transaction
You can either use one of the premade broadcasting facades implementing the interface or implement your own as shown below.
```ts
import { broadcastTransaction } from './src/index'; 
import { BroadcastFacade } from './src/facade/BroadcastFacade';
 
const broadcastFacade: BroadcastFacade = {
        broadcastTransaction(raw: string): Promise<string> {
            return Promise.resolve('BROADCAST REQUEST GOES HERE');
        }
}
const rawTx = broadcastTransaction("TX_RAW", broadcastFacade);
```

### Using simpleSend
If you just want to broadcast a transaction using [Bitcoin.com](https://rest.bitcoin.com) apis then you can use
the `simpleSend()` function from `simple.ts`. This method is an abstraction on top of the other methods for simplicity.
If you want more control use the other methods in combination with each other.
```ts
import { simpleSend } from './src/simple'; 
import BigNumber from 'bignumber.js'; 

const txId = await simpleSend("Kzm1oz8sLSvuQLEgu99xixSkhNj7kyBZe6TpqTfdgKGhXUeSLSR",
            "simpleledger:qp2mdqc4r8ylsrnu9f4j5xu8r47m722f6uyfsz4zr6",
            "323437d4c86b00874c3b00cd454ab6ffb3226130fde09747009cf270caedddcf",
            new BigNumber(300));
```

# Build & Test

## Build
```bash
npm run build
```

## Test
```bash
npm run test:npx
```

## Authors

* **Andreas Larsson** - [AndreasLarssons](https://github.com/AndreasLarssons)  
* **Jt Freeman** - [blockparty-sh](https://github.com/blockparty-sh)
