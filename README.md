# Paytocol
An intent based payment protocol by working with different solvers.


## Grant sponsorship
- Circle for CCTP2.0 to claim tokens
- 1inch for interest rate's swap


## User scenario
- Pay with dApp
    - User can pay tokens via dApp with payee address, amount, token address, period, and recipient address
- Pay with Transfer
    - User can pay tokens via wallet's send function without any specific data on chain
    - The payment detail requires 
        - a specific address with specific rules to deposit
        - or a frontend + backend to detect and write the records to backend


## How it work
When a user create a payment request, the information will be record on chain according to the Pay with dApp. 
The first of solver can get the payment by depositing the bond.
When the payment is done by the solver, and the solver can claim its bond and the extra interest rates from the protocol.
If the user's payment is recurring / repeatable, the first split's winner will be the first place of the upcoming order.

Why Solver?
The Solver execute the user's intent without centralized gatekeeper. 





## Problems to solve
- How to validate the payment works as user's expectation


- How to validate a payment without a central authority
- How to determine the specific token's belong to a specific user without a central authority
- Consensus on the transaction
    - In central way: Challenge period
    - In decentral way: Proof of work
- How to prevent double spending
    - In central way: Central server remembers all transactions
    - In decentral way: 



