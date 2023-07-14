---
title: ERC20Vault
---

## ERC20Vault

This vault holds all ERC20 tokens (but not Ether) that users have deposited.
It also manages the mapping between canonical ERC20 tokens and their bridged
tokens.

_Ether is held by Bridges on L1 and by the EtherVault on L2, not
ERC20Vaults._

### CanonicalERC20

```solidity
struct CanonicalERC20 {
  uint256 chainId;
  address addr;
  uint8 decimals;
  string symbol;
  string name;
}
```

### MessageDeposit

```solidity
struct MessageDeposit {
  address token;
  uint256 amount;
}
```

### isBridgedToken

```solidity
mapping(address => bool) isBridgedToken
```

### bridgedToCanonical

```solidity
mapping(address => struct ERC20Vault.CanonicalERC20) bridgedToCanonical
```

### canonicalToBridged

```solidity
mapping(uint256 => mapping(address => address)) canonicalToBridged
```

### messageDeposits

```solidity
mapping(bytes32 => struct ERC20Vault.MessageDeposit) messageDeposits
```

### BridgedERC20Deployed

```solidity
event BridgedERC20Deployed(uint256 srcChainId, address canonicalToken, address bridgedToken, string canonicalTokenSymbol, string canonicalTokenName, uint8 canonicalTokenDecimal)
```

### EtherSent

```solidity
event EtherSent(bytes32 msgHash, address from, address to, uint256 destChainId, uint256 amount)
```

### ERC20Sent

```solidity
event ERC20Sent(bytes32 msgHash, address from, address to, uint256 destChainId, address token, uint256 amount)
```

### ERC20Released

```solidity
event ERC20Released(bytes32 msgHash, address from, address token, uint256 amount)
```

### ERC20Received

```solidity
event ERC20Received(bytes32 msgHash, address from, address to, uint256 srcChainId, address token, uint256 amount)
```

### ERC20_VAULT_INVALID_TO

```solidity
error ERC20_VAULT_INVALID_TO()
```

Thrown when the `to` address in an operation is invalid.
This can happen if it's zero address or the address of the token vault.

### ERC20_VAULT_INVALID_VALUE

```solidity
error ERC20_VAULT_INVALID_VALUE()
```

Thrown when the value in a transaction is invalid.
The value can be an Ether amount or the amount of a token being
transferred.

### ERC20_VAULT_INVALID_TOKEN

```solidity
error ERC20_VAULT_INVALID_TOKEN()
```

Thrown when the token address in a transaction is invalid.
This could happen if the token address is zero or doesn't conform to the
ERC20 standard.

### ERC20_VAULT_INVALID_AMOUNT

```solidity
error ERC20_VAULT_INVALID_AMOUNT()
```

Thrown when the amount in a transaction is invalid.
This could happen if the amount is zero or exceeds the sender's balance.

### ERC20_VAULT_INVALID_OWNER

```solidity
error ERC20_VAULT_INVALID_OWNER()
```

Thrown when the owner address in a message is invalid.
This could happen if the owner address is zero or doesn't match the
expected owner.

### ERC20_VAULT_INVALID_SRC_CHAIN_ID

```solidity
error ERC20_VAULT_INVALID_SRC_CHAIN_ID()
```

Thrown when the source chain ID in a message is invalid.
This could happen if the source chain ID doesn't match the current
chain's ID.

### ERC20_VAULT_MESSAGE_NOT_FAILED

```solidity
error ERC20_VAULT_MESSAGE_NOT_FAILED()
```

Thrown when a message has not failed.
This could happen if trying to release a message deposit without proof of
failure.

### ERC20_VAULT_INVALID_SENDER

```solidity
error ERC20_VAULT_INVALID_SENDER()
```

Thrown when the sender in a message context is invalid.
This could happen if the sender isn't the expected token vault on the
source chain.

### init

```solidity
function init(address addressManager) external
```

### sendERC20

```solidity
function sendERC20(uint256 destChainId, address to, address token, uint256 amount, uint256 gasLimit, uint256 processingFee, address refundAddress, string memo) external payable
```

Transfers ERC20 tokens to this vault and sends a message to the
destination chain so the user can receive the same amount of tokens
by invoking the message call.

#### Parameters

| Name          | Type    | Description                            |
| ------------- | ------- | -------------------------------------- |
| destChainId   | uint256 | Chain ID of the destination chain      |
| to            | address | Address of the receiver                |
| token         | address | The address of the token to be sent.   |
| amount        | uint256 | The amount of token to be transferred. |
| gasLimit      | uint256 | Gas limit for the transaction          |
| processingFee | uint256 | Processing fee for the transaction     |
| refundAddress | address | Address for refunds                    |
| memo          | string  | Any additional data or notes           |

### releaseERC20

```solidity
function releaseERC20(struct IBridge.Message message, bytes proof) external
```

Release deposited ERC20 back to the owner on the source ERC20Vault with
a proof that the message processing on the destination Bridge has failed.

#### Parameters

| Name    | Type                   | Description                                                            |
| ------- | ---------------------- | ---------------------------------------------------------------------- |
| message | struct IBridge.Message | The message that corresponds to the ERC20 deposit on the source chain. |
| proof   | bytes                  | The proof from the destination chain to show the message has failed.   |

### receiveERC20

```solidity
function receiveERC20(struct ERC20Vault.CanonicalERC20 canonicalToken, address from, address to, uint256 amount) external
```

This function can only be called by the bridge contract while
invoking a message call. See sendERC20, which sets the data to invoke
this function.

#### Parameters

| Name           | Type                             | Description                                                                                                          |
| -------------- | -------------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| canonicalToken | struct ERC20Vault.CanonicalERC20 | The canonical ERC20 token which may or may not live on this chain. If not, a BridgedERC20 contract will be deployed. |
| from           | address                          | The source address.                                                                                                  |
| to             | address                          | The destination address.                                                                                             |
| amount         | uint256                          | The amount of tokens to be sent. 0 is a valid value.                                                                 |

---

## title: ProxiedERC20Vault

## ProxiedERC20Vault