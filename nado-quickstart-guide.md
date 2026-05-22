# Nado 快速入门指南

> 目标：从零开始，在 ~5 分钟内完成你的第一笔 Nado 交易。

本指南假设你具备基本的区块链知识，并希望直接动手。如需深入理解，请参阅 [核心概念](https://docs.nado.xyz/developer-resources/get-started/core-concepts)。

---

## ✅ 前置要求

开始之前，请确保你拥有：

- **Ink 钱包（含私钥）**（任何 EVM 兼容钱包均可）
- **Ink Sepolia ETH（用于 Gas）** — 从 [Ink Faucet](https://docs.inkonchain.com/tools/faucets) 获取免费测试网 ETH
- **测试网 USDT0（≥ $5）** — 从 [Nado Testnet Faucet](https://testnet.nado.xyz/portfolio/faucet) 获取免费测试代币

{% hint style="success" %}
**本指南使用测试网（Ink Sepolia）**，在不涉及真实资金的情况下安全学习。

**获取测试代币：**

1. **Gas（Ink Sepolia ETH）**：[Ink Faucet](https://docs.inkonchain.com/tools/faucets) — 免费测试网 ETH，用于交易手续费
2. **交易资金（USDT0、KBTC 等）**：[Nado Testnet Faucet](https://testnet.nado.xyz/portfolio/faucet) — 获取测试代币

准备开始真实交易时，切换到主网（Ink），将所有示例中的 `testnet` 替换为 `mainnet` 即可。
{% endhint %}

---

## 📦 步骤 1：安装 SDK（2 分钟）

选择你熟悉的编程语言，安装 SDK：

{% tabs %}
{% tab title="Python" %}

```bash
# 安装 Nado Python SDK
pip install nado-protocol
```

**验证安装：**

```bash
python -c "import nado_protocol; print('✅ Installed successfully')"
```

{% endtab %}

{% tab title="TypeScript" %}

```bash
# 安装 Nado TypeScript SDK
npm install @nadohq/client viem bignumber.js
# 或
yarn add @nadohq/client viem bignumber.js
```

**验证安装：**

```bash
node -e "require('@nadohq/client'); console.log('✅ Installed successfully')"
```

{% endtab %}

{% tab title="Rust" %}

```bash
# 添加到 Cargo.toml
cargo add nado-sdk@0.3.3 ethers tokio
```

**或手动添加到 Cargo.toml：**

```toml
[dependencies]
nado-sdk = "0.3.3"
ethers = "2.0"
tokio = { version = "1", features = ["full"] }
```

**验证安装：**

```bash
cargo build
```

{% endtab %}
{% endtabs %}

---

## 🌐 步骤 2：连接到 Nado（30 秒）

创建客户端并验证连接：

{% tabs %}
{% tab title="Python" %}

```python
from nado_protocol.client import create_nado_client, NadoClientMode

# 替换为你的私钥
PRIVATE_KEY = "0x..."  # ⚠️ 妥善保管，切勿泄露！

# 创建客户端（测试网 - Ink Sepolia）
client = create_nado_client(NadoClientMode.TESTNET, PRIVATE_KEY)

# 验证连接 — 查询合约
contracts = client.context.engine_client.get_contracts()
print(f"✅ 已连接到 Nado 测试网！")
print(f"Chain ID: {contracts.chain_id}")  # 测试网应为 763373
print(f"你的地址: {client.context.signer.address}")
```

{% endtab %}

{% tab title="TypeScript" %}

```typescript
import { createNadoClient } from '@nadohq/client';
import { createWalletClient, createPublicClient, http } from 'viem';
import { privateKeyToAccount } from 'viem/accounts';
import { inkSepolia } from 'viem/chains';  // Ink Sepolia 测试网

// 替换为你的私钥
const PRIVATE_KEY = '0x...';  // ⚠️ 妥善保管，切勿泄露！

// 创建钱包客户端
const account = privateKeyToAccount(PRIVATE_KEY);
const walletClient = createWalletClient({
  account,
  chain: inkSepolia,  // Ink Sepolia
  transport: http()
});

const publicClient = createPublicClient({
  chain: inkSepolia,
  transport: http()
});

// 创建 Nado 客户端（测试网 - Ink Sepolia）
const client = createNadoClient('inkTestnet', {
  walletClient,
  publicClient
});

// 验证连接
const contracts = await client.context.engineClient.getContracts();
console.log('✅ 已连接到 Nado 测试网！');
console.log('Chain ID:', contracts.chainId);  // 测试网应为 763373
console.log('你的地址:', account.address);
```

{% endtab %}

{% tab title="Rust" %}

```rust
use nado_sdk::prelude::*;
use nado_sdk::utils::private_key::private_key;

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    // 替换为你的私钥，或在 .env 中设置 RUST_SDK_PRIVATE_KEY
    let private_key = private_key();

    // 创建客户端（测试网 - Ink Sepolia）
    let client = NadoClient::new(ClientMode::Test)
        .with_signer(private_key)
        .await?;

    // 验证连接 — 查询合约
    let contracts = client.get_contracts().await?;
    println!("✅ 已连接到 Nado 测试网！");
    println!("Chain ID: {}", contracts.chain_id);  // 测试网应为 763373
    println!("你的地址: {:?}", client.address()?);

    Ok(())
}
```

{% endtab %}
{% endtabs %}

{% hint style="warning" %}
**安全提醒：** 切勿将私钥提交到 Git！请使用环境变量：

- Python: `os.getenv('PRIVATE_KEY')`
- TypeScript: `process.env.PRIVATE_KEY`
- Rust: `std::env::var("PRIVATE_KEY")`
{% endhint %}

---

## 💰 步骤 3：创建子账户（充值资金）（1 分钟）

子账户会在你首次充值时自动创建。需要 **≥ $5 USDT0** 才能创建子账户。

{% hint style="info" %}
**多种充值方式：**

- **UI 水龙头**（测试网最简便 — 如下所示）
- **直接充值**（发送代币到你的专属充值地址）
- **智能合约**（链上调用 depositCollateral）

查看 [充值指南](https://docs.nado.xyz/developer-resources/api/depositing) 了解全部方式和高级选项。
{% endhint %}

### 方式 A：通过 UI 充值（最简便）

**测试网（Ink Sepolia）：**

1. 前往 [Nado Testnet Faucet](https://testnet.nado.xyz/portfolio/faucet)
2. 连接钱包（确保在 Ink Sepolia 网络上）
3. 铸造测试网 USDT0（点击"Mint"按钮 — 免费！）
4. 前往 [Testnet Trading](https://testnet.nado.xyz) 验证余额
5. 准备开始交易！（子账户自动创建）

**主网（Ink）：**

1. 前往 [Nado Mainnet](https://nado.xyz)
2. 连接钱包（确保在 Ink 主网上）
3. 点击"Deposit"并按 UI 提示操作
4. 发送 ≥ $5 USDT0 完成充值
5. 子账户自动创建

### 方式 B：通过 SDK 充值

{% tabs %}
{% tab title="Python" %}

```python
from nado_protocol.utils.bytes32 import subaccount_to_hex
from nado_protocol.utils.math import to_x6
from nado_protocol.contracts.types import DepositCollateralParams

# 步骤 1：批准 USDT0 额度（一次性操作）
product_id = 0  # USDT0
amount = to_x6(10)  # $10 USDT0

# 批准终端合约支配你的代币
client.spot.approve_allowance(product_id, amount)
print("✅ 额度已批准")

# 步骤 2：充值到子账户
deposit_params = DepositCollateralParams(
    subaccount_name="default",
    product_id=product_id,
    amount=amount
)
client.spot.deposit(deposit_params)
print("💰 充值已提交，等待确认...")

# 等待几秒处理
import time
time.sleep(5)

# 步骤 3：验证子账户已创建
from nado_protocol.utils.math import from_x18

subaccount_hex = subaccount_to_hex(client.context.signer.address, "default")
info = client.context.engine_client.get_subaccount_info(subaccount_hex)

if info.exists:
    print("✅ 子账户已创建！")
    # 从第一个现货产品（USDT0）获取余额 — 所有值均为 x18
    if info.spot_balances:
        balance = from_x18(int(info.spot_balances[0].balance.amount))
        print(f"余额: ${balance:.2f} USDT0")
else:
    print("❌ 未找到子账户，请确认充值金额 ≥ $5 USDT0")
```

{% endtab %}

{% tab title="TypeScript" %}

```typescript
import { toFixedPoint } from '@nadohq/shared';

// 步骤 1：批准 USDT0 额度（一次性操作）
const productId = 0; // USDT0
const amount = toFixedPoint(10, 6); // $10 USDT0（6 位小数）

await client.spot.approveAllowance({
  productId,
  amount: amount.toString()
});
console.log('✅ 额度已批准');

// 步骤 2：充值到子账户
const depositTx = await client.spot.deposit({
  subaccountName: 'default',
  productId,
  amount: amount.toString()
});
console.log('💰 充值已提交，等待确认...');

// 等待交易确认
await client.context.publicClient.waitForTransactionReceipt({
  hash: depositTx
});

// 等待几秒处理
await new Promise(resolve => setTimeout(resolve, 5000));

// 步骤 3：验证子账户已创建
const info = await client.subaccount.getSubaccountSummary({
  subaccountOwner: client.context.walletClient.account.address,
  subaccountName: 'default'
});

if (info.exists) {
  console.log('✅ 子账户已创建！');
  console.log('余额:', parseFloat(info.health.totalDeposited) / 1e6, 'USDT0');
} else {
  console.log('❌ 未找到子账户，请确认充值金额 ≥ $5 USDT0');
}
```

{% endtab %}

{% tab title="Rust" %}

```rust
use nado_sdk::math::to_u128_x6;

// 充值 $10 USDT0（product_id = 0）
// SDK 一并处理批准和充值
client
    .deposit_collateral_builder()
    .product_id(0) // USDT0
    .amount(to_u128_x6(10)) // $10 USDT0（缩放至 6 位小数）
    .deposit_and_await_balance() // 等待余额变动
    .await?;

println!("✅ 充值完成，子账户已创建！");
println!("💰 现在可以下单了");
```

{% endtab %}
{% endtabs %}

{% hint style="info" %}
**等待时间：** 在 Ink Sepolia 测试网上，充值需要约 5-10 秒。可通过查询 `subaccount_info` 确认子账户创建状态。
{% endhint %}

---

## 📈 步骤 4：下第一笔订单（1 分钟）

现在我们在 BTC-PERP 上下一个简单的限价单：

{% tabs %}
{% tab title="Python" %}

```python
from nado_protocol.engine_client.types.execute import PlaceOrderParams, OrderParams
from nado_protocol.utils.math import to_x18, from_x18
from nado_protocol.utils.order import build_appendix
from nado_protocol.utils.expiration import OrderType, get_expiration_timestamp
from nado_protocol.utils.bytes32 import subaccount_to_hex
from nado_protocol.utils.nonce import gen_order_nonce

# 获取 BTC-PERP 产品 ID
all_products = client.context.engine_client.get_all_products()
btc_perp = next((p for p in all_products.perp_products if p.product_id == 2), None)
product_id = btc_perp.product_id  # BTC-PERP 的 product_id 为 2

# 获取当前市场价格
market_price = client.context.engine_client.get_market_price(product_id)
current_price = from_x18(int(market_price.bid_x18))  # 从 x18 转换
print(f"当前 BTC 价格: ${current_price:,.2f}")

# 以低于市价 1% 的价格下限价买入单
order_price = current_price * 0.99  # 低于市价 1%（不易立即成交）
order_size = 0.001  # 0.001 BTC

# 创建订单参数
subaccount_hex = subaccount_to_hex(client.context.signer.address, "default")
order = OrderParams(
    sender=subaccount_hex,
    priceX18=to_x18(order_price),
    amount=to_x18(order_size),  # 正数 = 买入，负数 = 卖出
    expiration=get_expiration_timestamp(60),  # 60 秒后过期
    nonce=gen_order_nonce(),
    appendix=build_appendix(OrderType.DEFAULT)
)

# 下单
params = PlaceOrderParams(product_id=product_id, order=order)
result = client.market.place_order(params)

print("✅ 订单提交成功！")
print(f"订单摘要: {result.data.digest}")
print(f"价格: ${order_price:,.2f}")
print(f"数量: {order_size} BTC（买入）")
```

{% endtab %}

{% tab title="TypeScript" %}

```typescript
import { toFixedPoint, packOrderAppendix, nowInSeconds } from '@nadohq/shared';

// 获取 BTC-PERP 产品 ID
const allProducts = await client.market.getAllMarkets();
const btcPerp = allProducts.find(p => p.symbol === 'BTC-PERP');
const productId = btcPerp.productId;

// 获取当前市场价格
const marketPrices = await client.market.getLatestMarketPrice([productId]);
const currentPrice = parseFloat(marketPrices[0].markPrice);
console.log(`当前 BTC 价格: $${currentPrice.toLocaleString()}`);

// 以低于市价 1% 的价格下限价买入单
const orderPrice = currentPrice * 0.99;  // 低于市价 1%
const orderSize = 0.001;  // 0.001 BTC

// 使用 SDK 下单（处理所有复杂逻辑！）
const result = await client.market.placeOrder({
  order: {
    subaccountName: 'default',
    price: orderPrice,
    amount: toFixedPoint(orderSize, 18),  // 正数 = 买入，负数 = 卖出
    expiration: nowInSeconds() + 60,  // 60 秒后过期
    appendix: packOrderAppendix({ orderExecutionType: 'default' })
  },
  productId
});

console.log('✅ 订单提交成功！');
console.log('订单摘要:', result.data.digest);
console.log(`价格: $${orderPrice.toLocaleString()}`);
console.log(`数量: ${orderSize} BTC（买入）`);
```

{% endtab %}

{% tab title="Rust" %}

```rust
use nado_sdk::math::{f64_to_x18, x18_to_f64};

// 获取 BTC-PERP 产品 ID
const BTC_PERP: u32 = 2;

// 获取当前市场价格
let market_price = client.get_market_price(BTC_PERP).await?;
let current_price = x18_to_f64(market_price.bid_x18);
println!("当前 BTC 价格: ${:.2}", current_price);

// 以低于市价 1% 的价格下限价买入单
let order_price = current_price * 0.99;  // 低于市价 1%
let order_size = 0.001;  // 0.001 BTC

// 使用 SDK 构建器下单（处理所有复杂逻辑！）
let result = client
    .place_order_builder()
    .product_id(BTC_PERP)
    .amount(f64_to_x18(order_size))  // 正数 = 买入，负数 = 卖出
    .price_x18(f64_to_x18(order_price))
    .execute()
    .await?;

println!("✅ 订单提交成功！");
println!("订单摘要: {:?}", result.unwrap().digest);
println!("价格: ${:.2}", order_price);
println!("数量: {} BTC（买入）", order_size);
```

{% endtab %}
{% endtabs %}

{% hint style="success" %}
**恭喜！** 🎉 你刚在 Nado 下了第一笔订单！这是一个低于市价 1% 的限价买入单，所以不会立即成交（会挂在订单簿上）。
{% endhint %}

---

## ✅ 步骤 5：验证你的订单

检查你的订单是否已被接受并在订单簿上：

{% tabs %}
{% tab title="Python" %}

```python
from nado_protocol.utils.bytes32 import subaccount_to_hex
from nado_protocol.utils.math import from_x18

# 查询你的活跃订单
subaccount_hex = subaccount_to_hex(client.context.signer.address, "default")
open_orders_data = client.context.engine_client.get_subaccount_open_orders(product_id, subaccount_hex)

if len(open_orders_data.orders) == 0:
    print("⚠️  未找到活跃订单")
    print("你的订单可能已立即成交！请检查持仓或订单历史。")
else:
    print(f"\n📋 活跃订单数: {len(open_orders_data.orders)}")
    for order in open_orders_data.orders:
        # 所有值均为 x18
        amount = from_x18(int(order.amount))
        side = '买入' if amount > 0 else '卖出'  # 正数 = 买入，负数 = 卖出

        print(f"  - 订单 ID: {order.digest}")
        print(f"    价格: ${from_x18(int(order.price_x18)):,.2f}")
        print(f"    数量: {abs(amount):.4f} BTC")
        print(f"    方向: {side}")
```

{% endtab %}

{% tab title="TypeScript" %}

```typescript
// 查询你的活跃订单
const openOrders = await client.market.getOpenSubaccountOrders({
  subaccountOwner: client.context.walletClient.account.address,
  subaccountName: 'default',
  productId
});

if (openOrders.length === 0) {
  console.log('⚠️  未找到活跃订单');
  console.log('你的订单可能已立即成交！请检查持仓或订单历史。');
} else {
  console.log(`\n📋 活跃订单数: ${openOrders.length}`);
  for (const order of openOrders) {
    const amount = Number(order.amount) / 1e18;
    const side = amount > 0 ? '买入' : '卖出';  // 正数 = 买入，负数 = 卖出

    console.log(`  - 订单 ID: ${order.digest}`);
    console.log(`    价格: $${(Number(order.price) / 1e18).toLocaleString()}`);
    console.log(`    数量: ${Math.abs(amount).toFixed(4)} BTC`);
    console.log(`    方向: ${side}`);
  }
}
```

{% endtab %}

{% tab title="Rust" %}

```rust
// 查询该产品的子账户订单
let response = client
    .get_subaccount_orders(client.subaccount()?, product_id)
    .await?;

if response.orders.is_empty() {
    println!("⚠️  未找到活跃订单");
    println!("你的订单可能已立即成交！请检查持仓或订单历史。");
} else {
    println!("\n📋 活跃订单数: {}", response.orders.len());
    for order in response.orders {
        let amount = order.amount as f64 / 1e18;
        let side = if amount > 0.0 { "买入" } else { "卖出" };  // 正数 = 买入，负数 = 卖出

        println!("  - 订单 ID: {:?}", order.digest);
        println!("    价格: ${:.2}", order.price as f64 / 1e18);
        println!("    数量: {:.4} BTC", amount.abs());
        println!("    方向: {}", side);
    }
}
```

{% endtab %}
{% endtabs %}

---

## 🎯 你刚刚做了什么

在短短 5 分钟内，你完成了：

1. ✅ 安装了 Nado SDK
2. ✅ 连接到了 Nado 测试网（Ink Sepolia）
3. ✅ 创建了子账户（通过充值）
4. ✅ 在 BTC-PERP 下了限价单
5. ✅ 验证了订单已在订单簿上

---

## 🚀 下一步

### 深入学习

- [**核心概念**](https://docs.nado.xyz/developer-resources/get-started/core-concepts) — 深入了解认证、子账户、关联签名者及签名机制
- [**首次充值**](https://docs.nado.xyz/developer-resources/get-started/first-deposit) — 多种充值的详细指南
- [**关联签名者指南**](https://docs.nado.xyz/developer-resources/get-started/linked-signers) — 设置一键交易和安全机器人密钥

### 继续开发

- [**第一笔交易**](https://github.com/nadohq/nado-docs/blob/main/docs/developer-resources/get-started/first-trade.md) — 高级订单类型和交易策略
- [**API 参考文档**](https://docs.nado.xyz/developer-resources/api) — 完整 API 文档
- [**常见错误排查**](https://docs.nado.xyz/developer-resources/get-started/common-errors) — 解决签名错误和充值问题

### SDK 文档

- [**Python SDK**](https://nadohq.github.io/nado-python-sdk/index.html) — 完整 Python SDK 文档
- [**TypeScript SDK**](https://docs.nado.xyz/developer-resources/typescript-sdk) — 完整 TypeScript SDK 指南
- [**Rust SDK**](https://crates.io/crates/nado-sdk) — crates.io 上的 Rust SDK

---

## 💡 进阶技巧

### 撤销测试订单

如果你想撤销刚下的订单：

{% tabs %}
{% tab title="Python" %}

```python
from nado_protocol.engine_client.types.execute import CancelOrdersParams

# 使用下单返回的 digest 取消订单
cancel_params = CancelOrdersParams(
    sender=subaccount_hex,
    productIds=[product_id],
    digests=[result.data.digest]
)
client.market.cancel_orders(cancel_params)
print("✅ 订单已撤销")
```

{% endtab %}

{% tab title="TypeScript" %}

```typescript
// 使用 placeOrder 返回的 digest 取消订单
await client.market.cancelOrders({
  subaccountName: 'default',
  productIds: [productId],
  digests: [result.data.digest]
});

console.log('✅ 订单已撤销');
```

{% endtab %}

{% tab title="Rust" %}

```rust
// 使用 place_order 返回的 digest 取消订单
let digest = result.unwrap().digest;

client
    .cancellation_builder()
    .digests(vec![digest])
    .product_ids(vec![product_id])
    .execute()
    .await?;

println!("✅ 订单已撤销");
```

{% endtab %}
{% endtabs %}

---

### 下市价单（即时成交）

想要立即成交？通过设置激进价格下市价单：

{% tabs %}
{% tab title="Python" %}

```python
from nado_protocol.engine_client.types.execute import PlaceOrderParams, OrderParams
from nado_protocol.utils.math import to_x18
from nado_protocol.utils.order import build_appendix
from nado_protocol.utils.expiration import OrderType, get_expiration_timestamp
from nado_protocol.utils.bytes32 import subaccount_to_hex
from nado_protocol.utils.nonce import gen_order_nonce

# 市价买入：设置高价（将以最优卖价成交）
# 市价卖出：设置低价（将以最优买价成交）
market_buy_price = current_price * 1.10  # 高于市价 10%

# 创建市价单（IOC = 即时取消）
subaccount_hex = subaccount_to_hex(client.context.signer.address, "default")
order = OrderParams(
    sender=subaccount_hex,
    priceX18=to_x18(market_buy_price),
    amount=to_x18(0.001),  # 正数 = 买入
    expiration=get_expiration_timestamp(10),  # 短有效期
    nonce=gen_order_nonce(),
    appendix=build_appendix(OrderType.IOC)  # 即时取消
)

params = PlaceOrderParams(product_id=product_id, order=order)
result = client.market.place_order(params)

print("✅ 市价单已下（立即成交！）")
```

{% endtab %}

{% tab title="TypeScript" %}

```typescript
import { toFixedPoint, packOrderAppendix, nowInSeconds } from '@nadohq/shared';

// 市价买入：设置高价（将以最优卖价成交）
const marketBuyPrice = currentPrice * 1.10;

// 下市价单（将立即成交）
await client.market.placeOrder({
  order: {
    subaccountName: 'default',
    price: marketBuyPrice,
    amount: toFixedPoint(0.001, 18),  // 正数 = 买入
    expiration: nowInSeconds() + 10,  // 短有效期
    appendix: packOrderAppendix({ orderExecutionType: 'ioc' })  // 即时取消
  },
  productId
});

console.log('✅ 市价单已下（立即成交！）');
```

{% endtab %}

{% tab title="Rust" %}

```rust
use nado_sdk::math::f64_to_x18;
use nado_sdk::types::order_types::OrderExecutionType;

// 市价买入：设置高价（将以最优卖价成交）
let market_buy_price = current_price * 1.10;

// 下市价单（IOC = 即时取消）
client
    .place_order_builder()
    .product_id(BTC_PERP)
    .amount(f64_to_x18(0.001))  // 正数 = 买入
    .price_x18(f64_to_x18(market_buy_price))
    .order_execution_type(OrderExecutionType::Ioc)  // 即时取消
    .execute()
    .await?;

println!("✅ 市价单已下（立即成交！）");
```

{% endtab %}
{% endtabs %}

---

## 🆘 故障排查

### "签名不匹配" 错误

参阅 [常见错误：Error 2028](https://docs.nado.xyz/developer-resources/common-errors#error-2028-signature-does-not-match-with-senders-or-linked-signers) 获取详细解决方案。

**快速修复：** 确认你使用的是正确的私钥，并已连接到测试网（Ink Sepolia）。

---

### "子账户不存在" 错误

参阅 [常见错误：Error 2024](https://docs.nado.xyz/developer-resources/common-errors#error-2024-provided-address-has-no-previous-deposits) 获取详细解决方案。

**快速修复：** 确认你已充值 ≥ $5 USDT0 并等待了确认。

---

### 需要帮助？

- **Telegram 社区**：[加入 Telegram](https://t.me/+whsZJKpiiVwwNjQ0)
- **提交工单**：[Nado Support](https://nado-90114.zendesk.com/hc/en-us/requests/new?ticket_form_id=52275013155481)

---

{% hint style="success" %}
**你已准备好开始交易！** 🎉

本快速入门已让你快速上手。投入生产使用前，请阅读 [核心概念](https://docs.nado.xyz/developer-resources/get-started/core-concepts) 和 [关联签名者](https://docs.nado.xyz/developer-resources/get-started/linked-signers) 指南，了解最佳实践和安全注意事项。
{% endhint %}

---

*本指南由 马龙 🏓 翻译整理 | 2026-04-18*
*原文：https://docs.nado.xyz/developer-resources/get-started/quickstart*
