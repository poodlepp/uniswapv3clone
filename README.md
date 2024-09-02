### some notes about uniswapV3

> source  https://y1cunhui.github.io/uniswapV3-book-zh-cn/

#### milestone0
- 做市商AMM
  - 恒定函数做市商 (Constant Function Market Makers) CFMM
  - ![alt text](readme_img/image01.png)
- 流动性
- AMM
- pool
- V2的缺点
  - 不适合稳定币，CURVE更适合
  - 资产利用率不足
- V3
  - 集中流动性
    - LP选择提供流动性的范围，更多的流动性提供在狭窄的空间
    - 许多的价格区间，区间内与v2类似
  - 公式
    - $L = \sqrt{xy}$
    - $\sqrt{P} = \sqrt{\frac{y}{x}}$
    - 平方根直接存储，而不是立即计算
    - $L = \frac{\Delta y}{\Delta \sqrt P}$
    - 可以得出x y  L  P 之前的关系
  - ticks
    - $p(i) = 1.0001^i$
    - 我们会用 Q64.96定点数 存储 $\sqrt P$
      - 也就是说 P不开根号的取值范围是 $2^{128}$ - $2^{-128}$
      - 以1.0001为底，得出tick的范围 [−887272,887272]

- 其他
  - evm使用的是LevelDB 高效的kv数据库
  - 每个节点支持 JSON APIs
    - eth_sendTransaction


#### milestone1
- 设定
  - ETH/USDC 现货价格 1:5000
  - 流动性区间 4545 - 5500
- 计算
  - 计算三个节点的 $\sqrt P$
    - $p_l$   下界
    - $p_c$   现货
    - $p_u$   上界
  - 根据$p(i)$公式可以得出
    - 三个节点的tick位置
      - 85176   84222  86129
- 计算区间流动性
  - 两个方向都计算L，选较小的L
  - 通过一通计算（主要依赖L Delta y, Delta x, P的关系）
  - 得出最小L 反推 Delta x,  Delta y
    - eth的数量，usdc的数量
  - 逻辑顺序
    - 知道了价格区间
    - 知道了流动性
    - 得出需要提供的x,y
- 完成代码
  - mint swap 公式计算部分省略
  - test
  - deploy
- 技巧
  - forge inspect aapool abi
  - cast call
  - cast --abi-decode
  - cast --to-dec
