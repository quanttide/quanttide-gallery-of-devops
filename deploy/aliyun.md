# aliyun CLI

aliyun CLI 的产品命令集按需安装，询价接口不在 `domain` 产品，而在 BSS OpenAPI。

## 规则

- 很多产品在 CLI 里是「插件可装但未安装」的状态
- 产品插件未安装时，该产品的命令集不全
- `domain` 产品没有询价接口，查价走 `bssopenapi QuerySkuPriceList`

## 工作流

### 1. 判断命令集不全的原因

使用某产品命令前，先确认该产品的插件是否已安装。很多产品是「插件可装但未安装」的：插件可以安装，但默认未装。未安装时该产品的命令集不全，表现为命令缺失或不完整——这是插件未安装的正常状态，不是 CLI 本身故障。

### 2. 查价走 bssopenapi QuerySkuPriceList

`domain` 产品没有询价接口。查价使用 `bssopenapi QuerySkuPriceList`，参数为 `--CommodityCode` 和 `--PriceEntityCode`：

```bash
aliyun bssopenapi QuerySkuPriceList --CommodityCode <CommodityCode> --PriceEntityCode <PriceEntityCode>
```

| 参数 | 含义 |
|------|------|
| `--CommodityCode` | 商品码 |
| `--PriceEntityCode` | 价格实体码 |

## 常见错误

| 现象 | 原因 | 处理 |
|------|------|------|
| 某产品命令缺失、命令集不全 | 该产品插件可装但未安装 | 先安装对应产品插件 |
| 在 `domain` 下找不到询价接口 | `domain` 产品没有询价接口 | 改走 `bssopenapi QuerySkuPriceList`，参数 `--CommodityCode`/`--PriceEntityCode` |
