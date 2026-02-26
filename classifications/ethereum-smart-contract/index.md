# EthereumSmartContract | Atom Classification

An "EthereumSmartContract" is a deployed contract identity on an EVM chain. Keep this atom minimal with chain ID and address.

## Data Structure

```json
{
  "chainId": "<chain_id>",
  "address": "<ethereum_address>"
}
```

### Atom Classification

```json
{
  "type": "EthereumSmartContract",
  "data": {
    "chainId": "<chain_id>",
    "address": "<ethereum_address>"
  },
  "meta": {
    "pluginId": "ethereum-smart-contract",
    "provider": "etherscan",
    "fetchedAt": "<iso_datetime>",
    "sourceUrl": "<etherscan_url_if_available>"
  }
}
```

### Example

```json
{
  "type": "EthereumSmartContract",
  "data": {
    "chainId": "1",
    "address": "0x1234567890abcdef1234567890abcdef123456"
  },
  "meta": {
    "pluginId": "ethereum-smart-contract",
    "provider": "etherscan",
    "fetchedAt": "2026-02-25T10:00:15-08:00",
    "sourceUrl": "https://etherscan.io/address/0x1234567890abcdef1234567890abcdef123456"
  }
}
```
