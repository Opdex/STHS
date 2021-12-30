# STHS

## Stratis Transaction Handoff Specification

Decentralized Stratis Transaction Handoff Specification used for dApps to handoff transaction details to compatible wallets.

## Usage and Benefits

Allows dApps to quote users without the users every giving direct access to any part of their wallet, then handoff the quoted payload to compatible wallets to build, review, sign and broadcast.

### Example

User quotes a swap transaction on Opdex, inputting the required information necessary for the dApp to quote the result. The dApp will format the given information into the STHS JSON and create a QR code for compatible wallets to scan. Wallets use the data within the QR code to build the actual transaction that can be reviewed, signed and broadcasted.

---

## Handoff

### dApp Compatibility

dApp compatibility relies on the implementation of the following requirements:

- Gather required fields necessary to complete the raw transaction
- Output a QR code matching the STHS schema
- An available callback endpoint for the broadcasting wallet to notify of the submitted transaction hash

**STHS Schema Example**

```JSON
{
    "sender": "tHYHem7cLKgoLkeb792yn4WayqKzLrjJak",
    "to": "t8XpH1pNYDgCnqk91ZQKLgpUVeJ7XmomLT",
    "amount": "100.00000000",
    "method": "SwapExactSrcForCrs",
    "parameters": [
        {
            "label": "Token In Amount",
            "value": "12#100000000"
        },
        {
            "label": "CRS Out Minimum",
            "value": "7#200000000"
        },
        {
            "label": "Token In",
            "value": "9#tGSk2dVENuqAQ2rNXbui37XHuurFCTqadD"
        },
        {
            "label": "Recipient",
            "value": "9#tHYHem7cLKgoLkeb792yn4WayqKzLrjJak"
        },
        {
            "label": "Deadline",
            "value": "7#201000000"
        },
    ],
    "callback": "https://some.api.com/transactions"
}
```

**STHS Properties** 

Property | Type | Description
--- | --- | ---
Sender | `Address` | The address of the wallet to be used for sending the transaction.
To | `Address` | The address of the smart contract or user that is being called or transferred to.
Amount | `string` | A decimal value as string with the total amount of CRS to send with the transaction. Formatted to include exactly as many decimal places that the token supports. (e.g. 1 CRS must be formatted exactly as `"1.00000000"` with 8 decimal places)
Method | `string` | The name of the smart contract method being called, if transaction is a smart contract transaction.
Callback | `string` | An endpoint that the wallet can callback to, notifying dApps of a sent transaction and its transaction hash.
Parameters | `Parameter[]` | A list of parameters, in the correct order of the smart contract methods signature, if a smart contract is being called. 

**STHS Parameter Properties** 

Property | Type | Description
--- | --- | ---
Label | `string` | A user friendly label wallets can use to describe what the parameter represents.
Value | `string` | The parameters value, formatted for transaction submission following Stratis standards here: https://academy.stratisplatform.com/Architecture%20Reference/SmartContracts/working-with-contracts.html#parameter-serialization

### Wallet Compatibility

Wallet compatibility relies on the implementation of the following requirements:

- Ability to scan or paste QR code data to retrieve transaction data
- User or wallet should validate the entire request
- build and broadcast the transaction, sending the transaction hash result to the callback present in the initial request
- _**POST**_ the JSON-encoded signed message and and public key to the callback URL, using HTTPS
    ```
    POST https://some.api.com/transactions
    Content-Type: application/json
    {
        "transactionHash": "206c00b258a671813264d6781451436c6ac091460bdd9d07dc950c6a419a687c"
    }
    ```