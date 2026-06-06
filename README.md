# Poker Equity Engine

A Texas Hold'em equity calculator built from scratch in vanilla JavaScript. No libraries, no dependencies — just combinatorics, Monte Carlo simulation, and probability math.

---

## Features

- **Equity Calculator** — pick hole cards and board cards for up to 4 players, run 10,000 Monte Carlo simulations to calculate win/tie/lose percentages
- **Pot Odds Calculator** — given pot size, bet to call, and your equity, calculates whether a call is +EV
- **EV Calculator** — expected value formula: `p × win − (1−p) × lose`
- **Outs Calculator** — exact equity from outs using combinatorics, with Rule of 4 & 2 comparison

---

## How it works

### Hand Evaluator

The core evaluator uses **C(7,5) = 21 combination enumeration** — every possible 5-card hand is scored and the best is returned.

Each 5-card hand is classified into one of 9 ranks:

| Rank | Hand |
|------|------|
| 8 | Straight flush / Royal flush |
| 7 | Four of a kind |
| 6 | Full house |
| 5 | Flush |
| 4 | Straight |
| 3 | Three of a kind |
| 2 | Two pair |
| 1 | Pair |
| 0 | High card |

Tiebreaks are resolved using ordered arrays of card values (e.g. for a full house: `[trip_value, pair_value]`). Edge cases handled:
- **Wheel straight** (A-2-3-4-5): Ace counts as 1
- **Royal flush** detection: Broadway straight + flush
- **Chopped pots**: multiple players with equal hands split the equity

### Monte Carlo Simulation

For any given set of known cards (hero hand, villain hand, board), the engine:

1. Builds a **reduced deck** — 52 cards minus all known cards
2. Runs **10,000 iterations**, each time:
   - Shuffling the remaining deck (Fisher-Yates)
   - Dealing unknown hole cards and completing the board
   - Evaluating all player hands
   - Recording wins and ties
3. Returns `equity = (wins + 0.5 × ties) / N`

Why Monte Carlo instead of exact enumeration? For 2 unknown hands and a full 5-card runout, exact enumeration requires iterating over millions of combinations. Monte Carlo converges to <0.5% error at 10k iterations and runs in ~50ms in the browser.

### Pot Odds

```
pot_odds = bet / (pot + bet)
```

If `your_equity > pot_odds` → call is profitable. The edge is `equity − pot_odds`.

### EV Formula

```
EV = p × win_amount − (1 − p) × lose_amount
```

Where `p` is your equity as a decimal.

---

## Running locally

No build step needed — it's a single HTML file.

```bash
git clone https://github.com/your-username/poker-equity-engine
cd poker-equity-engine
open index.html
```

Or serve it:

```bash
npx serve .
# → http://localhost:3000
```

---

## Project structure

```
poker-equity-engine/
├── index.html      # Everything: HTML, CSS, JS
└── README.md
```

---

## Potential extensions

- **Range vs range equity** — define hand ranges (e.g. "top 20% of hands") and compute equity across all combinations
- **Push/fold solver** — Nash equilibrium shove/call ranges by stack depth (big blinds)
- **Hand history replay** — paste a hand history and visualize equity at each street

---

## License

MIT
