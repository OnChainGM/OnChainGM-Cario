# 🌞 OnChainGM (Starknet - Cairo 1.0)

**OnChainGM** is a minimal and permissionless smart contract on Starknet that allows users to send an on-chain “GM” (Good Morning) message once every 24 hours using ERC-20 tokens (instead of ETH), with fees transferred to a designated recipient.

---

## 🚀 Features

- ✅ **Once per day** GM enforcement (per address, 24h rule)
- 🪙 **ERC-20 based fee** (requires `transfer_from` and prior approval)
- 🏷️ Emits a clean `OnChainGMEvent` for UI tracking
- 🔐 Immutable fee config set at deployment

---

## 🧾 Contract Info

| Parameter         | Value                                  |
|------------------|----------------------------------------|
| Fee Token         | `0x0471...938d` *(custom ERC-20)*      |
| GM Fee            | `0.5 token` *(500000000000000000 u256)* |
| Fee Recipient     | `0x077e...9a66`                         |
| Cooldown Period   | 86400 seconds (24 hours)               |

---

## ⚙️ Interface

The contract implements the following interface:

```rust
#[starknet::interface]
trait IOnchainGM<TContractState> {
    fn onChainGM(ref self: TContractState, value: u256);
    fn timeUntilNextGM(self: @TContractState, user: ContractAddress) -> u256;
    fn get_fee_recipient(self: @TContractState) -> ContractAddress;
    fn get_gm_fee(self: @TContractState) -> u256;
    fn get_last_GM_timestamp(self: @TContractState, address: ContractAddress) -> u256;
}
```

---

## 🔐 How It Works

1. **User must approve** the contract to spend the GM fee amount on their behalf.
2. User calls `onChainGM(value)` where `value == GM_FEE`.
3. The contract checks:
   - Has 24 hours passed since last GM?
   - Is the `value` correct?
4. Transfers `value` tokens from the caller to the `fee_recipient`.
5. Records the timestamp and emits `OnChainGMEvent`.

---

## 🪙 Fee Logic

- Token-based fee (`ERC20::transfer_from`)
- Caller must **approve** the contract beforehand
- If transfer fails, the call reverts with `"Fee transfer failed"`

---

## 📦 Events

```rust
#[event]
enum Event {
    OnChainGMEvent: OnChainGMEvent,
}

struct OnChainGMEvent {
    sender: ContractAddress,
}
```

Used to track daily GM activity for frontends or indexers.

---

## 🧪 Example Usage (Starknet CLI / SDK)

1. **Approve the GM contract:**

```bash
starknet invoke \
  --address <erc20_address> \
  --abi erc20_abi.json \
  --function approve \
  --inputs <onchaingm_contract_address> 500000000000000000
```

2. **Send your GM:**

```bash
starknet invoke \
  --address <onchaingm_contract_address> \
  --abi onchaingm_abi.json \
  --function onChainGM \
  --inputs 500000000000000000
```

---

## 🧭 Deployment & Constants

Deployed version (for reference):

```rust
// Fee token
FEE_TOKEN = 0x04718f5a0fc34cc1af16a1cdee98ffb20c31f5cd61d6ab07201858f4287c938d

// Fee recipient
FEE_RECIPIENT = 0x077e6508cdd61b683fd3004bb2c2096229256f5e31e0d28ba0fb1a3748089a66

// GM Fee (0.5 token assuming 18 decimals)
GM_FEE = 500000000000000000
```

---

## 📜 License

MIT License. Free to use, fork, and extend.

---

## 📎 Reference Deployment

👉 *Example contract:*  
[Starkscan](https://starkscan.co/contract/0x03830e4eda8e20783529b5316c0469f6f471b72c89be5369fbd5246c77b21936)


---

## ❤️ Support

Send your GM and show your support to the ecosystem 🌞
