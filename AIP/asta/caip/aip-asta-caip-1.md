---
aip: aip-asta-caip-1
title: CAIP based chainId, accountId, assetId formatting
status: Draft
type: Standard
author: Piyush Jaiswal <iampiyushjaiswal103@gmail.com>
created: 2022-09-13
updated: 2022-09-13
---


## Abstract
The representation of blockchain's `chainId`, `accountId` or `assetId` must be complaint to the `CAIP`. The [issue](https://github.com/Byta-Tigris/almight-decision/issues/7) covers the expected requirements from the implementation of CAIP.

## Specification
The functionalities expected from CAIP are
- Formatting
- Parsing
- Pattern Matching

**The described specifications are best suited for Javascript/Typescript language**
### Requirements
- `caip` library from `npm` for JS/TS. (If developing on another language this can be skipped, but alternate solutions should be developed accordingly).
    *   ` npm i caip `

The data-structure of each identifier is provided below. Each identifier must hold only required amount of data and must be easily convertible between `string` and `data-structure`
```ts
interface ChainId {
    namespace: string
    reference: string
}

interface AccountId {
    address: string;
    chainId: ChainId;
}

interface AssetName {
    namespace: string;
    reference: string;
}

interface AssetType {
    chainId:  ChainId;
    assetName: AssetName
}

interface AssetId {
    chainId: ChainId;
    assetName: AssetName;
    tokenId: string;
}

```

### Object Representation
A composite object capable of representing each identifier can be created as
```ts
interface Caip {

    namespace: string;
    reference: string;
    chainId: ChainId;
    address?: string;
    assetName?: AssetName;
    tokenId?: string;


}
```

* The `CAIP` object is mutated form of `AssetId`. `ChainId` is the most basic form of identifier and each **CAIP** object must contain a **ChainId**. 

* Both `ChainId` and `AssetName` contains property `namespace` and `reference`. In case of conflict i.e when both the identifiers are present in the same object, the **AssetName** will be given the property to store **namespace** and **reference** data directly in the property while storing the **ChainId** in `chainId` property. 

* Constructor of the CAIP object can accept an `Object`, `string` or `Array<string>`.

### Required getters
```ts
class Caip {

    getChainId(): ChainId;
    getChainIdString(): string;

    getAccountId(): AccountId;
    getAccountIdString(): string;

    getAssetName(): AssetName;
    getAssetNameString(): string;

    getAssetType(): AssetType;
    getAssetTypeString(): string;

    getAssetId(): AssetId;
    getAssetIdString(): string;

}
```


### Formatting
Formatting handles the conversion of data into CAIP defined string. Each string must ally with the defined format.

- Each identifier must have a method called `format` returning the formatted string.

- A `getFormatterClass` method to handle the allocation of identifier class for formatting. This method will determine which identifier object will applicable according to the properties.

    ```ts 
    // getFormatterClass

    // If tokenId is present then by default the CAIP is representing AssetId
    if(data.tokenId !== undefined){
        return AssetId;
    }
    // If tokenId is not present, but assetName is present along with chainId
    else if(data.chainId !== undefined && data.assetName !== undefined){
        return AssetType;
    }
    // If address is present along with chainId then AccountId
    else if(data.address !== undefined){
        return AccountId;
    }
    return ChainId;
    ```

- `CAIP` object must have a `format` method which handles the execution of complete formatting process.
    ```ts
    // Return formatted string of identifier
    this.getFormatterClass().format()
    ```

### Parsing
Parsing handles conversion of formatted string to required identifier object. The objective of this functionality is to validate the string according to the protocol and then extract the valuable information.

- `CAIP` object must have a **static** `parse` method, accepting a `string` parameter and returning another `CAIP` object instance containing the extracted information.

- `fragment` is function that splits the string into an array using delimiter. The two used delimiter in the `CAIP` protocol are `/` and `:`.
    ```python
    protocolString: str

    splashSplit = protocolString.split("/")
    colonSplit = []
    for chunk in splashSplit:
        colonSplit = colonSplit + chunk.split(":")
    
    colonSplit # completely separated values from the protocol
    ```
- The length of the array returned after **fragment** operation contains detail about identifier.
    ```ts
    const frag: Array<string> = fragment(str)
    switch(frag.length){
        case 5:
            return AssetId;
        case 4:
            return AssetType;
        case 3:
            return AccountId;
        default:
            return ChainId;
    }
    ```
