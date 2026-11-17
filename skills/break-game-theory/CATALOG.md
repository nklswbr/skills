# Exploit Catalog

Strategies that break app mechanics, grouped by what the mechanic does. Use the sections that apply to the players and rewards in your Phase 1 map. Each row is something real users do; the signal says what to look for in the code or rules; the fix is the usual mechanism change.

Use fake accounts, `example.com` emails, and test payment modes for every reproduction.

---

## Identity and multi-accounting

Every reward given per account is a reward given per identity-cost. Find out what a new account costs.

| Strategy | Who does it | Signal in code or rules | Fix pattern |
| --- | --- | --- | --- |
| Email aliases (`name+1@`, dots in Gmail, catch-all domains) | Anyone after a signup bonus | Uniqueness on raw email string | Normalize emails; but treat as a speed bump, not a wall |
| Disposable email services | Trial cyclers | No domain checks | Grant the valuable part later (on payment, on usage), not on signup |
| Same card, new account | Trial cyclers | Card fingerprint not checked across accounts | One trial per payment fingerprint |
| Self-referral | Credit farmers | Referrer and referee can be the same person | Pay referrer on referee's *payment* or real activation, not signup |
| Sybil accounts to vote, review, or rank | Brigades, sellers, creators | Votes weighted equally regardless of account age or history | Weight by account history; cap influence of new accounts |
| Account sharing (one seat, many people) | Teams, agencies, families | Pricing per seat, no concurrency check | Concurrent session limit, or price on a unit sharing doesn't break (workspace, usage) |
| Account selling (aged or high-reputation accounts) | Spammers, sellers | Reputation transfers with the account | Reputation decays without activity; re-verify on big changes |
| Smurfing (expert on a fresh account) | Competitive players | Matchmaking starts new accounts at average | Fast rating convergence; place by early performance |

## Rewards, credits, and referrals

| Strategy | Who does it | Signal in code or rules | Fix pattern |
| --- | --- | --- | --- |
| Farming signup credits | Multi-accounters | Credits granted at signup, worth more than identity cost | Smaller upfront, more on activation; credits that can't be transferred or resold |
| Converting free credits to cash or resale | Arbitrageurs | Credits buy something resellable (API calls, gift cards, compute) | Restrict what free credits buy; expire them |
| Hoarding then dumping | Rational savers | Credits never expire, rewards accrue indefinitely | Expiry or decay; caps on balance |
| Double redemption via concurrent requests | Script users | Check-then-write without lock or unique constraint | Unique constraint, idempotency key, transaction |
| Refund after reward | Anyone | Reward granted on purchase, not clawed back on refund | Vest rewards past the refund window; claw back on refund |
| Reward for action with no value (empty posts, junk uploads) | Reward farmers | Reward on count, not quality | Reward on downstream signal (someone else used it) |
| Milestone gaming (exactly hit the threshold, then stop) | Rational users | Step-function rewards | Smooth rewards, or rewards that require sustained behavior |

## Trials, pricing, and quotas

| Strategy | Who does it | Signal in code or rules | Fix pattern |
| --- | --- | --- | --- |
| Trial cycling | Price-sensitive users | Trial per account, cheap accounts | Trial per payment fingerprint or org domain; limit trial value, not length |
| Cancel to trigger win-back discount, resubscribe | Rational subscribers | Win-back offer to anyone who cancels | Offer once per customer lifetime; don't make cancellation the cheapest path to a discount |
| Downgrade timing (use annual features, downgrade before renewal) | Power users | Features stay after downgrade until period end with exports | Fine if intended; make sure data access matches plan |
| Plan arbitrage (buy the plan whose limit is easiest to stretch) | Teams | Limits on one axis, value on another | Limit on the axis that tracks value |
| Quota splitting across accounts | Heavy users | Per-account quota, cheap accounts | Per-org or per-payment quota |
| Burst at quota reset | Script users | Fixed windows reset at midnight | Sliding windows or token buckets |
| Exceeding quota via parallel requests | Script users | Quota checked before work, counted after | Reserve quota atomically before work |
| Resale of your API through a proxy | Arbitrageurs | Unmetered or flat-rate plan with programmatic access | Usage-based tier above a fair-use line; ToS plus detection |

## Reputation: reviews, ratings, karma

