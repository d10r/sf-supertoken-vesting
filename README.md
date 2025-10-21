## SuperToken Vesting Operational Guidelines

### Pre-requisite

- Have an ADMIN account created
- Have the TREASURY multisig created and setup
- Have all the insiders data ready and validated :
  - `recipient` address
  - `recipientVestingIndex` for each insider (0 if none exists, 1 if one exists, etc.)
  - `amount` for each insider
  - `cliffAmount` for each insider
  - `cliffDate` for each insider
  - `endDate` for each insider

### Step 1 :

- Deploy the `SupVestingFactory` contract

- Note 1 : In `DeployVesting.s.sol` script, we deploy a dummy `SupVesting` contract for verification purpose.
  This might not be need if we perform a preliminary mainnet deployment

- Note 2 : Once the `SupVestingFactory` contract is deployed, the SNAPSHOT STRATEGY parameters must be updated.

```shell
/*
SUP_ADDRESS=0xa69f80524381275A7fFdb3AE01c54150644c8792 \
VESTING_SCHEDULER_ADDRESS=0x7b77A34b8B76B66E97a5Ae01aD052205d5cbe257 \
ADMIN_ADDRESS={ADMIN_ADDRESS} \
TREASURY_ADDRESS={TREASURY_ADDRESS} \
forge script script/vesting/DeployVesting.s.sol:DeployVestingScript --ffi --rpc-url $BASE_MAINNET_RPC_URL --account SUP_DEPLOYER --broadcast --verify -vvv --etherscan-api-key $BASESCAN_API_KEY
*/
```

### Step 2 :

- Approve the `SupVestingFactory` contract address to spend $SUP tokens from the TREASURY account
- Note : Approved Amount shall be equal to the sum of all insiders' amount

### Step 3 :

- Execute script to create SupVesting contract for each insiders.

Fork test:
```
RPC_URL=... SCHEDULES_FILE=data/schedules_x.tsv script/vesting/run-fork-verification.sh
```

Note: the fork testing script also simulates approval to the factory, thus won't tell you if step 2 is missing.
It's about verifying that all transactions would succeed, with the final expected totalSupply of lockedSUP tokens.

Execution:
```
MODE=EXECUTE ACCOUNT_NAME=... RPC_URL=... ../tasks/create-vestings.sh data/schedules_x.tsv
```

### Step 4 :

- Add each `SupVesting` contract address to the automation whitelist (offchain)

### Step 5 :

- Update `SupVestingFactory` admin account (`SupVestingFactory::setAdmin`) to TREASURY multisig
