---
lip: 3
title: Universal Profile
author: Fabian Vogelsteller <fabian@lukso.network> 
discussions-to: https://discord.gg/E2rJPP4
status: Draft
type: LSP
created: 2019-07-12
requires: LSP1, LSP2, ERC165, ERC173, ERC725Account, ERC1271
---


## Simple Summary

This standard describes a set of [ERC725Y](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-725.md) key value stores that are useful to describe a smart contract based profile.
 
## Abstract

This standard, defines a set of key value stores that are useful to create a public on-chain profile, based on an [ERC725Account](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-725.md).
Additionally this standards expects [LSP1-UniversalReceiver](https://github.com/lukso-network/LIPs/blob/master/LSPs/LSP-1-UniversalReceiver.md) to be implemented as well, to make the smart contract account future proof.

## Motivation

To make the usage of Blockchain infrastructures easier and allow smart contract based accounts to be more than just a store of assets.
We need to define standards that make these accounts easier to use and interact-able. Therefore we need to define:

- Ways to make security and access to these accounts upgradeable through the use of ERC173 and ERC725X
- Allow these accounts to store information and reference other related systems through the use of ERC725Y
- Define a number of key values stores that any interface can expect to present through this standard (LSP3)  

On top of that many token standards (like ERC721, ERC223 and ERC777) do define their own way of calling contracts.
To rectify this, we propose the usage of [LSP1-UniversalReceiver](https://github.com/lukso-network/LIPs/blob/master/LSPs/LSP-1-UniversalReceiver.md) and modified versions of ERC777 and other token standards,
to be used with this standard. This is obviously easier on EVM networks that have not yet an established ecosystem like LUKSO, than on the Ethereum mainnet.
Though we still think this addition is extremly necessary even on networks like Ethereum, as more standards are appearing that require to notify other smart contracts. 


## Specification

Every contract that supports to the Universal Profile standard SHOULD implement:

### Keys


#### SupportedStandards:ERC725Account

The supported standard SHOULD be `ERC725Account`

```json
{
    "name": "SupportedStandards:ERC725Account",
    "key": "0xeafec4d89fa9619884b6b89135626455000000000000000000000000afdeb5d6",
    "keyType": "Mapping",
    "valueContent": "0xafdeb5d6",
    "valueType": "bytes"
}
```

#### LSP1UniversalReceiverDelegate

If the account delegates its universal receiver to another smart contract,
this smart contract address SHOULD be stored at the following key:

```json
{
    "name": "LSP1UniversalReceiverDelegate",
    "key": "0x0cfc51aec37c55a4d0b1a65c6255c4bf2fbdf6277f3cc0730c45b828b6db8b47",
    "keyType": "Singleton",
    "valueContent": "Address",
    "valueType": "address"
}
```


#### LSP3Profile

A JSON file that describes the profile information, including profile image, background image, description and related links.

```json
{
    "name": "LSP3Profile",
    "key": "0x5ef83ad9559033e6e941db7d7c495acdce616347d28e90c7ce47cbfcfcad3bc5",
    "keyType": "Singleton",
    "valueContent": "JSONURL",
    "valueType": "bytes"
}
```

For construction of the JSONURL value see: [ERC725Y JSON Schema](https://github.com/lukso-network/LIPs/blob/master/LSPs/LSP-2-ERC725YJSONSchema.md#jsonurl-example)

The linked JSON file SHOULD have the following format:

```js
{
    "LSP3Profile": {
        "name": "string", // a self chosen username (will likely be replaced by an ENS name)
        "description": "string" // A description, describing the person, company, organisation or creator of the profile.
        "links": [ // links related to the profile
            {
                "title": "string", // a title for the link.
                "url": "string" // the link itself
            },
            ...
        ],
        "tags": [ "string", "string", ... ], // tags related to the profile
        // below each image type SHOULD have different size of the same image, so that interfaces can choose which one to load for better loading performance
        "profileImage": [ // One image in different sizes, representing the profile.
            {  
                "width": Number,
                "height": Number,
                "hashFunction": 'keccak256(bytes)',
                "hash": 'string', // bytes32 hex string of the image hash
                "url": 'string'
            },
            ...
        ],
        "backgroundImage": [ // Image in different sizes, that can be used in conjunction with profile image to give a more personal look to a profile.
            { 
                "width": Number,
                "height": Number,
                "hashFunction": 'keccak256(bytes)',
                "hash": 'string', // bytes32 hex string of the image hash
                "url": 'string'
            },
            ...
        ]
    }
}
```

Example:

```js
{
    LSP3Profile: {
        name: 'frozeman',
        description: 'The inventor of ERC725 and ERC20...',
        links: [
            { title: 'Twitter', url: 'https://twitter.com/feindura' },
            { title: 'lukso.network', url: 'https://lukso.network' }
        ],
        tags: [ 'brand', 'public profile' ],
        profileImage: [
            {
                width: 1024,
                height: 974,
                hashFunction: 'keccak256(bytes)',
                hash: '0xa9399df007997de92a820c6c2ec1cb2d3f5aa5fc1adf294157de563eba39bb6e',
                url: 'ifps://QmW4wM4r9yWeY1gUCtt7c6v3ve7Fzdg8CKvTS96NU9Uiwr'
            },
            {
                width: 640,
                height: 609,
                hashFunction: 'keccak256(bytes)',
                hash: '0xb316a695125cb0566da252266cfc9d5750a740bbdffa86712bb17508e70e6a31',
                url: 'ifps://QmXGELsqGidAHMwYRsEv6Z4emzMggtc5GXZYGFK7r6zFBg'
            }
        ],
        backgroundImage: [
            {
                width: 1800,
                height: 1013,
                hashFunction: 'keccak256(bytes)',
                hash: '0x98fe032f81c43426fbcfb21c780c879667a08e2a65e8ae38027d4d61cdfe6f55',
                url: 'ifps://QmPJESHbVkPtSaHntNVY5F6JDLW8v69M2d6khXEYGUMn7N'
            },
            {
                width: 1024,
                height: 576,
                hashFunction: 'keccak256(bytes)',
                hash: '0xfce1c7436a77a009a97e48e4e10c92e89fd95fe1556fc5c62ecef57cea51aa37',
                url: 'ifps://QmZc9uMJxyUeUpuowJ7AD6MKoNTaWdVNcBj72iisRyM9Su'
            }
        ]
    }
}
```

#### LSP3IssuedAssets[]

References issued smart contract assets, like tokens and NFTs.

```json
{
    "name": "LSP3IssuedAssets[]",
    "key": "0x3a47ab5bd3a594c3a8995f8fa58d0876c96819ca4516bd76100c92462f2f9dc0",
    "keyType": "Array",
    "valueContent": "Number",
    "valueType": "uint256",
    "elementValueContent": "Address",
    "elementValueType": "address"
}
```

For construction of the Asset Keys see: [ERC725Y JSON Schema](https://github.com/lukso-network/LIPs/blob/master/LSPs/LSP-2-ERC725YJSONSchema.md#array)

## Rationale

Universal Profiles are important to create verifiable public accounts that are the source of asset issuance,
or a verifiable public appearance. 

## Implementation

A implementation can be found in the [lukso-network/universalprofile-smart-contracts](https://github.com/lukso-network/universalprofile-smart-contracts/blob/main/contracts/LSP3Account.sol);
The below defines the JSON interface of the `LSP3Account`.

ERC725Y JSON Schema `LSP3Account`:

```json
[
    {
        "name": "SupportedStandards:ERC725Account",
        "key": "0xeafec4d89fa9619884b6b89135626455000000000000000000000000afdeb5d6",
        "keyType": "Mapping",
        "valueContent": "0xafdeb5d6",
        "valueType": "bytes"
    },
    {
        "name": "LSP3Profile",
        "key": "0x5ef83ad9559033e6e941db7d7c495acdce616347d28e90c7ce47cbfcfcad3bc5",
        "keyType": "Singleton",
        "valueContent": "JSONURL",
        "valueType": "bytes"
    },
    {
        "name": "LSP1UniversalReceiverDelegate",
        "key": "0x0cfc51aec37c55a4d0b1a65c6255c4bf2fbdf6277f3cc0730c45b828b6db8b47",
        "keyType": "Singleton",
        "valueContent": "Address",
        "valueType": "address"
    },
    {
        "name": "LSP3IssuedAssets[]",
        "key": "0x3a47ab5bd3a594c3a8995f8fa58d0876c96819ca4516bd76100c92462f2f9dc0",
        "keyType": "Array",
        "valueContent": "Number",
        "valueType": "uint256",
        "elementValueContent": "Address",
        "elementValueType": "address"
    }
]
```

## Interface Cheat Sheet

```solidity

interface ILSP3  /* is ERC165 */ {
         
    
    // ERC173
    
    event OwnershipTransferred(address indexed previousOwner, address indexed newOwner);


    function owner() external view virtual returns (address);
    
    function transferOwnership(address newOwner) external virtual onlyOwner;
    
    
    // ERC725Account (ERC725X + ERC725Y)
      
    event Executed(uint256 indexed _operation, address indexed _to, uint256 indexed  _value, bytes _data);
    
    event ValueReceived(address indexed sender, uint256 indexed value);
    
    event ContractCreated(address indexed contractAddress);
    
    event DataChanged(bytes32 indexed key, bytes value);
    
    
    function execute(uint256 operationType, address to, uint256 value, bytes calldata data) external payable onlyOwner;
    
    function getData(bytes32[] calldata key) external view returns (bytes[] memory value);
    // LSP3 retrievable keys:
    // SupportedStandards:ERC725Account: 0xeafec4d89fa9619884b6b89135626455000000000000000000000000afdeb5d6
    // LSP3Profile: 0x5ef83ad9559033e6e941db7d7c495acdce616347d28e90c7ce47cbfcfcad3bc5
    // LSP3IssuedAssets[]: 0x3a47ab5bd3a594c3a8995f8fa58d0876c96819ca4516bd76100c92462f2f9dc0
    // LSP1UniversalReceiverDelegate: 0x0cfc51aec37c55a4d0b1a65c6255c4bf2fbdf6277f3cc0730c45b828b6db8b47
    
    function setData(bytes32[] calldata key, bytes[] calldata value) external onlyOwner;
    
    
    // ERC1271
    
    function isValidSignature(bytes32 _hash, bytes memory _signature) external view returns (bytes4 magicValue);
    
    
    // LSP1

    event UniversalReceiver(address indexed from, bytes32 indexed typeId, bytes32 indexed returnedValue, bytes receivedData);

    function universalReceiver(bytes32 typeId, bytes calldata data) external returns (bytes32);
    // IF `LSP1UniversalReceiverDelegate` key is set
    // THEN calls will be forwarded to the address given (UniversalReceiver even MUST still be fired)
}


```

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
