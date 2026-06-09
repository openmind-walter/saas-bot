---
form:
  id: aim_parameters
  css: "form-container max-w-xl mx-auto p-4 sm:p-6 bg-white rounded-lg shadow-sm grid grid-cols-1 md:grid-cols-2 gap-4"

  layout:
    type: grid
    css: "grid grid-cols-1 md:grid-cols-2 gap-4"
    areas:
      - ["initial_cash", "ratchet_pct"]
      - ["buy_safe", "sell_safe"]
      - ["min_buy", "min_sell"]
      - ["submit", "submit"]

  fields:
    - id: initial_cash
      label: "Initial cash ($)"
      component: number
      css: "input input-bordered w-full"
      bind: "params.initial_cash"
      default: 1000
      props:
        step: 100
        placeholder: "1000"
      help: "Starting cash, deployed half on the opening buy."
      validation:
        required: true
        min: 0

    - id: ratchet_pct
      label: "Ratchet (0–1)"
      component: number
      css: "input input-bordered w-full"
      bind: "params.ratchet_pct"
      default: 0.5
      props:
        step: 0.05
        placeholder: "0.5"
      help: "Fraction of each buy added to the control value, banking profit over time."
      validation:
        required: true
        min: 0
        max: 1

    - id: buy_safe
      label: "Buy safe (%)"
      component: number
      css: "input input-bordered w-full"
      bind: "params.buy_safe"
      default: 5
      props:
        step: 0.5
        placeholder: "5"
      help: "Buy-side safety band — ignores price dips smaller than this."
      validation:
        required: true
        min: 0
        max: 100

    - id: sell_safe
      label: "Sell safe (%)"
      component: number
      css: "input input-bordered w-full"
      bind: "params.sell_safe"
      default: 8
      props:
        step: 0.5
        placeholder: "8"
      help: "Sell-side safety band — ignores price rises smaller than this."
      validation:
        required: true
        min: 0
        max: 100

    - id: min_buy
      label: "Min buy ($)"
      component: number
      css: "input input-bordered w-full"
      bind: "params.min_buy"
      default: 10
      props:
        step: 1
        placeholder: "10"
      help: "Minimum trade value to act on a buy (filters tiny trades). 0 disables."
      validation:
        min: 0

    - id: min_sell
      label: "Min sell ($)"
      component: number
      css: "input input-bordered w-full"
      bind: "params.min_sell"
      default: 10
      props:
        step: 1
        placeholder: "10"
      help: "Minimum trade value to act on a sell. 0 disables."
      validation:
        min: 0

  submit:
    id: submit
    label: "Save parameters"
    css: "btn btn-primary col-span-2 w-full"
---

# AIM Strategy Parameters

This Markdown body is ignored by the form engine — it documents the schema that
lives above the closing `---`. The form binds to the bot's `params` JSONB blob,
which `strategy-core` reads as [`AimParams`](../strategy-core/src/aim.rs).

| Parameter      | Label            | Default | Meaning                                                                    |
| -------------- | ---------------- | ------- | -------------------------------------------------------------------------- |
| `initial_cash` | Initial cash ($) | 1000    | Starting cash, deployed half on the opening buy.                           |
| `buy_safe`     | Buy safe (%)     | 5       | Buy-side safety band — ignores price dips smaller than this.               |
| `sell_safe`    | Sell safe (%)    | 8       | Sell-side safety band — ignores price rises smaller than this.             |
| `ratchet_pct`  | Ratchet (0–1)    | 0.5     | Fraction of each buy added to the control value, banking profit over time. |
| `min_buy`      | Min buy ($)      | 10      | Minimum trade value to act on a buy (filters tiny trades).                 |
| `min_sell`     | Min sell ($)     | 10      | Minimum trade value to act on a sell.                                      |

Percentages are whole numbers (e.g. `5` = 5%), matching `AimParams`. `min_buy` /
`min_sell` are optional (`0` disables the floor); the rest are required.
