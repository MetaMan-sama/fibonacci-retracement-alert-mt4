# Fibonacci Retracement Alert — MQL4 Script

A MetaTrader 4 script that pre-computes a **seven-level Fibonacci retracement grid** from user-defined high and low anchor points using the standard ratios `{0.0, 0.236, 0.382, 0.5, 0.618, 0.786, 1.0}`, then monitors price in real time and fires proximity alerts when the current close comes within a configurable `Point × 5` tolerance of any retracement level — logging the matched level's price and percentage label on every trigger.

---

## Overview

Fibonacci retracement levels are derived by multiplying the range between a significant swing high and swing low by each Fibonacci ratio and adding the result to the swing low, producing seven price levels that historically attract price reactions. This script pre-calculates all seven levels on startup from the user-supplied `FibHigh` and `FibLow` anchor inputs, prints each level and its percentage label to the Experts tab for verification, then enters a monitoring loop that checks every bar close against all seven levels simultaneously using `MathAbs(currentPrice − retracementLevels[i]) < Point × 5` proximity detection. Any level hit within tolerance fires an alert including the current price, the precise Fibonacci level, and the percentage ratio label.

---

## Features

- **Seven-level pre-computed grid** — `retracementLevels[i] = FibLow + (FibHigh − FibLow) × FibLevels[i]` computed at startup from the constant ratio array `{0.0, 0.236, 0.382, 0.5, 0.618, 0.786, 1.0}` using a single `ArraySize(FibLevels)` loop
- **Startup level audit print** — each retracement level is printed with `PrintFormat("Fibonacci Level: %.5f (%.1f%%)", retracementLevels[i], FibLevels[i] × 100)` at initialization for manual visual verification before monitoring begins
- **`Point × 5` proximity tolerance** — `MathAbs(currentPrice − retracementLevels[i]) < Point × 5` fires on approach within 5 points of any level, appropriate for 5-digit forex brokers; adjustable at source level for higher-volatility instruments
- **Full-grid simultaneous scanning** — all seven levels evaluated in a single `ArraySize()` loop each cycle; multiple coincident level touches in the same bar each fire independently
- **Three notification channels:** sound alert, email, and mobile push
- **Lightweight loop** — polls once per minute (`Sleep(60000)`)
- Alert message includes current price, matched Fibonacci level price, and percentage label for immediate trading context

---

## How It Works

1. On `OnStart()`, a `double retracementLevels[7]` array is populated via `FibLow + (FibHigh − FibLow) × FibLevels[i]` for all 7 ratios; each level printed to Experts tab
2. Every minute, `iClose(TradeSymbol, Timeframe, 0)` fetches the current close
3. All 7 levels are scanned: `MathAbs(currentPrice − retracementLevels[i]) < Point × 5` triggers `AlertFibonacci()` for any matched level
4. `AlertFibonacci()` formats the message with price, Fibonacci level, and percentage label via `StringFormat("%.5f (%.1f%%)", ...)` and dispatches via all enabled channels

---

## Input Parameters

| Parameter      | Type            | Default     | Description                                                                  |
|----------------|-----------------|-------------|------------------------------------------------------------------------------|
| `TradeSymbol`  | string          | `EURUSD`    | Symbol for analysis                                                          |
| `Timeframe`    | ENUM_TIMEFRAMES | `PERIOD_H1` | Timeframe for price monitoring                                               |
| `FibHigh`      | double          | `1.2000`    | Swing high anchor point for Fibonacci grid calculation                       |
| `FibLow`       | double          | `1.1000`    | Swing low anchor point for Fibonacci grid calculation                        |
| `EnableAlerts` | bool            | `true`      | Fire an on-screen/sound alert                                                |
| `EnableEmail`  | bool            | `false`     | Send an email notification                                                   |
| `EnablePush`   | bool            | `false`     | Send a mobile push notification                                              |

---

## Alert Message Format

```
Price hit Fibonacci Level detected on EURUSD (Timeframe: PERIOD_H1)
Price: 1.16180, Fibonacci Level: 1.16180 (61.8%)
```

---

## Installation

1. Copy `Fibonacci_Retracement_Levels_001.mq4` to `MQL4/Scripts/` in your MT4 data folder
2. Compile in MetaEditor (F7)
3. Drag onto any chart from Navigator → Scripts
4. Set `FibHigh` and `FibLow` to your swing anchor points and click **OK**

> **Note:** `FibHigh` and `FibLow` must be set to meaningful swing points on your chart before running. The script does not auto-detect swing highs/lows — anchor points are user-defined inputs.

---

## Requirements

- MetaTrader 4 (`#property strict` compatible build)
- MQL4 compiler (MetaEditor)

---

## License

MIT License

Copyright (c) 2026

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
