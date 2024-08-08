# Trade Matching Algorithm

## Overview

This Python script processes a list of trades, matches buy and sell trades, and calculates the remaining unmatched trades. It uses a stack-based approach to handle unmatched buy trades and matches them with sell trades in reverse order. The results, including matched trades and remaining unmatched buys, are printed to the console.

## Code Explanation

### Trade Data

The `trades` list contains dictionaries representing individual trades. Each dictionary has the following fields:

- `id`: Unique identifier for the trade.
- `side`: Either "Buy" or "Sell".
- `avg_fill`: Average fill price of the trade.
- `formatted_filled`: Quantity of the trade, initially multiplied by 10,000,000 for precision.

### Processing Steps

1. **Adjust Quantity Precision:** Each trade's `formatted_filled` value is scaled by multiplying it by 10,000,000 for precision in calculations.
2. **Initialize Stacks and Counters:**

   - `buy_stack`: A stack to keep track of unmatched buy trades.
   - `matches`: A list to store matched trades.
   - `quantity_buys`, `quantity_sells`, `quantity_remainder`: Counters for total buy quantities, sell quantities, and remaining quantities.

3. **Match Trades:**

   - Iterate through the trades in reverse order.
   - For "Buy" trades, add the trade to `buy_stack` and update `quantity_buys`.
   - For "Sell" trades, attempt to match with trades from `buy_stack`. Update `quantity_sells` and record matches.

4. **Output Results:**
   - Print matched trades.
   - Print remaining unmatched buy trades.
   - Print total buys, sells, and remaining quantities.

## Code

```python
trades = [
    {"id": 1, "side": "Buy", "avg_fill": 65888.70, "formatted_filled": 0.00600000},
    {"id": 2, "side": "Sell", "avg_fill": 64309.50, "formatted_filled": 0.00200000},
    {"id": 3, "side": "Sell", "avg_fill": 63367.40, "formatted_filled": 0.00200000},
    {"id": 4, "side": "Sell", "avg_fill": 62831.10, "formatted_filled": 0.00400000},
    {"id": 5, "side": "Buy", "avg_fill": 58652.20, "formatted_filled": 0.00800000},
    {"id": 6, "side": "Sell", "avg_fill": 58901.30, "formatted_filled": 0.00300000},
    {"id": 7, "side": "Sell", "avg_fill": 58695.50, "formatted_filled": 0.00200000},
    {"id": 8, "side": "Sell", "avg_fill": 58725.60, "formatted_filled": 0.00400000},
    {"id": 9, "side": "Sell", "avg_fill": 58746.80, "formatted_filled": 0.00800000},
    {"id": 10, "side": "Buy", "avg_fill": 56917.00, "formatted_filled": 0.00300000},
    {"id": 11, "side": "Buy", "avg_fill": 55500.00, "formatted_filled": 0.00400000},
    {"id": 12, "side": "Sell", "avg_fill": 57862.50, "formatted_filled": 0.00900000},
    {"id": 13, "side": "Buy", "avg_fill": 54198.10, "formatted_filled": 0.00400000},
    {"id": 14, "side": "Buy", "avg_fill": 57167.20, "formatted_filled": 0.00400000},
    {"id": 15, "side": "Buy", "avg_fill": 60319.70, "formatted_filled": 0.00300000},
    {"id": 16, "side": "Buy", "avg_fill": 56500.00, "formatted_filled": 0.00400000},
    {"id": 17, "side": "Buy", "avg_fill": 61580.40, "formatted_filled": 0.00400000}
]

quantity_buys = 0
quantity_sells = 0
quantity_remainder = 0

# Stack to keep track of unmatched buys
for trade in trades:
    trade['formatted_filled'] *= 10000000

buy_stack = []
matches = []

# Loop through trades in reversed order
for trade in reversed(trades):
    if trade['side'] == 'Buy':
        quantity_buys += trade['formatted_filled']
        buy_stack.append(trade)
    elif trade['side'] == 'Sell':
        quantity_sells += trade['formatted_filled']
        sell_trade_id = trade['id']
        sell_matches = []
        while trade['formatted_filled'] > 0 and buy_stack:
            buy_trade = buy_stack[-1]
            buy_trade_id = buy_trade['id']

            if buy_trade['formatted_filled'] > trade['formatted_filled']:
                matched_quantity = trade['formatted_filled']
                buy_trade['formatted_filled'] -= matched_quantity
                trade['formatted_filled'] = 0
            else:
                matched_quantity = buy_trade['formatted_filled']
                trade['formatted_filled'] -= matched_quantity
                buy_stack.pop()

            sell_matches.append((buy_trade_id, matched_quantity))

        matches.append((sell_trade_id, sell_matches))

    quantity_remainder = quantity_buys - quantity_sells

# Print matches
for sell_id, sell_matches in matches:
    print(f"Sell Trade ID: {sell_id}")
    for buy_id, quantity in sell_matches:
        print(f"  Matched with Buy Trade ID: {buy_id}, Quantity: {quantity / 10000000:.3f}")

# Print remaining unmatched buys
for buy_trade in buy_stack:
    print(f"Unmatched Buy Trade ID: {buy_trade['id']}, Quantity: {buy_trade['formatted_filled'] / 10000000:.3f}")

print(f"Buys:  {quantity_buys / 10000000: .8f}")
print(f"Sells:  {quantity_sells / 10000000: .8f}")
print(f"Quantity remaining:  {quantity_remainder / 10000000:.8f}")
```

# Trade Results

## Matched Trades

| Sell Trade ID | Buy Trade ID | Quantity (BTC) |
| ------------- | ------------ | -------------- |
| 12            | 13           | 0.004          |
| 12            | 14           | 0.004          |
| 12            | 15           | 0.001          |
| 9             | 10           | 0.003          |
| 9             | 11           | 0.004          |
| 9             | 15           | 0.001          |
| 8             | 15           | 0.001          |
| 8             | 16           | 0.003          |
| 7             | 16           | 0.001          |
| 7             | 17           | 0.001          |
| 6             | 17           | 0.003          |
| 4             | 5            | 0.004          |
| 3             | 5            | 0.002          |
| 2             | 5            | 0.002          |

## Unmatched Buys

| Buy Trade ID | Quantity (BTC) |
| ------------ | -------------- |
| 1            | 0.006          |

## Summary

| Description        | Quantity (BTC) |
| ------------------ | -------------- |
| Total Buys         | 0.04000000     |
| Total Sells        | 0.03400000     |
| Quantity Remaining | 0.00600000     |

# Running the Script

To run this script, follow these steps:

1. Save the script to a file named `deque.py`.
2. Open a terminal or command prompt.
3. Navigate to the directory containing `deque.py`.
4. Run the script using Python 3:

   ```bash
   python3 deque.py
   ```

The script will output the matched trades, unmatched buys, and the summary of total buys, sells, and remaining quantities.
