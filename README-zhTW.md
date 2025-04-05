# Paytocol (paytocol.xyz)

## 獎勵贊助
- Circle，基於 CCTP2.0 讓用戶可以在任意鏈將資金提領出來或是存入合約來贊助
- 存入特定池子來生利息
- ~~1inch，用戶可以選擇用什麼代幣來支付，會先轉為 USDC / 換為可生息的利率~~
- ~~World Mini App~~
- ~~1inch funsion+ 是否能夠發 postMessage~~

## 主題情境

### 🌊 意圖網路模式 (current)

#### 使用者進行贊助
當使用者創建一個支付請求時，資訊將根據「使用 dApp 支付」的方式將代幣利用
有三個角色在這個交易過程中
- 使用者，想要發起交易的用戶
- Paytocol 合約，負責與 CCTP 進行跨鏈溝通; 存款托管
- Solver，協助在跨鏈後進行存入 Morpho 操作
流程如下:
1. 使用者跟 dApp 互動發起交易，包含收款人地址、金額、代幣地址、週期將代幣存入合約
2. 將穩定幣，收款人地址，贊助週期等資訊寫入 backend 與合約互動
3. 將穩定幣利用 CCTP 跨鏈到 Base 的 Paytocol 合約
4. Solver 幫忙從合約存進會生息資產的 Morpho 協議 再將資產轉回 Paytocol 合約

#### Guardian (only show in system diagram)
用來驗證鏈上交易是否完成，能對一個訂單進行狀態的改變，例如 驗證 close 或是挑戰 open。

#### Paytocol Solver
當有一筆贊助的訂單出現時，Solver 可以跟 backend/合約 取得訂單資訊，並且進一步去根據抵押存款來取得訂單的結算權。
- 根據 blocktime 來決定時間完成與否。
- 負責處理生息跨鏈後的行為與提領給用戶
- 他可以是一個 cross chain streaming protocol 的生態。

#### 被贊助者提領資金
有三個角色在這個交易過程中
- 收款方，想要收款的用戶
- Base Paytocol 合約，負責存款托管
- Solver，協助收款方將資產從 Morpho 提領出來

Solver 需要根據 blocktime 在正負 1 小時內將訂單完成，收款方可以在後台設定提領時選擇在那個鏈與錢包收款

1. Solver 對 Base Paytocol 合約發起交易
2. Base Paytocol 會把生息資產從 Morpho 換回 USDC 然後轉進收款方


### 系統設計

#### 發起一筆轉帳

```
sequenceDiagram
    participant User
    participant dApp
    participant Backend
    participant Paytocol Contract
    participant CCTP
    participant Solver
    participant Base Paytocol Contract
    participant Morpho Protocol

    User->>dApp: 發起交易 (收款人地址, 金額, 代幣地址, 週期)
    dApp->>Paytocol Contract: 寫入鏈上資訊，存入代幣
    dApp->>Backend: 發送交易資訊 (穩定幣, 收款人地址, 贊助週期)
    Paytocol Contract->>CCTP: 發起跨鏈請求 (穩定幣)
    CCTP-->>Base Paytocol Contract: 跨鏈成功 (穩定幣)
    Base Paytocol Contract-->>Solver: 觸發完成執行事件
    Solver->>Base Paytocol Contract: 查詢訂單資訊
    Base Paytocol Contract-->>Solver: 返回訂單資訊
    Solver->>Base Paytocol Contract: 從合約提取穩定幣
    Base Paytocol Contract->>Morpho Protocol: 存入穩定幣以賺取利息
    %% Morpho Protocol-->>Base Paytocol Contract: 存款成功
    %% Base Paytocol Contract-->>Morpho Protocol: 提取成功 (生息資產)
    %% Base Paytocol Contract->>Base Paytocol Contract: 存回 Paytocol 合約
    %% Base Paytocol Contract->>Morpho Protocol: 從 Morpho 提取生息資產
```

```
sequenceDiagram
    participant User
    participant dApp
    participant Backend
    participant Paytocol Contract
    participant CCTP
    participant Solver
    participant Base Paytocol Contract
    participant Morpho Protocol

    User->>dApp: Initiate Transaction (Recipient Address, Amount, Token Address, Period)
    dApp->>Paytocol Contract: Write On-chain Information, Deposit Tokens
    dApp->>Backend: Send Transaction Information (Stablecoin, Recipient Address, Sponsorship Period)
    Paytocol Contract->>CCTP: Initiate Cross-chain Request (Stablecoin)
    CCTP-->>Base Paytocol Contract: Cross-chain Successful (Stablecoin)
    Base Paytocol Contract-->>Solver: Trigger Completion Event
    Solver->>Base Paytocol Contract: Query Order Information
    Base Paytocol Contract-->>Solver: Return Order Information
    Solver->>Base Paytocol Contract: Withdraw Stablecoin from Contract
    Base Paytocol Contract->>Morpho Protocol: Deposit Stablecoin to Earn Interest
    %% Morpho Protocol-->>Base Paytocol Contract: Deposit Successful
    %% Base Paytocol Contract-->>Morpho Protocol: Withdrawal Successful (Interest-Bearing Assets)
    %% Base Paytocol Contract->>Base Paytocol Contract: Deposit Back to Paytocol Contract
    %% Base Paytocol Contract->>Morpho Protocol: Withdraw Interest-Bearing Assets from Morpho
```

#### 被贊助者提領資金

```
sequenceDiagram
    participant Solver
    participant Base Paytocol Contract
    participant Morpho Protocol
    participant Recipient

    Solver->>Base Paytocol Contract: 發起提領交易
    Base Paytocol Contract->>Morpho Protocol: 從 Morpho 換回 USDC (生息資產)
    Morpho Protocol-->>Base Paytocol Contract: USDC 兌換完成
    Base Paytocol Contract->>Recipient: 將 USDC 轉給收款方
```

```
sequenceDiagram
    participant Solver
    participant Base Paytocol Contract
    participant Morpho Protocol
    participant Recipient

    Solver->>Base Paytocol Contract: Initiate Withdrawal Transaction
    Base Paytocol Contract->>Morpho Protocol: Swap Interest-Bearing Assets back to USDC
    Morpho Protocol-->>Base Paytocol Contract: USDC Swap Completed
    Base Paytocol Contract->>Recipient: Transfer USDC to Recipient
```