| Strategy | Who does it | Signal in code or rules | Fix pattern |
| --- | --- | --- | --- |
| Retaliation reviews | Sellers, hosts, buyers | One side sees the other's review before writing theirs | Simultaneous reveal (both publish together or after a window; Airbnb has done this since 2014) |
| Rating inflation (everyone gives 5 stars to avoid retaliation or awkwardness) | Everyone | Ratings all cluster at max | Relative or multi-dimensional ratings; private feedback channel |
| Review swaps and rings | Sellers, creators | Reviewers who only review each other | Graph detection; weight reviews by reviewer history |
| Review-for-reward | Sellers | Incentivized reviews look like organic ones | Disclose or disallow; weight verified purchases |
| Bury bad reviews with new listings | Sellers | Reputation tied to listing, not seller | Reputation on the seller |
| Karma farming in low-effort spaces | Status seekers | Karma from any upvote anywhere | Karma scoped to where it was earned |
| Mass-reporting to remove a competitor | Griefers, rivals | Auto-action after N reports | Weight reporters by accuracy history; human review before removal |

## Rankings, leaderboards, and feeds

| Strategy | Who does it | Signal in code or rules | Fix pattern |
| --- | --- | --- | --- |
| Vote brigading | Communities, fans | Raw vote counts, no velocity or source checks | Weight by voter diversity and history; dampen sudden coordinated bursts |
| Engagement bait (outrage, cliffhangers, "comment YES") | Creators | Ranking on raw engagement | Rank on downstream satisfaction (saves, return visits), not just reactions |
| Sandbagging (stay in a lower bracket to win it) | Competitive players | Brackets by self-reported or early stats | Brackets by recent performance |
| Time-of-day gaming (post when competition is low) | Creators | Ranking favors recency in absolute terms | Normalize for traffic; fine if harmless |
| Leaderboard squatting (one big early lead nobody can catch) | Early users | All-time leaderboards | Seasonal or rolling windows |
| Self-dealing in "trending" | Platform insiders, big accounts | Trending based on a few big accounts' actions | Count distinct actors, not volume |

## Marketplaces and two-sided products

| Strategy | Who does it | Signal in code or rules | Fix pattern |
| --- | --- | --- | --- |
| Take it off-platform after the first match | Buyers and sellers | Fee per transaction, contact info shareable | Make the platform worth the fee (payments protection, reputation, scheduling), not just block contact info |
| Shill bidding or fake demand | Sellers | Seller can bid via another account | Link accounts; hide bidder identity patterns |
| Bait-and-switch listings | Sellers | Listing can change after purchase or booking | Snapshot listing at transaction |
| Cherry-picking (accept only the best jobs, decline the rest) | Supply side | Supply sees full job details before accepting | Show less before accepting, or reward acceptance rate |
| Adverse selection (only the worst risks opt in) | Users of insurance-like features (guarantees, protection) | Opt-in protection priced flat | Price by risk, make it default, or bundle |
| Moral hazard (careless because someone else pays) | Protected users | Full coverage, no deductible | Partial coverage; something at stake |
| Cold-start fakery (seed fake supply to attract demand) | The company | Fake listings or profiles | Seed with real subsidized supply instead; fake supply is a trust bomb |

## Auctions, deadlines, and timing

| Strategy | Who does it | Signal in code or rules | Fix pattern |
| --- | --- | --- | --- |
| Sniping (bid in the last second) | Bidders | Hard close | Soft close: extend on late bids (the Amazon vs eBay auction comparison in Roth & Ockenfels, 2002, shows hard closes drive late bidding) |
| Waiting for the inevitable discount | Customers | Predictable sales or end-of-quarter deals | Fewer, less predictable discounts; reward early commitment |
| Front-running a visible queue | Fast or automated users | Order visible before it executes | Batch, randomize, or hide until execution |
| Racing the drop (bots on limited releases) | Resellers | First-come-first-served | Raffle or lottery; verified-human queue |
| Streak protection hoarding | Engaged users | Streak freezes accumulate without limit | Cap freezes |
| Fake activity to keep a streak | Engaged users | Streak counts any trivial action | Streak counts the meaningful action |

## Griefing (hurting others cheaply)

Payoff isn't money: spite, rivalry, or fun. The test is the ratio of damage to effort.

