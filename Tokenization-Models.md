# TOKEN STRUCTURES FOR FIXED INCOME STRATEGIES
## A Comprehensive Analysis: Bond Funds, Lending Protocols, and Tokenization Architecture

---

## EXECUTIVE SUMMARY

This paper examines three fundamental token structures applicable to fixed income strategies, analyzing the architectural distinctions between on-chain and off-chain components. While the primary focus is tokenizing traditional bond fund operations, the analysis also examines existing lending protocols (Maple, Goldfinch) to understand how LP token structures function in practice - though these represent a different operational model than traditional bond portfolio management.

Through detailed analysis of existing implementations (Franklin BENJI for money markets, Ondo OUSG for treasuries) and examination of technical architecture, we demonstrate that contrary to common misconceptions, tokenization primarily affects ownership tracking rather than asset management operations.

**Scope:**
- **Bond Funds:** Traditional portfolio management buying/selling bonds in secondary markets
- **Lending Protocols:** Direct loan origination platforms (different model, included for LP token examples)
- **Token Structures:** How yield flows to investors regardless of underlying strategy

**Key Findings:**
- Asset management remains entirely off-chain using traditional infrastructure
- Token structures differ primarily in how yield flows to investors
- All three structures can work for bond funds, with different trade-offs
- The choice depends on duration risk, credit risk, target investors, and DeFi integration needs
- **Critical gap:** True tokenized bond funds (trading corporate bonds) don't exist yet - only treasuries and money markets tokenized so far

---

## 1. INTRODUCTION

### 1.1 Scope and Terminology

**Primary Focus: Bond Fund Tokenization**

This analysis examines token structures for traditional bond portfolio management:
- Buying/selling bonds in secondary markets
- Trading via Bloomberg, brokers, dealer networks
- Daily mark-to-market NAV calculation
- Active duration and credit positioning

**Also Examined: Lending Protocols (For Context)**

The study includes lending protocols (Maple Finance, Goldfinch) to illustrate LP token structures in practice, though these represent a fundamentally different model:
- Direct loan origination to borrowers
- Hold-to-maturity approach
- Illiquid by design
- Different from secondary market bond trading

**Key Distinction:**
```
Traditional Bond Fund:
└─ You = Portfolio Manager
    └─ Buy/sell bonds via dealers
    └─ Mark to market daily
    └─ Active trading

Lending Protocol:
└─ You = Credit Underwriter
    └─ Originate loans directly
    └─ Hold to maturity
    └─ Passive after origination

Both can use similar token structures,
but underlying operations are different!
```

**CRITICAL INSIGHT: Token Structure ≠ Investment Strategy**

```
LP Token Structure:
├─ Token represents % of pool
├─ Price = Pool Value / Token Supply
├─ Performance fees via token minting
├─ Quarterly redemptions
└─ This is just HOW THE TOKEN WORKS

What's In The Pool Can Be:
├─ Lending Protocol (Maple, Goldfinch):
│   └─ Pool holds: Direct loans to borrowers
│       └─ Illiquid, hold to maturity
│
├─ Traditional Bond Fund (Doesn't exist yet, but COULD):
│   └─ Pool holds: Corporate bonds from secondary market
│       └─ Liquid, actively traded
│
├─ Hedge Fund:
│   └─ Pool holds: Multi-strategy positions
│       └─ Bonds, derivatives, leverage
│
└─ LP token structure works for ALL of these!

Example: Your Potential Bond Fund
├─ Off-Chain: You trade IG corporate bonds on Bloomberg
├─ Off-Chain: Mark to market daily, calculate pool value
├─ On-Chain: LP tokens represent % of pool
├─ Performance Fee: 20% of profits above 5% hurdle
├─ Redemptions: Quarterly
└─ Token structure: LP token (same as Maple!)
    But strategy: Traditional bond PM (different from Maple!)

The LP token is just the wrapper.
What you manage inside is up to you!
```

### 1.2 What Tokenization Actually Means

A critical misunderstanding in discussions of tokenized funds is the belief that tokenization requires moving asset management on-chain. In reality:

**Tokenization = Blockchain-based ownership tracking**  
**NOT = On-chain asset management**

The fundamental architecture consists of two independent layers:

```
┌─────────────────────────────────────────────────────────┐
│                 ASSET MANAGEMENT LAYER                   │
│                      (Off-Chain)                         │
├─────────────────────────────────────────────────────────┤
│                                                          │
│  • Traditional custody (banks hold securities)           │
│  • Traditional trading (Bloomberg Terminal, brokers)     │
│  • Traditional NAV calculation (administrator/in-house)  │
│  • Traditional risk management systems                   │
│                                                          │
│  Nothing changes from traditional fund operations        │
└─────────────────────────────────────────────────────────┘
                            │
                            │ Daily NAV feed via oracle
                            ↓
┌─────────────────────────────────────────────────────────┐
│              OWNERSHIP TRACKING LAYER                    │
│                     (On-Chain)                           │
├─────────────────────────────────────────────────────────┤
│                                                          │
│  • Smart contract tracks token ownership                 │
│  • Records daily NAV updates                             │
│  • Enforces transfer restrictions (compliance)           │
│  • Processes subscriptions/redemptions                   │
│                                                          │
│  This is the only on-chain component                     │
└─────────────────────────────────────────────────────────┘
```

### 1.2 The Oracle Bridge

The only new infrastructure component required is an **oracle** - a mechanism to bring off-chain NAV calculations on-chain:

```
Traditional NAV Calculation (Off-Chain)
    ↓
Oracle Service (Chainlink or custom)
    ↓
Smart Contract Update (On-Chain)
    ↓
Token Price Auto-Updates
```

This oracle can be:
- **Centralized:** Fund administrator posts directly (lowest cost)
- **Decentralized:** Chainlink or similar service (higher trust)
- **Hybrid:** Multiple data sources with consensus

The key point: NAV calculation itself remains entirely off-chain using traditional methods.

---

## 2. ARCHITECTURAL FOUNDATIONS

### 2.1 Off-Chain Layer (Asset Management)

Regardless of token structure chosen, the asset management layer is identical to traditional fund operations:

**Portfolio Management:**
- Trade bonds via traditional broker-dealers
- Use Bloomberg Terminal for pricing and analytics
- Execute hedges through futures/swaps markets
- Manage duration, convexity, and credit exposure
- Monitor risk using institutional risk systems

**Custody:**
- Securities held at qualified custodian banks
- Traditional settlement (T+2 for corporate bonds, T+1 for treasuries)
- Safe-keeping of physical or book-entry securities
- Corporate actions processing

**NAV Calculation:**
- Mark bonds to market using Bloomberg/vendor prices
- Calculate accrued interest
- Account for cash positions
- Deduct fees and expenses
- Produce end-of-day NAV

**Example Daily Flow:**
```
08:00 - Portfolio manager reviews positions
09:00 - Trading activity on traditional platforms
12:00 - Risk monitoring and adjustment
16:00 - NAV calculation using traditional methods
16:05 - NAV sent to oracle (ONLY new step)
16:06 - Oracle posts to blockchain (automated)
```

The only addition to traditional operations: a 1-minute process to post NAV on-chain daily.

### 2.2 On-Chain Layer (Ownership Tracking)

The smart contract performs a limited set of functions:

**Core Responsibilities:**
1. **Track token ownership** - who owns how many tokens
2. **Record NAV updates** - receive daily NAV from oracle
3. **Calculate token price** - NAV / token supply
4. **Enforce compliance** - whitelist, transfer restrictions
5. **Process subscriptions** - mint new tokens when investors subscribe
6. **Process redemptions** - burn tokens when investors redeem

**What Smart Contracts Do NOT Do:**
- ❌ Trade bonds
- ❌ Hold securities
- ❌ Calculate NAV
- ❌ Make investment decisions
- ❌ Execute portfolio strategy
- ❌ Manage risk

### 2.3 Information Flow

```
SUBSCRIPTION PROCESS:

Off-Chain:
1. Investor completes KYC/AML
2. Investor wires cash to fund
3. Fund receives cash at custody bank
4. Portfolio manager buys bonds with cash
5. Administrator records subscription

On-Chain:
6. Smart contract mints tokens to investor
7. Token balance appears in investor's wallet
⏱️ Steps 6-7 take ~12 seconds

DAILY NAV UPDATE:

Off-Chain:
1. Mark all bonds to market (Bloomberg)
2. Calculate accrued interest
3. Account for cash and expenses
4. Compute total NAV
5. NAV calculation complete

On-Chain:
6. Oracle receives NAV via API
7. Oracle posts transaction to blockchain
8. Smart contract records new NAV
9. Token price auto-updates (NAV/supply)
⏱️ Steps 6-9 take ~1 minute

REDEMPTION PROCESS:

On-Chain:
1. Investor requests redemption via smart contract
2. Tokens burned immediately
3. Redemption event emitted
⏱️ Takes ~12 seconds

Off-Chain:
4. Fund operations sees redemption request
5. Portfolio manager sells bonds
6. Bond settlement (T+2)
7. Wire cash to investor
⏱️ Takes 2-3 business days
```

