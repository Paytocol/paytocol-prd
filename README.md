# Paytocol (paytocol.xyz)

## Bounty Sponsorship
- Circle, based on CCTP 2.0, allows users to withdraw funds from any chain or deposit them into the contract for sponsorship.
- Deposit into specific pools to earn interest.
- ~~1inch, users can choose which token to pay with, it will be converted to USDC first / swapped to interest-bearing rates.~~
- ~~World Mini App~~
- ~~1inch fusion+ whether it can send postMessage~~

## Theme Scenarios

### 🌊 Intent Network Model (current)

#### User Deposit
When a user creates a payment request, the information will utilize tokens according to the "pay with dApp" method:
1. Users can pay tokens through a dApp using the recipient address, amount, token address, expiration date, and receiver address.
1. ~~Utilize 1inch to convert non-stablecoins to stablecoin denominated amounts (1inch swap api).~~
2. Write stablecoins, recipient address, sponsorship period, and other information into the backend and interact with the contract.
3. Utilize CCTP to cross-chain stablecoins to Paytocol contract on Base.
4. Solver helps deposit interest-bearing assets into the Morpho protocol from the contract and then transfers the assets back to the Paytocol contract.

#### Guardian (only show in system diagram)
Used to verify whether on-chain transactions are completed, and can change the status of an order, such as verifying close or challenging open.

#### Paytocol Solver
When a sponsorship order appears, the Solver can obtain order information from the backend/contract and further obtain the settlement right of the order based on the collateral deposit.
- Determine whether the time is completed based on blocktime.
- Responsible for handling post-interest-bearing cross-chain behavior and withdrawals for users.
- It can be an ecosystem of a cross-chain streaming protocol.

#### Withdrawal
The Solver needs to complete the order within plus or minus 1 hour based on blocktime.
1. The recipient can set the chain and wallet for receiving funds in the backend when withdrawing.
2. The Solver needs to invoke Paytocol to swap the interest-bearing assets back from Morpho and then transfer them to the recipient.
