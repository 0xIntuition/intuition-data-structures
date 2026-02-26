# EthereumAccount | Atom Classification

An "EthereumAccount" is a wallet/account identity on an EVM chain. Keep this atom minimal with only the address.

## Data Structure

```json
{
  "address": "<ethereum_address>"
}
```

### Atom Classification

```json
{
  "type": "EthereumAccount",
  "data": {
    "address": "<ethereum_address>"
  },
  "meta": {
    "pluginId": "ethereum-account",
    "provider": "etherscan",
    "fetchedAt": "<iso_datetime>",
    "sourceUrl": "<etherscan_url_if_available>"
  }
}
```

### Example

```json
{
  "type": "EthereumAccount",
  "data": {
    "address": "0x1234567890abcdef1234567890abcdef123456"
  },
  "meta": {
    "pluginId": "ethereum-account",
    "provider": "etherscan",
    "fetchedAt": "2026-02-25T10:00:15-08:00",
    "sourceUrl": "https://etherscan.io/address/0x1234567890abcdef1234567890abcdef123456"
  }
}
```