---

## 3. THE THREE TOKEN STRUCTURES

### 3.1 Structure 1: NAV-Backed Fund Token

**Concept:** Token represents traditional fund share, attempts to maintain stable price.

**Mechanics:**
- Target price: $1.00 (like money market funds)
- Token supply: Floats with dividend distributions
- Yield distribution: Periodic dividends OR automatic reinvestment

**Token Price Formula:**
```
Price = NAV / Token Supply
Target: Maintain $1.00 by adjusting supply
```

**Off-Chain Operations:**
```
Asset Management (Traditional):
├─ Buy short-term securities (T-bills, commercial paper)
├─ Duration: <90 days (minimal rate risk)
├─ Calculate daily NAV
└─ Determine yield to distribute

Daily at 4pm:
├─ NAV calculated: $10,040,000
├─ Target: Keep price at $1.00
├─ Yield earned: $40,000
├─ Decision: Distribute as more tokens
└─ Post to oracle
```

**On-Chain Mechanics:**
```solidity
contract NAVBackedToken {
    uint256 public targetNAV = 1e18;  // $1.00
    uint256 public currentNAV;
    
    function updateNAV(uint256 newNAV) external {
        currentNAV = newNAV;
        
        // If NAV above target, distribute yield
        if (currentNAV > targetNAV) {
            distributeYield();
        }
    }
    
    function distributeYield() internal {
        // Calculate excess over $1.00 target
        uint256 excess = currentNAV - targetNAV;
        
        // Mint proportional tokens to all holders
        // (Increases everyone's balance)
        
        // Reset NAV to $1.00
        currentNAV = targetNAV;
    }
}
```

**Example Flow:**
```
Day 0: Launch
├─ Raise $10M, buy T-bills
├─ Issue 10M tokens at $1.00
└─ NAV = $10M, Supply = 10M, Price = $1.00

Day 30: Yield Accumulation
├─ Off-chain: T-bills earned $40,000
├─ Off-chain: NAV = $10,040,000
├─ On-chain: Distribute 40,000 tokens to holders
└─ NAV = $10,040,000, Supply = 10.04M, Price = $1.00

Investor Experience:
- Started with 100,000 tokens
- Now has 100,400 tokens
- Still worth $1.00 each
- Total value: $100,400 (4% gain)
```

**Application to Bond Funds:**

✅ **Suitable for:**
- Money market funds
- Ultra-short duration funds (<1 year)
- Products where NAV stability is realistic
- Retail-focused products (familiar $1 = 1 share)

❌ **Not suitable for:**
- Funds with interest rate risk (duration >1 year)
- Credit-focused strategies
- Any significant mark-to-market volatility

**The Rate Risk & Credit Risk Problem:**

NAV-backed structures fundamentally break when NAV moves significantly from $1. Both rate risk AND credit risk cause this:

```
Example: 5-Year IG Corporate Bond Fund
Duration: 4.5 years
Spread Duration: 4.0 years

SCENARIO 1: Rate Risk (Fed Hikes 100bps)
├─ Off-chain: Duration effect = -4.5%
├─ Off-chain: NAV drops from $10M to $9.55M
├─ On-chain: True NAV per token = $0.955
│   
Options:
1. Let price fall to $0.955 → "De-pegging" defeats entire purpose!
2. Burn 4.5% of tokens → Investor tokens vanish (nightmare UX)
3. Absorb loss in fund → Fund becomes insolvent
└─ All options fail!

SCENARIO 2: Credit Risk (Spreads Widen 50bps)
├─ Off-chain: Spread duration effect = -2%
├─ Off-chain: NAV drops from $10M to $9.8M
├─ On-chain: True NAV per token = $0.98
│   
Same impossible choices:
1. De-peg to $0.98 → Why have NAV-backed structure?
2. Burn 2% of tokens → Tokens disappearing = panic
3. Fund absorbs loss → Becomes insolvent
└─ Structure breaks!

SCENARIO 3: Credit Event (Issuer Defaults)
├─ Off-chain: 5% position in defaulted bond
├─ Off-chain: Write-down 60% = -3% fund NAV
├─ Off-chain: NAV drops from $10M to $9.7M
├─ On-chain: True NAV per token = $0.97
└─ Cannot maintain $1 peg with credit losses!
```

**Why Price De-Pegging Defeats the Purpose:**

```
The Entire Point of NAV-Backed:
└─ Maintain stable $1.00 price
    └─ Like money market funds
    └─ Stablecoin-like user experience
    └─ Familiar to retail investors

If Price De-Pegs:
├─ Token price = $0.95
├─ You've just created... a regular token
├─ Not different from yield-bearing non-rebasing!
├─ Lost all benefits of NAV-backed structure
└─ Plus you have the complexity of distributions

Example:
├─ NAV-backed de-pegged to $0.95 
├─ = Same as yield-bearing at $0.95
├─ But with more complexity (distribution mechanism)
└─ Why not just use yield-bearing from start?

Conclusion: 
If you can't maintain peg, don't use NAV-backed structure.
Only money markets can maintain peg.
```

**Does Anyone Actually Run Bond Funds This Way?**

**Answer: NO. Not with duration/credit risk.**

```
Reality Check:
├─ Money Market Funds with NAV-Backed: ✅ YES
│   ├─ Franklin BENJI (government money market)
│   ├─ Backed Finance MMF
│   └─ Works because <30 day duration, no credit risk
│
├─ Bond Funds (duration >1yr) with NAV-Backed: ❌ NO
│   ├─ No examples exist
│   ├─ Would immediately de-peg with rate moves
│   └─ Fundamentally incompatible
│
└─ Bond Funds Use Instead:
    ├─ Ondo OUSG: Yield-bearing (treasuries)
    ├─ Maple: LP token (corporate credit)
    ├─ Backed ibGBP: Yield-bearing (UK gilts)
    └─ All avoid NAV-backed for good reason!
```

**Conclusion: NAV-backed incompatible with BOTH rate risk AND credit risk**

The structure only works when:
- Duration < 90 days (minimal rate sensitivity)
- Credit quality: government only (no spread risk)  
- Can credibly maintain $1.00 NAV
- = Money market funds only

For any bond fund with:
- Duration > 1 year → Use yield-bearing or LP token
- Credit exposure → Use yield-bearing or LP token
- Mark-to-market volatility → Use yield-bearing or LP token

**Real-World Example: Franklin BENJI**

```
Structure:
├─ Off-Chain: U.S. government money market fund
│   ├─ T-bills, repos, short treasuries
│   ├─ WAM: ~30 days (minimal rate risk)
│   ├─ Traditional custody (bank accounts)
│   └─ Franklin PM team manages via Bloomberg
│
├─ On-Chain: Token on Stellar blockchain
│   ├─ 1 BENJI = 1 fund share at $1.00
│   ├─ Daily yield distributed as more tokens
│   ├─ Transfer restrictions enforced
│   └─ Oracle posts NAV daily
│
└─ Why It Works:
    └─ Money market fund CAN maintain $1 NAV
        └─ Minimal duration/credit risk
```

**Evaluation for Bond Funds:**

