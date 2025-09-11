# 🧠 Solana 技术 FAQ 收录

本仓库整理了在 Solana 开发中常见的疑难杂症、报错排查、合约部署、IDL生成、工具推荐等实用问答。希望能帮助开发者少踩坑、快上手。欢迎 PR 补充～

---

## 1. Solana 中如何做交易的 tracing？

可以通过 `simulateTransaction` 和 `getTransaction` 查看 tx log：

- 官方文档：https://solana.com/docs/rpc/http/simulatetransaction  
- https://solana.com/docs/rpc/http/gettransaction

但如果要像以太坊 `traceTransaction` 那样更直观，需要使用第三方 NaaS 平台，例如：

- [Helius Enhanced Transactions API](https://docs.helius.dev/solana-apis/enhanced-transactions-api/parse-transaction-s)

---

## 2. 所有程序都能生成 Anchor 格式的 IDL 吗？

不能，必须是 Anchor 编写的程序才能自动生成 Anchor IDL。非 Anchor 程序需要手动构造。

---

## 3. 非 Anchor 合约怎么搞 IDL？

- 自己手动构造 bytes 格式
- 或者用 AI/工具辅助生成接口结构
- 有人建议参考 Steel 开发者的做法

---

## 4. 想做钱包项目，有开源参考吗？

- [Solibra Wallet](https://github.com/solibra-wallet/solibra-wallet)
- Backpack Wallet 有旧版本代码快照可参考（Backpack 由 Coral-xyz 开发）

---

## 5. Coral-xyz 是什么团队？

Coral-xyz 是 Backpack 背后的团队，主导了 Anchor、Backpack、xNFT 等核心开发。

---

## 6. 链上 IDL 可以用什么工具解析？

- Codama（支持大部分 Anchor 合约）
- [solana-anchor-go](https://github.com/daog1/solana-anchor-go)（Go语言解析 IDL）
- [anchor-gen](https://github.com/saber-hq/anchor-gen) (Rust)

---

## 7. Solana 上有类似 OpenZeppelin 的标准库吗？

可以参考以下 Anchor 合约模板：

- https://github.com/deanmlittle/anchor-escrow-2024  
- https://github.com/deanmlittle/anchor-vault-2024  
- https://github.com/deanmlittle/anchor-amm-2023

---

## 8. CPI 调用 Jupiter 的参考代码？

- https://github.com/jup-ag/sol-swap-cpi  
- https://dev.jup.ag/docs/old/apis/cpi

---

## 9. Solana 的 Go SDK 推荐用哪个？

推荐：[gagliardetto/solana-go](https://github.com/gagliardetto/solana-go)

---

## 10. 合约升级失败怎么办？原 programId 的数据怎么迁移？

- 一般是空间不足导致升级失败，建议预留充足空间
- 不能重复使用旧 programId 部署新程序，数据账户需重新创建
- 可通过脚本转移原 programId 绑定的数据

---

## 11. macOS 上搭建 Solana 开发环境失败？

- 最常见的问题是版本不兼容
- 推荐使用 VPN + 官方 install 脚本：https://solana.com/docs/intro/installation  
- Anchor 与 Solana CLI 的版本需匹配

---

## 12. pump.fun 有 IDL 吗？

- 有人收录在此 repo：https://github.com/Tee-py/solana-txn-parser/blob/main/idl/pump-idl.ts

---

## 13. Solana 会有重入风险吗？怎么防重入？

不会。由于账户模型 + rent 抵押机制，Solana 合约天然不支持重入逻辑。

---

## 14. devnet 上如何获取 SOL？

- CLI 方式：`solana airdrop 1`
- 或使用 faucet：https://faucet.solana.com/

---

## 15. 合约部署能使用签名机吗？

可以。先构建 unsigned tx，提取 message，发送给签名机签名，再广播 tx 即可。

---

## 16. Codama 不支持 IDL 的 alias 类型？

社区已提交 PR：https://github.com/codama-idl/codama/pull/555

---

## 17. 为什么 17 号后 base fee 接近 0？

主网更新后调整 base fee 策略，原机制对普通用户不友好，现降低费率以提升打包成功率。

---

## 18. anchor build 报错 proc_macro::SourceFile 无法识别？

原因是 `proc-macro2 1.0.95` 改动。

Option 1:
Step 1:
cargo install --git https://github.com/coral-xyz/anchor --tag v0.31.1 anchor-cli --force
Step 2:
add to Cargo.toml
[dependencies]
anchor-lang = "0.31.1"
anchor-spl = "0.31.1"
add to Anchor.toml
[toolchain]
anchor_version = "0.31.1"
Step 3:
cargo clean
anchor clean
anchor build

Option 2:
cargo update proc-macro2 --precise 1.0.94

Option 3:
rustup update
cargo update
anchor build


---

## 19. 升级是创建新账户吗？升级机制解析

不是，升级只是写入临时 buffer 账户，再覆盖原有 data。原 programId 和权限不变。

---

## 20. 主网仿真环境有哪些？

- [Surfpool](https://docs.surfpool.run/surfpool)：可 fork 主网进行测试
- 暂不支持精确某 slot 快照，状态可能略有偏差

---

## 21. ATA Token Account 为什么要留钱？

- 创建时支付的是 rent（租金），这笔钱就是最低余额
- 若账户余额低于 rent-exempt，无法正常转账
- 关闭 ATA 可回收 rent

---

## 22. PDA 有 Data 标签的机制？

- 一般只有官方或主流项目 IDL 被 Solscan 接入才有解析
- 自定义 PDA 暂时无法设置该标签

---

## 23. Solana 合约可以开源展示吗？

- 不支持浏览器自动展示合约源码
- 最多支持展示 IDL（如被 solscan 解析）

---

## 24. 自己合约怎么上传 IDL？

使用 Anchor 命令行工具：

```bash
anchor idl init <PROGRAM_ID> ./target/idl/your_program.json

```
---

## 25. 目前 solana 上开源的合约开发框架有哪些？

具体的使用参考项目链接

- [anchor](https://github.com/solana-foundation/anchor)
- [pinocchio](https://github.com/anza-xyz/pinocchio)
- [steel](https://github.com/regolith-labs/steel)
- [typhoon](https://github.com/exotic-markets-labs/typhoon)

---

## 26. IDL 标准

目前IDL有两种标准
- [anchor](https://github.com/solana-foundation/anchor)
- [shank](https://github.com/metaplex-foundation/shank) 上面列举的几个框架(除anchor)部分支持shank

---
