# 🧠 Solana Developer FAQ (English Version)

This FAQ document serves as a curated guide to help Solana developers navigate common challenges in development, deployment, and debugging. Topics include transaction tracing, IDL generation, program upgrades, and tooling recommendations. Contributions via pull requests are welcome!

---

## 1. How can I trace transactions in Solana?

You can inspect transaction logs using:

* [`simulateTransaction`](https://solana.com/docs/rpc/http/simulatetransaction)
* [`getTransaction`](https://solana.com/docs/rpc/http/gettransaction)

For Ethereum-like tracing functionality, consider using third-party NaaS solutions such as:

* [Helius Enhanced Transactions API](https://docs.helius.dev/solana-apis/enhanced-transactions-api/parse-transaction-s)

---

## 2. Can all programs generate Anchor-compatible IDLs?

No. Only programs written with the Anchor framework can auto-generate Anchor IDLs. Programs written without Anchor require manual construction of the IDL.

---

## 3. How can I create an IDL for non-Anchor programs?

* Manually define the interface in a byte-format IDL.
* Use tools or AI assistance to generate the structure.
* Refer to development practices from projects like Steel.

---

## 4. Are there open-source wallet projects to reference?

Yes, here are some examples:

* [Solibra Wallet](https://github.com/solibra-wallet/solibra-wallet)
* Backpack Wallet (legacy versions; developed by Coral-xyz)

---

## 5. What is Coral-xyz?

Coral-xyz is the team behind Anchor, Backpack, and xNFT, responsible for some of the core tooling in the Solana ecosystem.

---

## 6. What tools can parse on-chain IDLs?

* **Codama** — supports most Anchor-based programs
* [solana-anchor-go](https://github.com/daog1/solana-anchor-go) — Go-based IDL parser

---

## 7. Is there a standard library like OpenZeppelin for Solana?

You can refer to the following Anchor contract templates:

* [anchor-escrow-2024](https://github.com/deanmlittle/anchor-escrow-2024)
* [anchor-vault-2024](https://github.com/deanmlittle/anchor-vault-2024)
* [anchor-amm-2023](https://github.com/deanmlittle/anchor-amm-2023)

---

## 8. Are there example CPI calls to Jupiter?

Yes:

* [sol-swap-cpi](https://github.com/jup-ag/sol-swap-cpi)
* [Jupiter CPI API Docs](https://dev.jup.ag/docs/old/apis/cpi)

---

## 9. Recommended Solana Go SDK?

Use [gagliardetto/solana-go](https://github.com/gagliardetto/solana-go).

---

## 10. What if a contract upgrade fails? How to migrate data?

* Most failures are due to insufficient account space — ensure adequate allocation.
* You cannot reuse the same program ID for new deployments.
* Use migration scripts to transfer data from the original `programId`.

---

## 11. Troubleshooting macOS Solana development setup?

* Common issue: version incompatibility
* Use VPN and the [official installation script](https://solana.com/docs/intro/installation)
* Anchor version must match the Solana CLI version

---

## 12. Is there an IDL for pump.fun?

Yes. See the [community-contributed IDL](https://github.com/Tee-py/solana-txn-parser/blob/main/idl/pump-idl.ts).

---

## 13. Is reentrancy an issue in Solana? How to prevent it?

No. Solana’s account-based model and rent mechanism naturally prevent reentrancy.

---

## 14. How to get SOL on devnet?

* CLI: `solana airdrop 1`
* Web: [Solana Faucet](https://faucet.solana.com/)

---

## 15. Can smart contract deployment use hardware signers?

Yes. Build an unsigned transaction, extract the message, send it to the signer, then broadcast the signed transaction.

---

## 16. Codama doesn't support `alias` types in IDLs?

A community PR is available: [codama#555](https://github.com/codama-idl/codama/pull/555)

---

## 17. Why is the base fee nearly zero after the 17th?

Following a mainnet update, fee strategies were adjusted to improve inclusion rates. The previous model was less friendly for regular users.

---

## 18. Fix for `proc_macro::SourceFile` error during `anchor build`?

Due to changes in `proc-macro2 v1.0.95`, try one of the following:

**Option 1:**

```bash
cargo install --git https://github.com/coral-xyz/anchor --tag v0.31.1 anchor-cli --force
```

Add to `Cargo.toml`:

```toml
[dependencies]
anchor-lang = "0.31.1"
anchor-spl = "0.31.1"
```

In `Anchor.toml`:

```toml
[toolchain]
anchor_version = "0.31.1"
```

Then:

```bash
cargo clean
anchor clean
anchor build
```

**Option 2:**

```bash
cargo update proc-macro2 --precise 1.0.94
```

**Option 3:**

```bash
rustup update
cargo update
anchor build
```

---

## 19. Does contract upgrading create a new account?

No. Upgrades write to a temporary buffer and overwrite the original program data. The `programId` and authority remain unchanged.

---

## 20. Are there mainnet simulation environments?

* [Surfpool](https://docs.surfpool.run/surfpool): fork mainnet for testing
* Exact slot snapshots are not yet supported; minor state discrepancies may exist

---

## 21. Why keep SOL in an ATA (Associated Token Account)?

* Rent (minimum balance) is paid at creation
* Below rent-exempt threshold, token transfers may fail
* Closing the ATA reclaims the rent

---

## 22. Can PDAs have data labels?

* Only IDLs from official or major projects parsed by Solscan are labeled
* Custom PDAs currently cannot set custom labels

---

## 23. Can Solana contracts be browsed like EVM source code?

* No, source code is not auto-displayed by explorers
* IDLs may be viewable if parsed by platforms like Solscan

---

## 24. How to upload your own IDL?

Use the Anchor CLI:

```bash
anchor idl init <PROGRAM_ID> ./target/idl/your_program.json
```

---

## 25. What contract frameworks are available for Solana?

Refer to these open-source frameworks:

* [anchor](https://github.com/solana-foundation/anchor)
* [pinocchio](https://github.com/anza-xyz/pinocchio)
* [steel](https://github.com/regolith-labs/steel)
* [typhoon](https://github.com/exotic-markets-labs/typhoon)

---

## 26. IDL Standards

Two IDL standards are currently in use:

* [anchor](https://github.com/solana-foundation/anchor)
* [shank](https://github.com/metaplex-foundation/shank) — partially supported by frameworks like pinocchio and steel

---