| Factor | Score | Notes |
|--------|-------|-------|
| **Duration Compatibility** | ⭐ | Only <1yr duration |
| **Credit Strategy Compatibility** | ⭐ | Limited to gov't/high-quality |
| **Investor Familiarity** | ⭐⭐⭐⭐⭐ | Very familiar (like mutual fund) |
| **DeFi Integration** | ⭐ | Poor (floating supply) |
| **Operational Complexity** | ⭐⭐⭐ | Requires distribution mechanism |
| **Regulatory Precedent** | ⭐⭐⭐⭐⭐ | Strong ('40 Act structure) |

**Critical Reality Check:**

```
DOES ANYONE USE NAV-BACKED FOR BOND FUNDS?

Money Market Funds: ✅ YES
├─ Franklin BENJI ($844M)
├─ Other tokenized MMFs
└─ Why it works: <30 day duration, gov't only, stable NAV

Investment Grade Bond Funds: ❌ NO
High Yield Bond Funds: ❌ NO
Emerging Market Debt: ❌ NO
Corporate Credit: ❌ NO

Zero examples exist because:
├─ Rate moves would de-peg price
├─ Credit spreads would de-peg price
├─ Defaults would de-peg price
└─ De-pegging defeats entire purpose!

Even conservative IG funds with 3-year duration:
├─ 50bps rate move = -1.5% NAV
├─ Would de-peg to $0.985
├─ Why have NAV-backed if not at $1?
└─ Just use yield-bearing instead!
```

**Verdict:** Only suitable for money market or ultra-short bond funds. **No examples exist** of successful NAV-backed implementation for traditional bond strategies with duration/credit risk. Not recommended for any bond fund with meaningful mark-to-market volatility.

---

### 3.2 Structure 2: Yield-Bearing Token (Non-Rebasing)

**Concept:** Token represents a bond-like instrument where yield accrues to price appreciation, never distributed.

**Mechanics:**
- Token price: Floats with NAV (appreciates with yield)
- Token supply: Fixed (except subscriptions/redemptions)
- Yield distribution: Never - only accrues to price

**Token Price Formula:**
```
Price = NAV / Token Supply
No target price - floats freely
All returns = price changes
```

**Off-Chain Operations:**
```
Asset Management (Traditional):
├─ Full bond fund operations
│   ├─ Corporate bonds, treasuries, any duration
│   ├─ Active trading on Bloomberg
│   ├─ Duration/credit/curve positioning
│   └─ Mark-to-market daily
│
└─ Key difference from NAV-backed:
    └─ Never distribute yield
    └─ All returns stay in fund → increase NAV
```

**On-Chain Mechanics:**
```solidity
contract YieldBearingToken {
    uint256 public totalAssets;  // Total NAV
    
    // Token price floats with NAV
    function pricePerToken() public view returns (uint256) {
        if (totalSupply() == 0) return 1e18;
        return totalAssets * 1e18 / totalSupply();
    }
    
    // Update NAV from off-chain calculation
    function updateNAV(uint256 newNAV) external {
        totalAssets = newNAV;
        // Price automatically updates via formula
        // No distributions ever
    }
    
    // No distribution function exists!
}
```

**Example Flow:**
```
Day 0: Launch
├─ Raise $10M, buy bond portfolio
├─ Issue 10M tokens at $1.00
└─ NAV = $10M, Supply = 10M, Price = $1.00

Month 1: Positive Carry
├─ Off-chain: Earned $45k coupons
├─ Off-chain: Bonds flat in price
├─ Off-chain: NAV = $10,045,000
├─ On-chain: Oracle updates totalAssets
└─ NAV = $10.045M, Supply = 10M, Price = $1.0045

Month 2: Rate Spike
├─ Off-chain: Earned $45k coupons
├─ Off-chain: Bonds fell 2% = -$200k
├─ Off-chain: NAV = $9,890,000
├─ On-chain: Oracle updates
└─ NAV = $9.89M, Supply = 10M, Price = $0.989

Month 6: Recovery
├─ Off-chain: Accumulated $270k coupons
├─ Off-chain: Bonds rallied 3% = +$300k
├─ Off-chain: NAV = $10,570,000
├─ On-chain: Oracle updates
└─ NAV = $10.57M, Supply = 10M, Price = $1.057

Investor Experience:
- Bought 100,000 tokens at $1.00
- Saw price drop to $0.989 (month 2)
- Now at $1.057 (month 6)
- Total return: +5.7%
- Exact same experience as traditional bond fund!
```

**Mark-to-Market Behavior:**

This structure perfectly mirrors traditional bond fund economics:

```
Traditional Bond Fund:
└─ NAV per share fluctuates with bond prices
└─ Investors see mark-to-market changes
└─ Yield compounds into NAV
└─ Share price = NAV / shares outstanding

Yield-Bearing Token:
└─ Token price fluctuates with bond prices
└─ Investors see mark-to-market changes
└─ Yield compounds into totalAssets
└─ Token price = totalAssets / tokens outstanding

Mechanically identical!
```

**Application to Bond Funds:**

✅ **Excellent for:**
- Any bond fund with duration exposure
- Credit strategies (IG, HY, emerging markets)
- Multi-sector fixed income
- Absolute return strategies
- Anything with mark-to-market volatility

**Duration Risk Handling:**

```
Example: 5-Year IG Corporate Bond Fund

Scenario 1: Rates Rise
├─ Portfolio duration: 4.5 years
├─ Rates up 50bps
├─ Portfolio value: -2.25%
├─ Token price: Drops 2.25%
└─ Correct behavior - risk properly reflected

Scenario 2: Credit Spreads Widen  
├─ Spread duration: 4.0 years
├─ Spreads widen 25bps
├─ Portfolio value: -1%
├─ Token price: Drops 1%
└─ Correct behavior - credit risk reflected

Scenario 3: Curve Steepening
├─ 5s/10s spread: +40bps
├─ Portfolio positioned for steepening
├─ Portfolio value: +0.8%
├─ Token price: Rises 0.8%
└─ Correct behavior - strategy P&L flows through

This is how bond funds should work!
```

**Real-World Example: Ondo OUSG**

```
Structure:
├─ Off-Chain: Invests in BlackRock BUIDL
│   ├─ BUIDL holds U.S. T-bills
│   ├─ BlackRock manages securities
│   ├─ BNY Mellon custody
│   ├─ Duration: ~3 months
│   └─ Yield: ~5% annually
│
├─ On-Chain: Token on Ethereum
│   ├─ Starting price: ~$100
│   ├─ Price appreciation: ~$0.014/day
│   ├─ After 1 year: ~$105
│   ├─ Fixed supply between subs/redeems
│   └─ Daily NAV updates from Ondo oracle
│
└─ DeFi Integration:
    ├─ Collateral in lending protocols (proposed)
    ├─ AMM pools: OUSG/USDC on Uniswap
    ├─ Yield aggregators
    └─ Works because supply is predictable
```

**DeFi Composability:**

The key advantage: fixed supply enables DeFi integration.

```
Aave Lending Protocol:
├─ User deposits 1000 tokens as collateral
├─ Today: Price = $10.50, Value = $10,500
├─ Aave sets LTV: 75%
├─ User can borrow: $7,875
│
├─ Tomorrow: Price = $10.52
├─ Collateral value: $10,520
├─ Borrowing capacity: $7,890
│
└─ Works perfectly - supply doesn't change
    └─ Aave just tracks price changes

Uniswap AMM:
├─ Liquidity pool: TOKEN/USDC
├─ Add: 1000 tokens + $10,500 USDC
├─ Price: $10.50
│
├─ Next day: NAV updates
├─ Token price in pool: $10.52
├─ Pool automatically rebalances
│
└─ Works perfectly - traders arb price difference
    └─ No unexpected supply changes

This is why institutions prefer non-rebasing!
```

**Evaluation for Bond Funds:**

| Factor | Score | Notes |
|--------|-------|-------|
| **Duration Compatibility** | ⭐⭐⭐⭐⭐ | Perfect - all durations work |
| **Credit Strategy Compatibility** | ⭐⭐⭐⭐⭐ | Perfect - spreads flow through |
| **Investor Familiarity** | ⭐⭐⭐⭐ | Familiar (bond fund mechanics) |
| **DeFi Integration** | ⭐⭐⭐⭐⭐ | Excellent (fixed supply) |
| **Operational Complexity** | ⭐⭐⭐⭐ | Simple (just post NAV) |
| **Regulatory Precedent** | ⭐⭐⭐⭐ | Growing (Ondo, others) |

**Verdict:** Optimal structure for most bond fund strategies. Combines traditional bond fund economics with blockchain benefits and DeFi compatibility.

---

### 3.3 Structure 2B: Yield-Bearing Token (Rebasing)

**Concept:** Token price stays at $1, but investor's token balance grows with yield.

**Mechanics:**
- Token price: Fixed at $1.00
- Token supply: Grows/shrinks daily with NAV changes
- Yield distribution: Automatic balance increases

**Token Price Formula:**
```
Price = Always $1.00
Supply = Total NAV (in dollar terms)
Individual balance = % of supply
```

**Off-Chain Operations:**
```
Asset Management (Traditional):
├─ Same as non-rebasing
├─ Full bond fund operations
├─ Calculate daily NAV
└─ Send NAV to oracle

No operational difference from non-rebasing
```

**On-Chain Mechanics:**
```solidity
contract RebasingToken {
    // Internal: track "shares" not tokens
    mapping(address => uint256) private _shares;
    uint256 private _totalShares;
    uint256 public totalAssets;
    
    // External: users see "tokens"
    function balanceOf(address account) public view returns (uint256) {
        // Balance = user's % of shares × total assets
        return _shares[account] * totalAssets / _totalShares;
    }
    
    function totalSupply() public view returns (uint256) {
        return totalAssets;  // Supply = NAV in dollar terms
    }
    
    // Price always $1
    function pricePerToken() public pure returns (uint256) {
        return 1e18;  // $1.00
    }
    
    // Update NAV triggers rebase
    function updateNAV(uint256 newNAV) external {
        totalAssets = newNAV;
        // Everyone's balance recalculates automatically
    }
}
```

**Example Flow:**
```
Day 0: Launch
├─ Raise $10M, buy bonds
├─ Issue "shares" representing $10M
└─ Investor sees: 100,000 tokens at $1 = $100,000

Day 30: Positive Return
├─ Off-chain: Portfolio up 2%
├─ Off-chain: NAV = $10.2M
├─ On-chain: Oracle updates totalAssets
└─ Investor sees: 102,000 tokens at $1 = $102,000
    └─ Balance increased from 100k to 102k!

Day 60: Negative Return
├─ Off-chain: Portfolio down 1.5%
├─ Off-chain: NAV = $10.047M
├─ On-chain: Oracle updates
└─ Investor sees: 100,470 tokens at $1 = $100,470
    └─ Balance decreased from 102k to 100,470!

Investor Reaction:
- Positive rebase: "Cool, got more tokens!"
- Negative rebase: "Wait, my tokens disappeared!"
```

**The Problem with Duration Risk:**

```
Bond Fund with Rate/Credit Risk:

Good Period:
├─ Bonds rally 3%
├─ Rebase up 3%
├─ Balance: 100,000 → 103,000 tokens
└─ Investor happy

Bad Period:
├─ Rates spike, bonds fall 4%
├─ Rebase DOWN 4%
├─ Balance: 103,000 → 98,880 tokens
└─ Investor: "Did I get robbed?"

The Psychology Problem:
- Seeing tokens appear = feels like bonus
- Seeing tokens disappear = feels like theft
- Even though $ value same as non-rebasing!
- Terrible UX for volatile assets
```

**When Rebasing Works:**

✅ **Only suitable for:**
- Assets that only go up (never down)
- Staking rewards (always positive)
- Lending yields (always positive)
- Money market funds (minimal downside)

❌ **Not suitable for:**
- Bond funds with duration risk
- Credit strategies (spreads widen)
- Any mark-to-market volatility
- Institutional investors (accounting nightmare)

**Real-World Example: Aave aTokens**

```
Aave aUSDC (Rebasing):
├─ Deposit USDC into Aave
├─ Receive aUSDC at 1:1
├─ Yield accrues from lending
├─ Balance grows: 10,000 → 10,050 → 10,100
├─ Price stays $1
└─ Works because: never negative rebase
    └─ Borrowers always pay interest
```

**DeFi Incompatibility:**

```
Problem with AMMs:

Uniswap Pool: TOKEN/USDC
├─ Deposit: 10,000 tokens + $10,000 USDC
├─ Next rebase: token balance changes
│   └─ Pool now has 10,100 tokens + $10,000 USDC
│   └─ Price ratio broken
│   └─ Arbitrage opportunity (value leak)
└─ Most AMMs don't support rebasing

Problem with Lending:
├─ Deposit rebasing token as collateral
├─ If negative rebase: collateral shrinks
├─ Unexpected liquidations
├─ Risk models break
└─ Most protocols avoid rebasing
```

**Evaluation for Bond Funds:**

| Factor | Score | Notes |
|--------|-------|-------|
| **Duration Compatibility** | ⭐ | Negative rebase = bad UX |
| **Credit Strategy Compatibility** | ⭐ | Spread widening = rebase down |
| **Investor Familiarity** | ⭐⭐⭐⭐⭐ | "Balance grows" exciting |
| **DeFi Integration** | ⭐ | Most protocols incompatible |
| **Operational Complexity** | ⭐⭐⭐ | More complex than non-rebasing |
| **Regulatory Precedent** | ⭐⭐ | Limited examples |

**Verdict:** Not recommended for bond funds with mark-to-market risk. Only suitable if yield always positive (e.g., floating rate notes with no duration risk).

---

### 3.4 Structure 3: LP Token (Fractionalized Fund Interest)

**Concept:** Token represents % ownership of a pool, like hedge fund LP interest or Uniswap LP.

**Why "Fractionalized"?**

The term "fractionalized" means the fund interest is divided into small, fungible pieces:

```
Traditional Hedge Fund:
├─ Limited Partner invests $1M
├─ Gets "LP Interest" = legal ownership in partnership
├─ Represented by: Legal documents, K-1 tax forms
├─ Transfer: Requires legal paperwork, GP approval
└─ NOT divisible - can't sell 10% of your interest easily

Fractionalized (Tokenized):
├─ Limited Partner invests $1M
├─ Gets 100,000 LP tokens = same ownership
├─ Represented by: Tokens in wallet (fungible)
├─ Transfer: Send tokens peer-to-peer instantly
└─ DIVISIBLE - can sell 10k tokens, keep 90k tokens

"Fractionalized" = Broken into small, tradeable pieces
```

**The Key Insight:**

```
Traditional LP Interest:
└─ "You own 1% of the fund"
    └─ Indivisible legal interest
    └─ Must transfer whole position
    └─ Illiquid

Fractionalized LP Interest:
└─ "You own 100,000 tokens out of 10M total = 1%"
    └─ Can sell any amount (1 token, 1,000, 10,000)
    └─ Each token tradeable separately
    └─ More liquid

Example:
├─ Traditional: Can't sell "0.1% of my LP interest"
├─ Fractionalized: Can sell 10,000 tokens (0.1%)
└─ Tokens are "fractions" of total fund interest
```

**Real-World Analogy:**

```
Real Estate Ownership:

Non-Fractionalized:
├─ Own 10% of a building
├─ Legal partnership interest
├─ Want to sell 2%? → Complex legal process
└─ Want to sell 0.01%? → Impossible

Fractionalized (REIT or Token):
├─ Own 10,000 shares representing 10% of building
├─ Want to sell 2%? → Sell 2,000 shares
├─ Want to sell 0.01%? → Sell 10 shares
└─ Each share is a "fraction" of total ownership

Same concept applies to fund interests!
```

**Mechanics:**
- Token price: Pool value / Token supply
- Token supply: Algorithmically adjusted
- Ownership: % dilutes as new investors enter

**Token Price Formula:**
```
Price = Pool Value / Token Supply
Your value = Your tokens × Price
Your % = Your tokens / Total supply
```

**Off-Chain Operations:**
```
Asset Management (Hedge Fund Style):
├─ Active bond strategies
├─ Can use leverage (repo, derivatives)
├─ Concentrated positions allowed
├─ Performance fee structure
└─ Quarterly redemptions (not daily)

More flexibility than mutual fund structure
```

**On-Chain Mechanics:**
```solidity
contract LPToken {
    uint256 public poolValue;  // Total NAV
    uint256 public highWaterMark;
    uint256 public performanceFee = 20;  // 20%
    
    function pricePerToken() public view returns (uint256) {
        if (totalSupply() == 0) return 1e18;
        return poolValue * 1e18 / totalSupply();
    }
    
    function updatePoolValue(uint256 newValue) external {
        poolValue = newValue;
    }
    
    // Continuous deposits
    function deposit(uint256 amount) external returns (uint256) {
        uint256 tokens;
        if (totalSupply() == 0) {
            tokens = amount;  // First investor
        } else {
            tokens = amount * totalSupply() / poolValue;
        }
        _mint(msg.sender, tokens);
        poolValue += amount;
        return tokens;
    }
    
    // Quarterly redemptions
    function requestRedemption(uint256 tokens) external {
        // Add to queue, process quarterly
    }
    
    // Performance fees
    function chargePerformanceFee() external {
        uint256 currentPrice = pricePerToken();
        if (currentPrice > highWaterMark) {
            uint256 profit = (currentPrice - highWaterMark) * totalSupply();
            uint256 fee = profit * performanceFee / 100;
            
            // Mint fee tokens to manager
            _mint(manager, fee / currentPrice);
            
            highWaterMark = currentPrice;
        }
    }
}
```

**Example Flow:**
```
Quarter 1: Launch
├─ 10 investors × $1M = $10M
├─ Issue 1M LP tokens (arbitrary number)
├─ Price: $10.00
└─ Each investor: 100k tokens = 10% of fund

Quarter 2: Strong Performance
├─ Off-chain: Portfolio +15%
├─ Off-chain: Pool value = $11.5M
├─ Token price: $11.50
├─ Original investor: Still 100k tokens = 10%
└─ Value: $1.15M (15% return)

Quarter 3: New Investor Enters
├─ New investor: wants to invest $5M
├─ Current price: $11.50
├─ Gets: $5M / $11.50 = 434,783 tokens
│
├─ New pool value: $16.5M
├─ New supply: 1,434,783 tokens
├─ New price: still $11.50
│
├─ Original investor:
│   ├─ Tokens: 100k (unchanged)
│   ├─ Value: $1.15M (unchanged)
│   ├─ % ownership: 10% → 6.97% (DILUTED)
│   └─ But $ value protected
│
└─ This is the LP mechanic:
    - % ownership goes down
    - But value stays same
    - New capital doesn't dilute value

Quarter 4: Poor Performance + Redemption
├─ Off-chain: Portfolio -8%
├─ Pool value: $16.5M → $15.18M
├─ Token price: $11.50 → $10.58
│
├─ New investor redeems:
│   ├─ Burns 434,783 tokens
│   ├─ Gets $4.6M (8% loss)
│   └─ Exits
│
├─ Pool value: $15.18M → $10.58M
├─ Supply: 1,434,783 → 1,000,000
├─ Price: still $10.58
│
└─ Original investor:
    ├─ Still has 100k tokens
    ├─ Value: $1.058M
    ├─ % ownership: 6.97% → 10% (back to original %)
    └─ Shared the 8% loss proportionally
```

**The Dilution Dynamics:**

```
Unique Feature of LP Tokens:

Your % ownership changes constantly:
├─ New investors → % down (but $ protected)
├─ Redemptions → % up (but no $ windfall)
├─ Performance shared proportionally
└─ Like traditional hedge fund LP interests

Example:
Year 1: You own 10% of $10M = $1M
Year 2: Fund doubles, new $10M invested
        You own 5% of $30M = $1.5M
        ↑ % down, but value up (from performance)
```

**Performance Fee Implementation:**

```
Quarter 1: Launch at $10.00
Quarter 2: Rise to $12.00
├─ Above high-water mark by $2.00
├─ Performance fee: 20% × $2.00 = $0.40 per token
├─ Manager mints tokens to self
└─ New HWM: $12.00

Quarter 3: Fall to $11.00
├─ Below HWM
├─ No performance fee
└─ HWM: still $12.00

Quarter 4: Rise to $13.00
├─ Above HWM by $1.00
├─ Performance fee: 20% × $1.00 = $0.20 per token
└─ Must recover losses before earning fees again
```

**Application to Bond Funds:**

✅ **Suitable for:**
- Hedge fund-style fixed income strategies
- Absolute return bond funds
- Levered bond strategies
- Opportunistic credit
- Distressed debt
- Private credit

**CRITICAL DISTINCTION: Lending vs. Bond Fund Management**

```
LENDING PROTOCOLS (What Exists):
Example: Maple Finance
├─ Lenders deposit USDC into pools
├─ Pool Delegate originates loans
├─ Loans to crypto-native companies
├─ Hold loans to maturity
├─ Direct relationship with borrowers
└─ LP tokens represent pool ownership

This is NOT traditional bond fund management:
❌ Not buying bonds in secondary market
❌ Not trading based on rates/spreads
❌ Not marking positions to market daily
❌ Different risk profile (illiquid loans)

TRADITIONAL BOND FUND (What Could Be Tokenized):
Example: Your IG Corporate Fund
├─ Investors deposit cash
├─ Portfolio manager buys corporate bonds
├─ Trade bonds via Bloomberg/dealers
├─ Mark to market daily
├─ Active trading based on views
└─ LP tokens represent fund ownership

This IS traditional asset management:
✓ Secondary market trading
✓ Daily mark-to-market
✓ Active portfolio management
✓ Duration/spread positioning

NO TRUE TOKENIZED BOND FUNDS EXIST YET!
├─ Treasuries: Yes (Ondo, MatrixDock)
├─ Money Markets: Yes (BENJI)
├─ Corporate Bonds: No (frontier!)
└─ Opportunity for traditional credit managers
```

**Example Strategy:**

```
Opportunistic Credit Fund (LP Token):

Structure:
├─ Quarterly liquidity (not daily)
├─ Can use 2x leverage
├─ Concentrated positions (top 10 = 50%)
├─ Performance fee: 20% over 5% hurdle
└─ Manager co-invests 10%

Month 1: Opportunity
├─ Forced seller in auto ABS
├─ Trade: Buy $5M (20% of fund)
├─ Position: 3x normal size
└─ Conviction: High

Month 3: Position Exits
├─ ABS recovers to fair value
├─ P&L: +$750k (+30% on position)
├─ Fund: +3%
└─ Performance fee earned

Why LP Token Works:
├─ Quarterly redemptions allow illiquidity
├─ Performance fee aligns incentives
├─ Flexible capital base
└─ Manager can take concentrated bets
```

**Real-World Analogs:**

```
Traditional Hedge Fund:
├─ Limited partners invest capital
├─ Get partnership % (not shares)
├─ New LPs dilute % but not value
├─ Quarterly/annual redemptions
├─ Performance fees on profits
└─ Manager heavily invested

LP Token:
├─ Token holders invest capital
├─ Get tokens representing %
├─ New investors dilute % but not value
├─ Quarterly redemptions
├─ Performance fees via token minting
└─ Manager holds tokens

IMPORTANT: Existing "LP Token" Examples:
├─ Maple Finance (lending protocol):
│   ├─ Uses LP token structure ✓
│   ├─ BUT: Originating loans, not buying bonds
│   ├─ Pool Delegates = credit managers
│   └─ Different from secondary market bond trading
│
├─ Goldfinch (lending protocol):
│   ├─ Uses LP token structure ✓
│   ├─ BUT: Direct lending to companies
│   └─ Not trading corporate bonds
│
├─ Uniswap / Meteora (DEX liquidity):
│   ├─ Uses LP token structure ✓
│   └─ For providing liquidity, not fund management
│
└─ True tokenized credit FUND (buying/selling bonds):
    └─ Does not exist yet!
    └─ This is where opportunity lies

If Managing Traditional Credit Fund:
├─ Off-chain: Buy corporate bonds via dealers
├─ Off-chain: Trade based on credit views
├─ Off-chain: Mark to market daily
├─ On-chain: LP token tracks pool value
├─ Structure: Like hedge fund LP interest
└─ Example: Your absolute return bond fund could use this
```

**DeFi Composability:**

LP tokens work well in DeFi:

```
Lending as Collateral:
├─ Deposit LP tokens
├─ Borrow against NAV
├─ Works (fixed supply between subs/redeems)
└─ Similar to non-rebasing

AMM Pools:
├─ Create TOKEN/USDC pool
├─ Price discovery works
├─ Supply changes only quarterly
└─ Acceptable for DeFi
```

**Evaluation for Bond Funds:**

| Factor | Score | Notes |
|--------|-------|-------|
| **Duration Compatibility** | ⭐⭐⭐⭐⭐ | Any duration works |
| **Credit Strategy Compatibility** | ⭐⭐⭐⭐⭐ | Perfect for opportunistic |
| **Investor Familiarity** | ⭐⭐⭐ | Sophisticated investors only |
| **DeFi Integration** | ⭐⭐⭐⭐ | Good (predictable supply) |
| **Operational Complexity** | ⭐⭐⭐⭐ | Quarterly vs daily easier |
| **Regulatory Precedent** | ⭐⭐⭐ | Hedge fund model familiar |

**Verdict:** Excellent for sophisticated strategies and institutional investors who understand hedge fund structures. Less suitable for retail or traditional bond mutual fund replication.

---

## 4. COMPARATIVE ANALYSIS

### 4.1 Summary Matrix

| Feature | NAV-Backed | Yield Non-Rebasing | Yield Rebasing | LP Token |
|---------|------------|-------------------|----------------|----------|
| **Token Price** | Target $1 | Floats | Fixed $1 | Pool/Supply |
| **Supply Dynamics** | Floats | Fixed* | Grows | Algorithmic |
| **Yield Mechanism** | Distributions | Accrual | Balance growth | Accrual |
| **Duration Risk** | ❌ Incompatible | ✅ Works perfectly | ⚠️ Bad UX | ✅ Works perfectly |
| **Credit Risk** | ⚠️ Limited | ✅ Works perfectly | ⚠️ Bad UX | ✅ Works perfectly |
| **DeFi Collateral** | ❌ Poor | ✅ Excellent | ❌ Incompatible | ✅ Good |
| **AMM Trading** | ❌ No | ✅ Yes | ❌ No | ✅ Yes |
| **Investor UX** | Familiar | Must track price | Balance grows | % ownership |
| **Gas Efficiency** | Medium | High | Low | Medium |
| **Regulatory** | Strong | Growing | Limited | Medium |

*Except subscriptions/redemptions

### 4.2 Strategy Suitability

**Money Market / Ultra-Short (<1yr duration):**
- **Best:** NAV-Backed or Yield Non-Rebasing
- **Why:** Can maintain stable NAV or minimal volatility
- **Example:** Franklin BENJI uses NAV-backed

**Investment Grade Corporate (3-7yr duration):**
- **Best:** Yield Non-Rebasing
- **Why:** Duration risk needs mark-to-market
- **Avoid:** NAV-backed (can't maintain $1), Rebasing (bad UX)

**High Yield / Emerging Markets:**
- **Best:** Yield Non-Rebasing or LP Token
- **Why:** Credit volatility needs price discovery
- **Avoid:** NAV-backed, Rebasing

**Absolute Return / Hedge Fund Style:**
- **Best:** LP Token or Yield Non-Rebasing
- **Why:** Performance fees, flexible redemptions
- **Consider:** LP token if quarterly liquidity acceptable

**Private Credit / Illiquid:**
- **Best:** LP Token
- **Why:** Quarterly redemptions natural, performance fees
- **Why not others:** Daily NAV difficult for illiquid securities

### 4.3 Investor Profile Matching

**Traditional Institutional (Pension, Insurance):**
```
Preferences:
├─ Familiar accounting treatment
├─ Daily NAV transparency
├─ Regulatory comfort
└─ Mark-to-market expectations

Recommendation: Yield Non-Rebasing
├─ Looks exactly like traditional bond fund
├─ Clean capital gains treatment
├─ Institutional custody compatible
└─ Can get regulatory comfort
```

**Crypto-Native Institutional (Crypto Funds, DAOs):**
```
Preferences:
├─ DeFi composability
├─ Collateral capability
├─ AMM liquidity
└─ 24/7 trading

Recommendation: Yield Non-Rebasing
├─ Best DeFi integration
├─ Works as collateral
├─ AMM pools function
└─ Familiar to crypto users
```

**Sophisticated / Hedge Fund Investors:**
```
Preferences:
├─ Performance fee alignment
├─ Concentrated strategies
├─ Manager co-investment
└─ Quarterly liquidity acceptable

Recommendation: LP Token
├─ Performance fee structure native
├─ Flexible strategy freedom
├─ Natural quarterly redemptions
└─ Manager holds tokens
```

**Retail (If Permitted):**
```
Preferences:
├─ Simple UX
├─ Familiar concepts
├─ Daily liquidity
└─ Avoid complexity

Recommendations:
├─ Money Market: NAV-Backed (familiar $1 = 1 share)
├─ Bond Fund: Yield Rebasing (balance growth exciting)
└─ Avoid: LP Token (dilution confusing)
```

---

## 5. IMPLEMENTATION CONSIDERATIONS

### 5.1 Technology Requirements

**Minimal Technology Stack:**

```
OFF-CHAIN (Traditional):
├─ Portfolio management system
│   └─ Bloomberg Terminal, broker relationships
├─ Custody account
│   └─ Qualified custodian bank
├─ NAV calculation
│   └─ Administrator or in-house
└─ No changes from traditional operations

ON-CHAIN (New):
├─ Smart contract (~200 lines code)
│   └─ OpenZeppelin templates available
├─ Oracle mechanism
│   └─ Custom API or Chainlink
├─ Node provider
│   └─ Alchemy, Infura ($100/month)
└─ Gas wallet
    └─ ETH for daily updates (~$50/day)

Total new infrastructure cost: <$5k setup, <$3k/month
```

### 5.2 Oracle Design

**Three Options:**

```
Option A: Centralized (Lowest Cost)
├─ Your server posts NAV directly
├─ Cost: Just gas fees (~$50/update)
├─ Trust: Relies on your honesty
└─ Timeline: 1 day to implement

Option B: Decentralized (Most Trust)
├─ Chainlink or API3
├─ Cost: $100-500/update
├─ Trust: Multiple validators
└─ Timeline: 1 week to implement

Option C: Hybrid
├─ Your calculation + multiple verifiers
├─ Cost: Medium
├─ Trust: Good balance
└─ Timeline: 2 weeks to implement

For MVP: Start with Option A
For institutional scale: Upgrade to Option B
```

### 5.3 Regulatory Pathways

**Three Jurisdictions:**

```
United States:
├─ Reg D Private Placement (unregistered)
│   ├─ Accredited investors only
│   ├─ No SEC registration
│   └─ Fast (4-8 weeks)
│
├─ '40 Act Registered (like BENJI)
│   ├─ Full SEC registration
│   ├─ Can market publicly
│   └─ Slow (6-12 months)
│
└─ Recommendation: Reg D for launch

Cayman Islands:
├─ Exempted Limited Partnership
├─ Light regulation
├─ Fast approval (4-6 weeks)
└─ Popular for hedge funds

Switzerland:
├─ FINMA approval required
├─ Higher regulatory standards
├─ Slower (4-6 months)
└─ Premium positioning
```

### 5.4 Cost Structure

**Year 1 Estimate ($10M AUM):**

```
Setup Costs (One-Time):
├─ Legal: $30-100k
├─ Smart contract development: $10-30k
├─ Audit (optional): $20-50k
└─ Total: $60-180k

Annual Operating:
├─ Custody fees: 10-20bps = $10-20k
├─ Administrator: $50-100k (or in-house)
├─ Legal/compliance: $50k
├─ Audit: $50-100k
├─ Gas fees: $18k (~$50/day)
├─ Infrastructure: $36k
└─ Total: $214-324k

As % of AUM: 2.14-3.24%

At $100M AUM:
├─ Fixed costs: ~$250k
├─ Variable (custody/admin): ~150bps = $1.5M
└─ Total: $1.75M = 1.75%

Scale economies significant!
```

---

## 6. CASE STUDIES

### 6.1 Franklin BENJI - NAV-Backed Implementation

**Structure Overview:**
```
Fund: Franklin OnChain U.S. Government Money Fund (FOBXX)
AUM: ~$844M (as of late 2024)
Launch: April 2021
Blockchain: Stellar (primary), Polygon (secondary)
```

**Off-Chain Layer:**
```
Asset Management:
├─ Investment Strategy:
│   ├─ 99.5%+ U.S. government securities
│   ├─ T-bills (1-3 months maturity)
│   ├─ Overnight repos
│   └─ Federal agency securities
│
├─ Duration: ~30 days WAM
├─ Objective: Stable $1.00 NAV + yield
├─ Yield: ~5% (as of late 2024)
│
├─ Management:
│   └─ Franklin Templeton PM team
│   └─ Traditional money market operations
│   └─ Bloomberg Terminal, dealer relationships
│
└─ Custody:
    └─ Traditional bank custody
    └─ Not "on blockchain" - in bank accounts
```

**On-Chain Layer:**
```
Token Mechanics:
├─ 1 BENJI = 1 fund share
├─ Target price: $1.00
├─ Daily yield distributions:
│   └─ Yield paid as more BENJI tokens
│   └─ Like automatic dividend reinvestment
│   └─ Maintains $1 price
│
├─ Oracle:
│   └─ Franklin's administrator calculates NAV
│   └─ Posted daily at 4pm ET
│   └─ Centralized (Franklin controlled)
│
└─ Compliance:
    ├─ KYC/AML required
    ├─ Accredited investor restrictions
    ├─ Transfer restrictions enforced on-chain
    └─ Can revoke if compliance issue
```

**Why Stellar Blockchain:**
```
Technical Choices:
├─ Low transaction fees (<$0.01)
├─ Fast finality (5 seconds)
├─ Built-in asset tokenization features
├─ Compliance features native
└─ Lower gas costs than Ethereum
```

**Daily Operations:**
```
4:00pm ET - Franklin calculates NAV
4:05pm ET - Post to blockchain
4:06pm ET - Smart contract rebases tokens
4:10pm ET - Investor wallets update

Result: Minimal operational burden
        └─ 5 minutes of blockchain activity
        └─ Rest is traditional operations
```

**Why It Works:**
```
Money Market = Stable NAV:
├─ Ultra-short duration (minimal rate risk)
├─ High-quality securities (minimal credit risk)
├─ Can credibly maintain $1.00
└─ NAV-backed structure appropriate

If Franklin tried this with 5-year bonds:
├─ Rates rise 100bps
├─ Bonds fall 5%
├─ Cannot maintain $1 → structure breaks
└─ NAV-backed only works for money markets
```

**Lessons:**
- NAV-backed viable for ultra-short/money market
- Operational burden minimal (just oracle)
- Institutional asset manager can tokenize
- Regulatory path exists ('40 Act)
- But: Structure limited to stable-NAV products

---

### 6.2 Ondo OUSG - Yield-Bearing Implementation

**Structure Overview:**
```
Fund: Ondo US Government Securities (OUSG)
AUM: ~$200M+ (as of late 2024)
Launch: January 2023
Blockchain: Ethereum (primary), multi-chain via bridges
```

**Off-Chain Layer (Three Levels):**
```
Level 1: Physical Treasuries
├─ Actual U.S. T-bills
├─ Custodied at BNY Mellon
├─ 3-6 month maturity
└─ This is the actual asset

Level 2: BlackRock BUIDL
├─ BlackRock's tokenized treasury fund
├─ Holds the T-bills from Level 1
├─ Minimum: $5M investment
└─ Monthly liquidity

Level 3: Ondo Wrapper (OUSG)
├─ Ondo I LP structure
├─ Invests 99.5%+ in BUIDL
├─ Adds:
│   ├─ Lower minimum ($5k instant)
│   ├─ Daily liquidity (instant redemptions)
│   └─ Better retail access
└─ This is what investors buy

Management:
├─ Ondo does NOT manage treasuries
├─ BlackRock handles securities
├─ Ondo provides wrapper + distribution
└─ Minimal operational burden for Ondo
```

**On-Chain Layer:**
```
Token Mechanics:
├─ Non-Rebasing structure
├─ Starting price: ~$100
├─ Price appreciation: ~$0.014/day (~5% APY)
├─ No distributions ever
├─ All yield → price appreciation
│
├─ Token Supply:
│   ├─ Fixed between subscriptions/redemptions
│   ├─ No daily rebasing
│   └─ DeFi-friendly (predictable)
│
└─ Dual Versions:
    ├─ OUSG (non-rebasing, price grows)
    └─ rOUSG (rebasing, stays at $1)
        └─ Can convert between them freely

Oracle:
├─ Ondo calculates NAV daily
├─ Based on BUIDL NAV + fees
├─ Posts to Ethereum
└─ Both OUSG and rOUSG update

Compliance:
├─ 40-day minimum hold before transfer
├─ Accredited investor only
├─ KYC/AML required
└─ Whitelist enforced
```

**DeFi Integration Strategy:**
```
Why Non-Rebasing Chosen:

Aave/Compound Collateral:
├─ Fixed supply → predictable value
├─ Price updates smoothly
├─ Can calculate loan-to-value easily
└─ Proposal submitted (under review)

Uniswap AMM:
├─ Create OUSG/USDC pool
├─ Price discovery works
├─ No rebase complications
└─ Already deployed

Yield Aggregators:
├─ Yearn, Beefy, etc.
├─ Can farm with OUSG
├─ Strategies work normally
└─ Growing adoption

Why not rebasing:
❌ Would break most DeFi protocols
❌ AMMs don't handle balance changes
❌ Lending protocols risk models break
└─ Non-rebasing = DeFi compatible
```

**Daily Operations:**
```
Morning (BlackRock):
├─ BUIDL marks T-bills to market
├─ Calculate BUIDL NAV
└─ Publish (very stable, near $1)

Afternoon (Ondo):
├─ Value Ondo I LP holdings
│   └─ 99.5% in BUIDL (use BUIDL NAV)
│   └─ 0.5% cash
├─ Calculate OUSG NAV
│   └─ Yesterday: $105.42
│   └─ Today: $105.56 (+$0.14)
└─ Post to blockchain

On-Chain (Automated):
├─ Oracle transaction ($50-200 gas)
├─ Contract updates NAV
├─ OUSG price: $105.56
├─ rOUSG balances increase proportionally
└─ All holders see update in 1 block
```

**Why It Works:**
```
Low Duration = Predictable Yield:
├─ T-bills: minimal rate risk
├─ Government: no credit risk
├─ Yield: stable ~5%
├─ Price: smooth appreciation
└─ Institutional investors comfortable

Plus DeFi Composability:
├─ Can be collateral
├─ Can trade in AMMs
├─ Can use in strategies
└─ This is the key differentiator
```

**Compared to BENJI:**
```
BENJI:
├─ NAV-backed (rebasing to $1)
├─ Traditional '40 Act fund
├─ Stellar blockchain (lower costs)
├─ Retail friendly ($100 minimum)
└─ Poor DeFi integration

OUSG:
├─ Yield-bearing (price appreciation)
├─ Private placement (Reg D)
├─ Ethereum (DeFi ecosystem)
├─ Institutional minimum ($5k-100k)
└─ Excellent DeFi integration

Different choices for different goals!
```

**Lessons:**
- Yield-bearing works for any duration
- Can be DeFi collateral (huge advantage)
- Minimal operations (BlackRock does heavy lifting)
- Wrapper model scalable
- Non-rebasing preferred by institutions

---

## 7. CONCLUSIONS

### 7.1 Key Findings

**1. Asset Management Remains Off-Chain**

The most important finding: tokenization does NOT require on-chain asset management. Every successful implementation reviewed maintains traditional operations:
- Securities held in traditional custody
- Trading via traditional markets and brokers
- NAV calculated using traditional methods
- Portfolio management unchanged

**2. Token Structure Selection Depends on Rate & Credit Risk**

```
Decision Tree:

Ultra-short duration (<1yr), minimal credit risk, stable NAV?
└─ NAV-Backed or Yield Non-Rebasing
    └─ ONLY if NAV truly stays near $1
    └─ Money market funds only in practice

ANY duration risk (>1yr) OR credit risk?
└─ Yield Non-Rebasing (recommended)
└─ NOT NAV-backed → Will de-peg, defeats purpose
└─ NOT Rebasing → Negative rebase terrible UX

Key Reality:
├─ NO bond funds with duration/credit risk use NAV-backed
├─ De-pegging defeats the entire purpose of the structure  
├─ Only money markets can maintain $1 peg
└─ All other bond strategies use yield-bearing or LP token

Why De-Pegging Defeats Purpose:
├─ Point of NAV-backed: stable $1 price (stablecoin-like)
├─ If price drops to $0.95: just a regular volatile token
├─ Lost the benefit, kept the complexity
└─ Should have used yield-bearing from start

Hedge fund style, performance fees?
└─ LP Token

DeFi integration critical?
└─ Yield Non-Rebasing or LP Token
└─ NOT NAV-backed (floating supply)
└─ NOT Rebasing (protocol incompatibility)
```

**3. All Three Structures CAN Work for Bond Funds (But With Major Caveats)**

| Structure | Money Market | IG Corporate | High Yield | Hedge Fund |
|-----------|--------------|--------------|------------|------------|
| **NAV-Backed** | ✅ Ideal | ❌ No* | ❌ No* | ❌ No* |
| **Yield Non-Rebasing** | ✅ Good | ✅ Ideal | ✅ Ideal | ✅ Good |
| **Yield Rebasing** | ✅ Good | ⚠️ Poor UX | ⚠️ Poor UX | ❌ No |
| **LP Token** | ⚠️ Unusual | ✅ Good | ✅ Good | ✅ Ideal |

*No examples exist in practice. De-pegging defeats purpose.

**Important Clarification:**

```
NAV-Backed "CAN" work technically for bond funds:
├─ Smart contract can track any NAV
├─ Code doesn't break if NAV fluctuates
└─ Technically possible

But it DEFEATS THE PURPOSE:
├─ Entire point: maintain $1 price stability
├─ With duration/credit risk: price will de-peg
├─ De-pegged NAV-backed = worse version of yield-bearing
├─ More complexity, same volatility, no benefits
└─ This is why NOBODY does it

Rate Risk Example:
├─ Fed hikes 100bps
├─ 5yr bonds fall 5%  
├─ NAV per token: $1.00 → $0.95
├─ You now have a volatile token priced at $0.95
├─ Same as yield-bearing non-rebasing at $0.95
└─ But you added distribution complexity for nothing

Credit Risk Example:
├─ Credit spreads widen 50bps
├─ HY bonds fall 2%
├─ NAV per token: $1.00 → $0.98
├─ De-pegged, volatile, complex
└─ Why not just use yield-bearing?

Reality: 
NAV-backed technically works but economically pointless 
for anything with rate/credit risk.
```

**4. Operational Burden is Minimal**

The incremental work required:
- Deploy smart contract (one-time, <$50k)
- Daily oracle update (automated, <5 minutes)
- Gas fees (~$50/day on Ethereum)
- Everything else identical to traditional fund

**5. DeFi Integration Requires Fixed Supply**

For collateral use in Aave, Compound, etc.:
- ✅ Yield Non-Rebasing: Works perfectly
- ✅ LP Token: Works well
- ❌ NAV-Backed: Supply changes break protocols
- ❌ Yield Rebasing: Balance changes break protocols

### 7.2 Recommendations by Fund Type

**Critical Observation: What Actually Exists in Market**

```
TOKENIZED FIXED INCOME IN PRACTICE (2024-2025):

Money Market Funds:
├─ Franklin BENJI: NAV-Backed ✓
├─ Backed MMF: NAV-Backed ✓
└─ Structure: Works (ultra-short, gov't only)

Treasury Funds:
├─ Ondo OUSG: Yield Non-Rebasing ✓
├─ MatrixDock: Yield Non-Rebasing ✓  
├─ OpenEden: Yield Non-Rebasing ✓
└─ Structure: ALL use yield-bearing, NONE use NAV-backed

Lending Protocols (NOT bond funds):
├─ Maple Finance: LP Token ✓
│   └─ Direct loan origination, not bond trading
├─ Goldfinch: LP Token ✓
│   └─ Emerging market lending, not bond trading
└─ These originate loans, don't trade bonds

TRUE BOND FUNDS (buying/selling corporate bonds):
❌ NO examples exist yet!
├─ No tokenized IG corporate bond fund
├─ No tokenized high yield fund  
├─ No tokenized EM debt fund
└─ This is frontier territory!

Why the gap?
├─ Treasuries easy: government securities, liquid
├─ Money markets easy: ultra-short, stable NAV
├─ Corporate bonds harder: 
│   ├─ More complex credit analysis
│   ├─ Less liquid securities
│   ├─ OTC market structure
│   └─ No one has done it yet!

Opportunity:
└─ Traditional credit managers can be first movers
    └─ Tokenize traditional bond fund operations
    └─ Use yield-bearing or LP token structure
```

NO EXAMPLES of:
❌ NAV-backed IG corporate bond fund
❌ NAV-backed high yield fund  
❌ NAV-backed with >1yr duration
❌ NAV-backed with credit risk

Why? Because de-pegging defeats the purpose!
```

**Money Market Funds:**
```
Primary: NAV-Backed
├─ Familiar $1 = 1 share model
├─ Can credibly maintain $1 NAV
├─ Example: Franklin BENJI ($844M AUM)
├─ Regulatory precedent exists ('40 Act)
└─ WORKS because:
    ├─ Duration: <30 days (minimal rate risk)
    ├─ Credit: Government only (no spread risk)
    └─ Can actually maintain $1 peg

Alternative: Yield Non-Rebasing
├─ If DeFi integration desired
└─ Trades familiarity for composability

Why NAV-Backed Works Here:
├─ Fed raises rates 100bps:
│   └─ 30-day duration = -0.08% impact
│   └─ Price: $1.000 → $0.9992
│   └─ Stays near $1, peg maintained ✓
│
└─ This is ONLY case where NAV-backed works!
```

**Investment Grade Corporate Bond Funds:**
```
Primary: Yield Non-Rebasing
├─ Duration/credit risk flows through price
├─ Institutional investors understand mark-to-market
├─ DeFi collateral capability
├─ Example: Ondo OUSG model (treasuries, but same logic)
└─ Optimal for most cases

Not Recommended:
❌ NAV-Backed because:
   ├─ Rate risk: 4-5yr duration = 4-5% move per 100bps
   │   └─ Fed hikes → NAV drops to $0.96
   │   └─ De-pegging defeats purpose
   │
   ├─ Credit risk: IG spreads volatile (50-150bps moves)
   │   └─ Spread widening → NAV drops
   │   └─ Cannot maintain $1 peg
   │
   └─ Reality: Zero examples exist for good reason
   
❌ Rebasing because:
   └─ Negative rebase (tokens disappear) = terrible UX

Example: Why NAV-Backed Fails:
├─ 5yr IG Corporate fund, duration 4.5 years
├─ Moderate rate move: +50bps
├─ Portfolio impact: -2.25%
├─ NAV: $1.00 → $0.9775
├─ You wanted $1 peg, got $0.9775
├─ Point of NAV-backed structure lost!
└─ Should have used yield-bearing instead
```

**High Yield / Credit Strategies:**
```
Primary: Yield Non-Rebasing
├─ Credit volatility needs mark-to-market
├─ Price discovery important
└─ DeFi integration valuable

Alternative: LP Token
├─ If quarterly liquidity acceptable
├─ Performance fee structure desired
└─ Hedge fund-style positioning

IMPORTANT DISTINCTION:
Tokenized LENDING vs. Tokenized BOND FUNDS:

What EXISTS today (Lending Protocols):
├─ Maple Finance:
│   ├─ Lending platform, not bond fund
│   ├─ Pool Delegates originate loans to companies
│   ├─ Lenders provide capital to pools
│   ├─ NOT trading bonds in secondary market
│   └─ Direct origination model
│
├─ Goldfinch:
│   ├─ Lending protocol for emerging markets
│   ├─ Backers assess credit, invest first-loss
│   ├─ Senior pool provides senior capital
│   ├─ NOT buying/selling corporate bonds
│   └─ Direct lending to companies
│
└─ These are DIFFERENT from traditional credit fund:
    ├─ Traditional: Buy corporate bonds, trade secondary market
    ├─ Lending Protocol: Originate loans, hold to maturity
    └─ Different risk/liquidity profile

What DOESN'T exist yet:
❌ Tokenized IG corporate bond fund (buying bonds)
❌ Tokenized HY bond fund (trading secondary market)
❌ Tokenized EM debt fund (trading sovereigns/corps)
└─ True bond fund tokenization = frontier territory!

If you were tokenizing traditional bond fund:
├─ Buy bonds via Bloomberg/dealers (off-chain)
├─ Mark to market daily (off-chain)
├─ Trade in/out of positions (off-chain)
├─ Token tracks NAV (on-chain)
└─ Use: Yield-bearing non-rebasing structure
```

**Absolute Return / Hedge Fund Fixed Income:**
```
Primary: LP Token
├─ Performance fees native
├─ Quarterly redemptions natural
├─ Flexible strategy freedom
└─ Manager alignment via token holdings

Alternative: Yield Non-Rebasing
├─ If daily liquidity required
└─ If DeFi integration critical priority
```

### 7.3 Critical Success Factors

**For NAV-Backed:**
- ✅ ONLY use for ultra-short duration (<90 days)
- ✅ ONLY use for government/high-quality securities (no credit risk)
- ✅ Must be able to credibly maintain $1.00 NAV
- ✅ Good for retail marketing (familiar $1 = 1 share)
- ❌ Don't use for ANY duration risk (will de-peg)
- ❌ Don't use for ANY credit risk (spreads cause de-peg)
- ❌ If you can't maintain peg, structure defeats its own purpose

**The De-Pegging Problem:**
```
If NAV drops from $1.00 to $0.95:
├─ Lost the benefit (stable price)
├─ Kept the complexity (distributions)
├─ Same as yield-bearing at $0.95
└─ Why not use yield-bearing from start?

This is why no bond funds with duration/credit 
risk use NAV-backed structure in practice.
```

**For Yield Non-Rebasing:**
- ✅ Works for any bond strategy
- ✅ Prioritize DeFi integration
- ✅ Institutional investor comfort
- ✅ Clean capital gains treatment

**For Yield Rebasing:**
- ⚠️ Only if yield always positive
- ❌ Avoid for mark-to-market strategies
- ⚠️ Limited DeFi compatibility
- ✅ Retail likes balance growth

**For LP Token:**
- ✅ Best for sophisticated strategies
- ✅ Performance fee alignment
- ✅ Quarterly liquidity model
- ❌ Retail investors find confusing

### 7.4 Future Outlook

**Near-Term (2025-2026):**
- Continued growth of yield-bearing non-rebasing structures
- More traditional asset managers entering space
- Expansion beyond money markets into credit
- DeFi protocols adding RWA collateral support
- Multi-chain deployments becoming standard

**Medium-Term (2027-2028):**
- Regulatory clarity improving globally
- Institutional custody infrastructure maturing
- Cross-chain interoperability improving
- Traditional bond indices being tokenized
- Performance track records established

**Long-Term (2030+):**
- Tokenized bonds as DeFi primitive
- Native on-chain primary issuance
- Majority of new funds launching with tokens
- Traditional and crypto infrastructure converging
- RWAs representing significant DeFi TVL

---

## 8. APPENDICES

### Appendix A: Technical Resources

**Smart Contract Templates:**
- OpenZeppelin Contracts: https://docs.openzeppelin.com
- Ethereum Security Best Practices
- Solidity Documentation

**Oracle Solutions:**
- Chainlink: https://chain.link
- API3: https://api3.org
- Custom Implementation Guides

**Development Tools:**
- Hardhat (Ethereum development)
- Foundry (testing framework)
- Etherscan (contract verification)

### Appendix B: Regulatory Resources

**United States:**
- SEC Division of Investment Management
- Reg D / Rule 506 Guidance
- Investment Company Act of 1940

**International:**
- FINMA (Switzerland)
- CIMA (Cayman)
- MAS (Singapore)

### Appendix C: Service Providers

**Custody (Crypto-Friendly):**
- Sygnum Bank (Switzerland)
- Bank Frick (Liechtenstein)
- Fireblocks (technology)

**Legal (Token Securities):**
- Quarles & Brady
- Anderson Kill
- Lenz & Staehelin (Switzerland)

**Audit (Blockchain Experience):**
- Deloitte Digital Assets
- PwC Crypto Services
- Armanino

---

## ABOUT THIS RESEARCH

This analysis was conducted through comprehensive study of existing tokenized fund implementations, review of smart contract architectures, and examination of operational considerations for fixed income tokenization. The research demonstrates that tokenization primarily affects ownership tracking rather than asset management, making it accessible to traditional fund managers without requiring fundamental operational changes.

**Methodology:**
- Analysis of Franklin BENJI and Ondo OUSG implementations
- Smart contract architecture review
- Comparison of token economics across structures
- Evaluation of DeFi integration capabilities
- Assessment of regulatory pathways

**Scope:**
- Focus on U.S. dollar-denominated fixed income
- Public blockchain implementations
- Institutional-grade structures
- Regulatory-compliant approaches

**Limitations:**
- Rapidly evolving technology and regulations
- Limited operational history for some structures
- Regional regulatory variations not fully covered
- DeFi integration still maturing

---

*This research is provided for educational purposes. It does not constitute legal, financial, or investment advice. Readers should consult qualified professionals before implementing any tokenization strategy.*