| Strategy | Who does it | Signal in code or rules | Fix pattern |
| --- | --- | --- | --- |
| Spam invites or mentions | Trolls | No rate limit or consent for being added | Rate limits; consent before adding |
| Reserve and abandon (hold inventory, slots, usernames) | Rivals, squatters | Free holds with no expiry | Short holds, deposits |
| Vandalism of shared spaces | Trolls | Anyone can edit, no history or revert | History and one-click revert; trust levels |
| Report abuse | Rivals | Auto-actions on reports | See Reputation |
| Cost dumping (trigger expensive work on someone else's bill) | Malicious users | Actions that cost the target money (sending SMS, running jobs) | Charge the actor, or require target opt-in |

## Collusion

| Strategy | Who does it | Signal in code or rules | Fix pattern |
| --- | --- | --- | --- |
| Win trading (take turns winning) | Competitive players | Same pairs matched repeatedly | Randomize matching; detect repeated pairs |
| Price coordination between sellers | Sellers | Public pricing plus repeated interaction | Usually a legal issue, not a product one; flag it |
| Group buying a solo deal | Groups | Per-account discount on shareable goods | Per-household or per-payment limits |
| Referral rings | Groups | A refers B refers C refers A | Graph checks on referral trees |

## Metrics and Goodhart's law

"When a measure becomes a target, it ceases to be a good measure." Run this on the team's own metrics, not just users.

| Metric | How it goes up while the goal goes down | Better pairing |
| --- | --- | --- |
| Daily active users | Notifications and streaks that pull people in for nothing | DAU paired with task completion or retention |
| Time in app | Slower flows, infinite feeds | Time to complete the job |
| Signups | Lower friction, lower intent, more junk | Activated accounts |
| Tickets closed | Closing without solving | Reopen rate |
| Messages sent | Spammy prompts to message | Replies received |
| Conversion on a funnel step | Dark patterns, pre-checked boxes | Refund rate, churn after conversion |
| Lines of code or PRs merged | Smaller, noisier changes | Cycle time with defect rate |
| NPS | Asking only happy users | Response rate and who was asked |

## The company as a player

The company is in the game too. When the company profits from the user losing, users eventually notice, and trust is the thing that breaks.

| Strategy | Signal | Why it backfires |
| --- | --- | --- |
| Hard-to-cancel flows | Cancellation needs a call or six screens | Chargebacks, regulator attention, reviews |
| Auto-renew without reminder | No reminder before annual renewal | Refund requests, churn at next renewal |
| Drip pricing (fees revealed at checkout) | Price on listing ≠ price at payment | Abandoned carts, trust |
| Credits that silently expire | Expiry not shown in the balance | Users feel robbed; they stop buying credits |
| Rankings that favor paying sellers without disclosure | Paid placement looks organic | Buyers stop trusting rankings |
| Data lock-in (no export) | No export, or export in unusable formats | Users don't commit in the first place |

---

## Classic games behind the patterns

Name the game in the report when it helps the team see the shape.

| Game | Shape | Shows up as |
| --- | --- | --- |
| Prisoner's dilemma | Each does better defecting, both do worse if both defect | Rating inflation, review retaliation, price wars |
| Tragedy of the commons | Shared resource, private benefit, shared cost | Unlimited free tiers, shared quotas, notification spam |
| Free rider | Benefit without contributing | Account sharing, team plans where one person pays |
| Adverse selection | Hidden information drives the wrong people in | Flat-priced protection, "unlimited" plans attracting the heaviest users |
| Moral hazard | Hidden action once protected | Full guarantees, no deductible |
| Sybil attack | One actor, many identities | Votes, referrals, reviews, trials |
| Sniping / last-mover advantage | Fixed deadline rewards waiting | Auctions, limited offers, end-of-period bonuses |
| Chicken-and-egg | Each side waits for the other | Marketplaces at launch, network-effect features |
| Cobra effect | Paying for the symptom grows the cause (from the story of a bounty on cobras that led to cobra breeding) | Bounties per bug found, rewards per item reported |
| Goodhart's law | The metric drifts from the goal once targeted | Any KPI tied to a reward |

## Fix patterns

Ordered from most to least preferred. Higher ones make honesty the best strategy; lower ones police it.

1. **Change the payoff.** Reward the outcome you want, not a proxy for it (pay on activation, not signup; on usage, not upload count).
2. **Change the timing.** Vest, delay, or claw back rewards past the window where an exploit is reversible (refund windows, trial ends). Soft-close deadlines.
3. **Change the information.** Simultaneous reveal, hidden bidder identities, hiding the exact threshold, not showing supply the full job before they accept.
4. **Change the unit.** Price and limit on something sharing or splitting doesn't break (workspace, usage, payment fingerprint).
5. **Cap and decay.** Caps on balances, expiry on credits, seasonal leaderboards, decaying reputation.
6. **Raise identity cost proportionally.** Ask for more verification only where the reward is worth more; never make every honest user pay for a rare abuser.
7. **Detect and act.** Graph checks, velocity limits, reviewer weighting. Needed sometimes, but it's an arms race; it should back up the mechanism, not replace it.
8. **Terms of service.** A rule only in the ToS is a rule only honest users follow. Back it with one of the above.