- The setup of each properties according to the identifier object is crucial. 
    * `AssetId` will set `AssetType` and `tokenId` automatically.
    * `AssetType` will set `assetName` and `chainId` automatically.
    * `AccountId` will set `address` and `chainId`.
    * If `assetName` is a string then it will be converted to `AssetName` object.
    * `assetName` or `chainId` will set `namespace` and `reference` properties based on the priority rule stated above in the same document.
- Each identifier object must validate the provided values according to the regex, and shall only allow `*` to escape the validation.

### Pattern Matching
The original caip packages was extended in-order to support pattern matching against a wildcard character `*`. Wildcard character will accept any value (which adhere's with the provided regex).

- Matching will performed against
    * chainId.namespace
    * chainId.reference
    * address
    * assetName.namespace
    * assetName.reference
    * tokenId
- All of the above value must be a string, valid regex and equal if not a wildcard.
    ```ts
    function _match(comparator: string, target: string, regex?: string): boolean {
        // ValidateRegex is a function which accept regex and a value to validate against the provided regex.
        // Should not allow `*` to escape the validation.
        if(regex !== undefined && validateRegex(regex, target) === false){
            throw new Error('')
        }

        if(comparator === '*'){
            return true;
        }
        return comparator === target;

    }
    ```
- caip object must have `match` method which accepts `string`, `Object` or `Array<string>`.
    * If **Array** or **string** are provided then they'll be converted to a CAIP object.
    * The method will inspect each property stated above one-by-one using the `_match` function.
    * The desired regex can be found in the identifier object as a static property `regex`.



## Test Cases
```ts

new CAIP('eip155:1') // CAIP{ namespace: "eip155", reference: "1" }
new CAIP("eip155:1:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb");
// CAIP{ address: "0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb", 
// chainId: { namespace: "eip155", reference: "1" },
// namespace: "eip155", reference: "1"
// }

new CAIP("eip155:1/erc721:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb/1");
// CAIP{
//   chainId: { namespace: "eip155", reference: "1" },
//   assetName: { namespace: "erc721", reference: "0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb" },
//   tokenId: "1",
//   namespace: "erc721", reference: "0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb"
// }

new CAIP("eip155:1/erc721:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb");
// CAIP{
//   chainId: { namespace: "eip155", reference: "1" },
//   assetName: { namespace: "erc721", reference: "0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb" },
//   namespace: "erc721", reference: "0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb"
// }

new CAIP(["eip155","1","erc721","0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb","1"]);
// CAIP{
//   chainId: { namespace: "eip155", reference: "1" },
//   assetName: { namespace: "erc721", reference: "0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb" },
//   tokenId: "1",
//   namespace: "erc721", reference: "0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb"
// }


// Matching
let pattern = CAIP.pattern('eip155:*')

pattern.match('eip155:56') // true
pattern.match('eip155:5698') // true
pattern.match('cosmos:1') // false
pattern.match('eip155:2:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb') //false

let pattern = CAIP.pattern('eip155:1:*')
pattern.match('eip155:56') // false
pattern.match('eip155:1') // false
pattern.match('eip155:1:258') // true
pattern.match('cosmos:1') // false
pattern.match('eip155:1:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb') // true

let pattern = CAIP:pattern('eip155:*:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb')

pattern.match('eip155:56') // false
pattern.match('eip155:1') // false
pattern.match('cosmos:1') // false
pattern.match('eip155:1:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb') // true
pattern.match('eip155:1:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfc562') // false
pattern.match('eip155:1:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb') // true

let pattern = CAIP:pattern('eip155:*:*')
pattern.match('eip155:56') // false
pattern.match('eip155:1') // false
pattern.match('cosmos:1') // false
pattern.match('eip155:1:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb') // true
pattern.match('eip155:1:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfc562') //true
pattern.match('eip155:1:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb') // true

let pattern = CAIP.pattern('eip155:1/*:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb')
pattern.match('eip155:1/erc721:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb') // true


// Formatting
// Formatting 
new CAIP({namespace: 'eip155', reference: '1'}).format() 
 // "eip155:1"

new CAIP({
  chainId: { namespace: "eip155", reference: "1" },
  address: "0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb",
}).format()    
// "eip155:1:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb"

// ALSO
new CAIP({
  chainId: "eip155:1",
  address: "0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb",
}).format()
// "eip155:1:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb"


new CAIP({
  chainId: { namespace: "eip155", reference: "1" },
  assetName: {
    namespace: "erc721",
    reference: "0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb",
  },
  tokenId: "1",
}).format()

// ALSO

new CAIP({
  chainId: "eip155:1",
  assetName: "erc721:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb",
  tokenId: "1",
}).format();


// "eip155:1/erc721:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb/1"
```

