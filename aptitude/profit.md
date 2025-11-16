++ # Profit & Loss
+
## Overview

Profit and Loss is a fundamental topic in aptitude and basic commerce. It studies how buying price, selling price, cost, discounts, and markups interact to produce profit or loss. This page covers definitions, core formulas, worked examples, common tricks, and practice problems with answers.

## Basic Definitions

- **Cost Price (CP):** The price at which an item is purchased.
- **Selling Price (SP):** The price at which the item is sold.
- **Profit (Gain):** When SP > CP. Profit = SP - CP.
- **Loss:** When SP < CP. Loss = CP - SP.
- **Profit Percentage (Profit %):** (Profit / CP) × 100.
- **Loss Percentage (Loss %):** (Loss / CP) × 100.

Core relations:

- Profit = SP - CP
- Loss = CP - SP
- Profit% = (SP - CP) / CP × 100
- Loss% = (CP - SP) / CP × 100
- SP = CP × (1 + Profit%/100)
- SP = CP × (1 - Loss%/100)

Note: Percentages are almost always calculated with respect to CP unless explicitly stated otherwise.

## Important Derived Formulas

- If profit% = p, then SP = CP × (1 + p/100).
- If loss% = l, then SP = CP × (1 - l/100).
- If an item is sold at x% profit and then bought back at y% loss, combined effect is multiplicative on CP:

  Final amount = CP × (1 + x/100) × (1 - y/100)

- When given SP and profit%, CP = SP / (1 + p/100).

## Discounts, Markup, and Relationship

- **Discount:** A reduction on the marked price (MP). If MP is the listed price and discount d% is applied, then Selling Price SP = MP × (1 - d/100).
- **Markup:** The amount added to CP to obtain MP. Markup% = (MP - CP)/CP × 100.

Relationship example: A dealer marks up goods to MP and gives discount d% to customers. The seller's actual profit depends on MP, discount, and CP.

## Worked Examples

1) Simple profit:

   CP = 500, SP = 600. Profit = 600 - 500 = 100. Profit% = (100/500) × 100 = 20%.

2) Given profit% find SP:

   CP = 250, profit% = 12%. SP = 250 × (1 + 12/100) = 250 × 1.12 = 280.

3) Given SP and profit%, find CP:

   SP = 420, profit% = 20%. CP = 420 / 1.20 = 350.

4) Loss example:

   CP = 400, SP = 360. Loss = 40, Loss% = (40/400) × 100 = 10%.

5) Buy and sell sequence (combined effect):

   An item bought at CP = 100 is sold at 20% profit, then repurchased (or another sale) causes 10% loss on the new price.

   After profit: 100 × 1.20 = 120. After 10% loss: 120 × 0.90 = 108. Net gain = 8 → 8% overall profit.

6) Markup and discount:

   CP = 80, seller wants 25% profit, so MP = 80 × 1.25 = 100. If a discount of 10% is given on MP: SP = 100 × 0.90 = 90 → Profit = 90 - 80 = 10 → Profit% = 12.5%.

## Quick Tricks and Pitfalls

- Always check whether percentages are with respect to CP, MP, or SP.
- For combined successive % changes, multiply factors: (1 ± p1/100) × (1 ± p2/100) ...
- A p% profit followed by p% loss does not cancel out; net factor = (1 + p/100)(1 - p/100) = 1 - (p/100)^2 < 1, hence net loss.
- When problems give profit/loss per item and total, compute per-item CP or SP to scale.

## Practice Problems

1) If an article is sold at a profit of 20% for $1200, find its cost price.

2) A shopkeeper buys an article for $250 and incurs a loss of 8% when selling. What is the selling price?

3) An item is marked 40% above cost price. The shopkeeper gives a discount of 10% on the marked price. Find his gain or loss percentage.

4) A merchant sells two items for $240 each. On one he gains 20% and on the other he loses 20%. What is his overall profit or loss?

5) A trader marks goods at 25% above cost. He allows a discount of 20% and still makes a profit. Find the profit percent.

6) An item is sold at 15% profit. If the item had been sold at $10 more, the profit would be 20%. Find CP and SP.

## Answers (step-wise brief)

1) SP = 1200, profit% = 20% → CP = SP / 1.20 = 1200 / 1.2 = 1000.

2) CP = 250, loss% = 8% → SP = 250 × (1 - 0.08) = 250 × 0.92 = 230.

3) MP = CP × 1.40. After 10% discount, SP = MP × 0.90 = CP × 1.40 × 0.90 = CP × 1.26 → Profit% = 26%.

4) First item: sold at 20% gain → CP1 = 200 (since SP1 = 240 = CP1 × 1.2 → CP1 = 240/1.2 = 200)
   Second item: sold at 20% loss → CP2 = 300 (since SP2 = 240 = CP2 × 0.8 → CP2 = 240/0.8 = 300)
   Total CP = 500, Total SP = 480 → Net loss = 20 → Loss% = 20/500 × 100 = 4% loss.

5) CP = 100 (assume). MP = 125. After 20% discount, SP = 125 × 0.80 = 100 → Profit = 0 → 0% profit. If CP not 100, same result: SP == CP ⇒ 0% profit.

6) Let CP = x. SP1 = x × 1.15. If sold at $10 more, SP2 = x × 1.15 + 10 = x × 1.20. So x × 1.15 + 10 = x × 1.20 ⇒ 10 = x × 0.05 ⇒ x = 200. CP = 200. SP = 200 × 1.15 = 230.

## Summary

Remember: percent in profit/loss questions is almost always relative to cost price. Use multiplicative factors for successive percentage changes, and convert between CP / SP / MP carefully. Practice with the problems above and vary numbers to build intuition.

## Further Practice Suggestions

- Formulate reverse problems (given CP and profit%, find SP; given SP and loss%, find CP).
- Combine discounts and successive operations to practice multiplicative effects.
