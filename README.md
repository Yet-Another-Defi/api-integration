# API Integration Flow
 + [DEX Aggregator API](#dex_api)
 + [Get Started](#get_started)
 + [Flow Example](#flow)
 + [Endpoint Description](#endpoint)
     + [GET v1/platforms](#v1/platforms)
     + [GET v1/{ChainID}/tokens](#v1/tokens)
     + [GET /v1/{chainID}/gasprices](#v1/gasprices)
     + [GET /v1/{chainID}/price](#v1/price)
     + [GET v1/{chainID}/transaction/allowance](#v1/allowance)
     + [GET v1/{chainID}/transaction/approve](#v1/approve)
     + [GET /v1/{chainID}/quote](#v1/quote)

## <a name="dex_api"></a> DEX Aggregator API
- 6 supported blockchains, 3600 tokens 
- Full cycle of preparing a transaction for tokens exchange in the network 

## <a name="get_started"></a>Get Started
1. URL for API – https://api.yetanotherdefi.com
3. Without an API access key, a number of requests per second is limited, suitable for testing and development 
4. To obtain the key, contact [partners@yetanotherdefi.com](mailto:partners@yetanotherdefi.com "partners@yetanotherdefi.com")
5. The given key must be added to the **header** of each request, parameter {`"X-API-Key"`: `"<key>"`} 

## <a name="flow"></a>Flow Exmple
API Use Case
1. API - provides a list of supported blockchains upon request `GET v1/platforms`.
2. GUI - the user selects a network the exchange is made within. For example, ethereum, ChainID=1.
3. API - provides a list of blockchain tokens upon request `GET v1/{ChainID}/tokens` (in our example, `GET v1/1/tokens`).
4. API (optional) – provides calculated Gas Price values ​​in Gwei (nAVAX for Avalanche) for fast, medium, low transaction time `GET /v1/{chainID}/gasprices` (in the example `GET /v1/1/gasprices`).
5. GUI - the user selects the exchange tokens and the sale amount. For example, 1000 USDT to WBTC. 
6. GUI - the user sets the slippage tolerance value in percentage. The recommended value is 1%. 
7. GUI (optional) - the user selects the Gas Price value from pt 4 
8. API - by endpoint `GET /v1/{chainID}/price` (`GET /v1/1/price`) provides the number of the tokens for purchase (0.05 WBTC). 
9. GUI - the user connects the wallet.
10. API - by endpoint `GET v1/{chainID}/transaction/allowance` (GET `/v1/1/transaction/allowance`) returns to the amount of tokens that the exchange smart contract has access to (not required for native coins).
11. GUI - if the value of the sale is greater than the value from pt 10, the user is prompted to provide access for the tokens exchange (otherwise the exchange transaction will not be processed).
12. API - by endpoint `GET v1/{chainID}/transaction/approve` returns the input parameters (calldata) for a transaction to provide access to tokens, and the address of the contract where the transaction should be sent to. 
13. GUI - generates an unsigned transaction based on the data from pt 12 and sends it to the user's connected wallet. 
14. WALLET - the user confirms the operation in the wallet, and the wallet then signs the transaction and sends it to the blockchain.
15. GUI - after successful confirmation of the transaction from pt 14, the user is offered a button to exchange the selected tokens.
16. API - by endpoint `GET /v1/{chainID}/quote` (`GET /v1/1/quote`) provides the number of the tokens purchased (0.05 WBTC), transaction input parameters.
17. GUI - generates an unsigned transaction based on the data from pt 16 and sends it to the user's connected wallet. 
18. WALLET - the user confirms the operation in the wallet, and the wallet then signs the transaction and sends it to the blockchain. 

## <a name="endpoint"></a>Endpoint Description

### <a name="v1/platforms"></a>GET v1/platforms
Provides a list of supported blockchains.
#### Input Parameters
None 
#### Response Options
- **platforms** - an array of supported networks.
    - **chainId** - the blockchain network ID.
    - **shortname** - the short name of the blockchain network.
    - **name** - the full name of the blockchain network.
#### Response Example
```javascript
{
  "platforms": [
    {
      "chainId": 1,
      "name": "Ethereum",
      "shortname": ""
    },
    {
      "chainId": 10,
      "name": "Optimistic Ethereum",
      "shortname": "Optimism"
    },
    {
      "chainId": 56,
      "name": "BNB Smart Chain",
      "shortname": "BSC"
    }
  ]
}
```

### <a name="v1/tokens"></a>GET v1/{ChainID}/tokens
Provides a list of tokens for exchange on blockchains.
#### Input Parameters 
- **chainId** `required` – the blockchain network ID that requires a list of tokens (supported networks - v1/platforms).
#### Response Options 
* **tokens** - an array of supported tokens.
    * **chainId** - the blockchain network ID. 
    * **name** - the full name of the token.
    * **address** – the token smart contract address.
    * **decimals** – the number of decimals used to get its user representation. For example, if decimals equals 2, a balance of 505 tokens should be displayed to a user as 5,05 (505 / 10 ** 2).
    * **logoURI** - the link to the token logo.
    * **symbol** – the abbreviated name of the token.
#### Request Example 
URL + `/v1/1/tokens`
#### Response Example 
```javascript
{
  "tokens": [
    {
      "chainId": 1,
      "address": "0x1b40183efb4dd766f11bda7a7c3ad8982e998421",
      "name": "VesperToken",
      "symbol": "VSP",
      "decimals": 18,
      "logoURI": "https://cl.yetanotherdefi.com/1i/1/0x1b40183efb4dd766f11bda7a7c3ad8982e998421/48x48.png",
      "is_active": true
    },
    {
      "chainId": 1,
      "address": "0xac51066d7bec65dc4589368da368b212745d63e8",
      "name": "ALICE",
      "symbol": "ALICE",
      "decimals": 6,
      "logoURI": "https://cl.yetanotherdefi.com/1i/1/0xac51066d7bec65dc4589368da368b212745d63e8/48x48.png",
      "is_active": true
    }
  ]
}
```

### <a name="v1/gasprices"></a>GET /v1/{chainID}/gasprices
Provides calculated Gas Price values in GweiI (nAVAX for Avalanche). 
#### Input Parameters 
- **chainId** `required` – the blockchain ID for which the gas price is requested.
#### Response Options 
* **high** - the gas price at which the transaction is most likely to be accepted in the next block.
* **medium** - the gas price at which the transaction is most likely to be accepted in the next 2-3 blocks.
* **low** - the gas price at which the transaction is most likely to be accepted not earlier than after block 5. There is a risk of a long transaction confirmation.
#### Request Example 
URL + `/v1/1/gasprices`
#### Response Example 
```javascript
{
  "low": "29.521",
  "medium": "31.521",
  "high": "33.521"
}
```


### <a name="v1/price"></a>GET /v1/{chainID}/price
Returns best exchange offer, no calldata for transaction.
#### Input Parameters 
* **chainID** `required` – the blockchain ID in which tokens are being changed.
* **fromTokenAddress** `required` – smart contract address of the sale token.
* **toTokenAddress** `required` – smart contract address of the purchase token. 
* **amount** `required` – the amount of sale tokens in decimals of the token (can be taken from the method `/tokens`).
* **slippage** `required` – the amount of slippage allowed during the actual execution of the transaction (10 = 1% slippage). If the price changes by more than this percentage, the transaction will revert.
* **gasPrice** – value of the gas price for the transaction in WEI (1 gwei = 1000000000 wei), default value is the value high from the method `/gasprices`.
* **feeRecipient** - wallet address for receiving fees. The commission is paid from the purchase token. 
* **buyTokenPercentageFee** - percentage of commission from the amount of purchase tokens, charged in favor of feeRecipient (10 = 1%, maximum value - 500). 
#### Response Options 
* **amount_out_total** - the amount of purchase tokens in decimals of the token.
* **estimate_gas_total** - the estimated amount of gas that will be consumed when executing the transaction.
* **gas_price** - gas price value for a transaction in WEI (nAVAX for Avalanche).
* **fee_recipient_amount** - the amount of purchase tokens in decimals of the token, which will be deducted in favor of feeRecipient. The value will be 0 if the feeRecipient and buyTokenPercentageFee fields are not specified.
* **token_in** – smart contract address of the sale token.
* **token_out** - smart contract address of the purchase token.
* **routes** - an array of DEXs the transaction will be carried out through.
#### Request Example 
URL + `/v1/1/price?fromTokenAddress=0xdac17f958d2ee523a2206206994597c13d831ec7&toTokenAddress=0x6b175474e89094c44da98b954eedeac495271d0f&amount=1500000000&slippage=1&gasPrice=16000000000&feeRecipient=0xdac17f958d2ee523a2206206994597c13d831ec7&buyTokenPercentageFee=1`
#### Response Example 
```javascript
{
  "amount_out_total": "1498829767947395025220",
  "estimate_gas_total": "265000",
  "token_in": "0xdac17f958d2ee523a2206206994597c13d831ec7",
  "token_out": "0x6b175474e89094c44da98b954eedeac495271d0f",
  "gas_price": "16000000000",
  "fee_recipient_amount": "1497329440850006780",
  "routes": [
    {
      "protocol_name": "DODO_V2",
      "amount_in": "1500327097388245032000",
      "amount_out": "1500000000",
      "percent": 100,
      "pools": null
    }
  ]
}
```


### <a name="v1/allowance"></a>GET v1/{chainID}/transaction/allowance
Checks how many user’s tokens the exchange smart contract has access to.
#### Input Parameters 
* **chainID** `required` – the ID of the blockchain the token is located on (supported networks - `/v1/platforms`).
* **tokenAddress** `required` – smart contract address of the token for which access is being checked.
* **walletAddress** `required` – wallet of the user for which access is being checked.
#### Response Options 
* **remaining** - the number of tokens in decimals of the token the smart contract has access to.
#### Request Example 
URL + `/v1/1/transaction/allowance?tokenAddress=0xdAC17F958D2ee523a2206206994597C13D831ec7&walletAddress=0x58f58219e2d2598588c1b457bb6da65c34d99310`
#### Response Example 
```javascript
{
  "remaining": "11579208923731620000"
}
```


### <a name="v1/approve"></a>GET v1/{chainID}/transaction/approve
Generates transaction input parameters to provide access to the user's tokens for the exchange smart contract. 
#### Input Parameters 
* **chainID** `required` – ID of the blockchain the token is located on (supported networks - `/v1/platforms`).
* **tokenAddress** `required` – address of the smart contract of the token for which the access request is generated.
* **amount** - the amount of user tokens to which access is granted. By default - infinite number.
* **gasPrice** – value of gas price for making a transaction in WEI (nAVAX for Avalanche) (1 gwei = 1000000000 wei), default value – the value high from the method `/gasprices`.
#### Response Options 
* **calldata** - one of the input parameters for processing a transaction providing access to tokens.
* **estimate_gas** - the estimated amount of gas that will be used during the transaction.
* **gas_price** - gas price value for a transaction in WEI.
* **to** – address of the smart contract the transaction should be sent to.
#### Request Example 
URL + `/v1/1/transaction/approve?tokenAddress=0xdAC17F958D2ee523a2206206994597C13D831ec7&amount=100000000000&gasPrice=100000000000`
#### Response Example 
```javascript
{
  "calldata": "0x095ea7b30000000000000000000000001aaad07998466cd3eb8140827dddb37570be1e63000000000000000000000000000000000000000000000000000000174876e800",
  "gas_price": "100000000000",
  "to": "0xdac17f958d2ee523a2206206994597c13d831ec7",
  "estimate_gas": "48561"
}
```


### <a name="v1/quote"></a>GET /v1/{chainID}/quote
Returns the best exchange offer and input parameters for the transaction 
#### Input Parameters 
* **chainID** `required` – ID of the blockchain tokens must be exchanged on (supported networks - `v1/platforms`).
* **fromTokenAddress** `required` – smart contract address of the sale token.
* **toTokenAddress** `required` – smart contract address of the purchase token.
* **amount** `required` – the amount of sale tokens in decimals of the token (can be taken from the method `/tokens`) 
* **slippage** `required` – the amount of slippage allowed during the actual execution of the transaction (10 = 1% slippage). If the price changes by more than this percentage, the transaction will revert.
* **gasPrice** – gas price value for making a transaction in WEI (nAVAX for Avalanche) (1 gwei = 1000000000 wei), default value is the value high from `/gasprices`.
* **feeRecipient** - wallet address for receiving fees. The commission is paid from the purchase token. 
* **buyTokenPercentageFee** - percentage of commission from the amount of purchase tokens, is taken in favor of feeRecipient. (10 = 1%, maximum value is 500).
#### Response Options 
* **amount_out_total** - the amount of purchase tokens in decimals of the token.
* **estimate_gas_total** - the estimated amount of gas that will be used during the transaction.
* **gas_price** - gas price value for a transaction in WEI (nAVAX for Avalanche).
* **fee_recipient_amount** - the amount of purchase tokens in decimals of the token, which will be taken in favor of feeRecipient. The value will be 0 if feeRecipient and buyTokenPercentageFee fields are not specified.
* **token_in** – smart contract address of the sale token.
* **token_out** - smart contract address of the purchase token.
* **routes** - an array of DEXs the transaction will be carried out through.
* **calldata** - one of the input parameters for processing a transaction for tokens exchange.
* **to** – smart contract address where input parameters should be sent to.
#### Request Example 
URL + `/v1/1/quote?fromTokenAddress=0xdac17f958d2ee523a2206206994597c13d831ec7&toTokenAddress=0x6b175474e89094c44da98b954eedeac495271d0f&amount=1500000000&slippage=1&gasPrice=16000000000&feeRecipient=0xdac17f958d2ee523a2206206994597c13d831ec7&buyTokenPercentageFee=1`
#### Response Example 
```javascript
{
  "amount_out_total": "1498829767947395025220",
  "estimate_gas_total": "265000",
  "token_in": "0xdac17f958d2ee523a2206206994597c13d831ec7",
  "token_out": "0x6b175474e89094c44da98b954eedeac495271d0f",
  "gas_price": "16000000000",
  "fee_recipient_amount": "1497329440850006780",
  "routes": [
    {
      "protocol_name": "DODO_V2",
      "amount_in": "1500327097388245032000",
      "amount_out": "1500000000",
      "percent": 100,
      "pools": null
    }
  ],
  "calldata": "0x415565b0000000000000000000000000dac17f958d2ee523a2206206994597c13d.....00000d8b5e9d2b6359a3071e41296359A307",
  "to": "0x1AAAd07998466cD3Eb8140827DDdb37570BE1e63"
}
```
