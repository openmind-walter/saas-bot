---
form:
  id: bs_parameters
  css: "form-container max-w-xl mx-auto p-4 sm:p-6 bg-white rounded-lg shadow-sm grid grid-cols-1 md:grid-cols-2 gap-4"

  layout:
    type: grid
    css: "grid grid-cols-1 md:grid-cols-2 gap-4"
    areas:
      - ["initial_cash", "min_trade_value"]
      - ["buy_price_down", "sell_price_up"]
      - ["min_profit_pct", "min_profit_pct"]
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
      help: "Deployable budget for the grid. Each leg costs one trade; the budget caps grid depth."
      validation:
        required: true
        min: 0

    - id: min_trade_value
      label: "Trade value ($)"
      component: number
      css: "input input-bordered w-full"
      bind: "params.min_trade_value"
      default: 200
      props:
        step: 10
        placeholder: "200"
      help: "Dollar value committed per trade (each grid leg)."
      validation:
        required: true
        min: 0

    - id: buy_price_down
      label: "Buy when price drops (%)"
      component: number
      css: "input input-bordered w-full"
      bind: "params.buy_price_down"
      default: 5
      props:
        step: 0.5
        placeholder: "5"
      help: "How far price must drop below the lowest open buy before buying another leg."
      validation:
        required: true
        min: 0
        max: 100

    - id: sell_price_up
      label: "Short when price rises (%)"
      component: number
      css: "input input-bordered w-full"
      bind: "params.sell_price_up"
      default: 5
      props:
        step: 0.5
        placeholder: "5"
      help: "How far price must rise above the highest open sell before shorting another leg."
      validation:
        required: true
        min: 0
        max: 100

    - id: min_profit_pct
      label: "Profit target (%)"
      component: number
      css: "input input-bordered w-full"
      bind: "params.min_profit_pct"
      default: 10
      props:
        step: 0.5
        placeholder: "10"
      help: "How far a leg must move in its favour before it is closed for profit."
      validation:
        required: true
        min: 0
        max: 100

  submit:
    id: submit
    label: "Save parameters"
    css: "btn btn-primary col-span-2 w-full"
---

# BS (buy_sell) Strategy Parameters

This Markdown body is ignored by the form engine — it documents the schema that
lives above the closing `---`. The form binds to the bot's `params` JSONB blob,
which `strategy-core` reads as [`BsParams`](../strategy-core/src/bs.rs).

BS runs a **bidirectional grid of independent legs**: it opens an initial long,
ladders more buys lower and more shorts higher as price runs, and closes each leg
on its own once it reaches the profit target.

| Parameter         | Label                       | Default | Meaning                                                                  |
| ----------------- | --------------------------- | ------- | ------------------------------------------------------------------------ |
| `initial_cash`    | Initial cash ($)            | 1000    | Deployable budget for the grid; the budget caps how deep the grid grows. |
| `min_trade_value` | Trade value ($)             | 200     | Dollar value committed per trade (each grid leg).                        |
| `buy_price_down`  | Buy when price drops (%)    | 5       | How far price must drop before buying another leg.                       |
| `sell_price_up`   | Short when price rises (%)  | 5       | How far price must rise before shorting another leg.                     |
| `min_profit_pct`  | Profit target (%)           | 10      | How far a leg must move in its favour before it is closed.               |

Percentages are whole numbers (e.g. `5` = 5%), matching `BsParams`. All five
parameters are required.
