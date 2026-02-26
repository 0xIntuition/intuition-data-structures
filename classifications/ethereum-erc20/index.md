# EthereumERC20 | Atom Classification

An "EthereumERC20" is a fungible token contract identity on an EVM chain. Keep this atom minimal with chain ID, address, and core token identifiers.

## Data Structure

```json
{
  "address": "<ethereum_address>",
  "chainId": "<chain_id>",
  "name": "<name>",
  "symbol": "<symbol>",
  "decimals": "<decimals>"
}
```

### Atom Classification

```json
{
  "type": "EthereumERC20",
  "data": {
    "chainId": "1",
    "address": "<ethereum_address>",
    "name": "<name>",
    "symbol": "<symbol>",
    "decimals": "<decimals>"
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
  "type": "EthereumERC20",
  "data": {
    "chainId": "1",
    "address": "0x1234567890abcdef1234567890abcdef123456",
    "name": "Token",
    "symbol": "TOK",
    "decimals": "18"
  },
  "meta": {
    "pluginId": "ethereum-smart-contract",
    "provider": "etherscan",
    "fetchedAt": "2026-02-25T10:00:15-08:00",
    "sourceUrl": "https://etherscan.io/address/0x1234567890abcdef1234567890abcdef123456"
  }
}
```
