# Paytocol (paytocol.xyz)

## Reward Sponsorship
- Circle, based on CCTP2.0, allows users to withdraw funds from any chain or deposit them into the contract for sponsorship.
- Deposit into specific pools to generate interest.

## Theme Scenario

### 🌊 Intent Network Model

#### User depsoit funds
When a user creates a payment request, the information will utilize tokens according to the "pay with dApp" method.
There are three roles in this transaction process:
- User: The user who wants to initiate the transaction.
- Paytocol Contract: Responsible for cross-chain communication with CCTP; deposit custody.
- Solver: Assists in performing Morpho deposit operations after cross-chain transfer.
The process is as follows:
1. The user interacts with the dApp to initiate a transaction, including the recipient address, amount, token address, and period, and deposits the tokens into the contract.
2. Write stablecoin, recipient address, sponsorship period, and other information into the backend and interact with the contract.
3. Utilize CCTP to cross-chain transfer stablecoins to the Paytocol contract on Base.
4. The Solver helps deposit stablecoins from the contract into the interest-bearing asset Morpho protocol and then transfers the assets back to the Paytocol contract.

#### Guardian (only show in system diagram)
Used to verify whether on-chain transactions are completed, and can change the status of an order, such as verifying to close or challenging to open.

#### Paytocol Solver
When a sponsorship order appears, the Solver can obtain order information from the backend/contract and further obtain the settlement right of the order based on the collateral deposit.
- Determine whether the time is completed based on blocktime.
- Responsible for handling post-interest-bearing cross-chain behavior and withdrawals for users.
- It can be an ecosystem of a cross-chain streaming protocol.

#### Withdraw funds
There are three roles in this transaction process:
- Recipient: The user who wants to receive funds.
- Base Paytocol Contract: Responsible for deposit custody.
- Solver: Assists the recipient in withdrawing assets from Morpho.

The Solver needs to complete the order within plus or minus 1 hour based on block time. The recipient can set the chain and wallet for receiving funds in the backend when withdrawing.

1. The Solver initiates a transaction to the Base Paytocol contract.
2. Base Paytocol will swap the interest-bearing assets from Morpho back to USDC and then transfer them to the recipient.


### System Design

#### Initiate a Transfer

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
```

#### Sponsored Party Withdraws Funds

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


