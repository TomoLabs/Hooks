🧩 TomoLabs – FeeToSplitter Hook A Revenue-Sharing Uniswap v4 Hook for Automated Fee Splitting

This repository contains two smart contracts:

FeeSplitter.sol → Splits any ERC-20 token amount among recipients based on percentage shares.

FeeToSplitterHook.sol → A Uniswap v4 Hook that intercepts swap fees and automatically routes them to the FeeSplitter contract.

Together, they implement creator-aligned liquidity, where fees earned in a Uniswap pool are immediately distributed to predefined recipients.

🚀 Features ✔ Automatic fee sharing

Every time a swap occurs and fees are collected, the hook calls FeeSplitter.distribute().

✔ Basis-Point (BPS) share configuration

Total shares must always equal 10000 (100%).

✔ Flexible recipients

You can specify any number of recipients (creators, LP managers, DAO multisigs, etc.).

✔ Fully compatible with Uniswap v4 Hooks

Implements the exact required 14-permission structure.

📦 Contracts

FeeSplitter.sol
A lightweight contract that splits incoming tokens according to pre-configured BPS percentages.

Constructor constructor(address[] memory _recipients, uint256[] memory _shares);

distribute() function distribute(address token, uint256 amount) external;

This function transfers:

amount * share[i] / 10000

to each recipient.

FeeToSplitterHook.sol
A Uniswap v4 Hook that triggers after swaps (afterSwap). It detects positive fee deltas and passes them to FeeSplitter.

Key Responsibilities:

Reads swap deltas

Extracts token-0 delta

Calls FeeSplitter to distribute fees

Only enables required Uniswap hook permissions

🗂 Directory Structure src/ ├── FeeSplitter.sol └── FeeToSplitterHook.sol

script/ └── DeployFeeHook.s.sol

⚙️ Installation git clone https://github.com/TomoLabs/Hooks.git cd Hooks forge install forge build

🔧 Configuration

You can adjust the following in the deploy script:

Pool Manager Address 0xA7B8e01F655C72F2fCf7b0b8F9E0633D5c86B8Dc

Fee Token 0x00000000000000000000000000000000000000AA

Recipients 0x27eB14742eC8Fe485492A5B553ec9D13Db5F0Af4 0x0000000000000000000000000000000000000022

Shares 6000 = 60% 4000 = 40%

These are configured inside:

script/DeployFeeHook.s.sol

🚀 Deployment

Run:

forge script script/DeployFeeHook.s.sol --rpc-url --broadcast -vvvv

Example:

forge script script/DeployFeeHook.s.sol --rpc-url https://sepolia.infura.io/v3/ --broadcast -vvvv

This deploys:

FeeSplitter

FeeToSplitterHook

🧠 How It Works Step-by-step swap lifecycle:

User swaps tokens in a Uniswap v4 pool

The hook receives the delta:

delta.amount0()

If amount0 > 0, meaning fees were collected: → the hook calls FeeSplitter:

splitter.distribute(feeToken, uint256(int256(amount0)));

FeeSplitter divides fees among recipients based on basis-points (BPS)

🔍 Example Log Event

When fees are distributed:

FeeDistributed( token = 0x00000000000000000000000000000000000000AA, totalAmount = 12345 )

🧪 Testing (Optional)

Run:

forge test -vvvv

You can write tests to check:

Fee splitting

Swap callbacks

Permission matrix

Reverts when shares ≠ 10000

📄 License

MIT

🏷 Credits

Built by TomoLabs — creator-aligned Web3 liquidity infrastructure.
