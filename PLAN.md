# DL Dashboard - Project Brief

## What is this
A single page analytics dashboard showing monthly business metrics like a simple Google Analytics view.

## Data
Generate a fake dataset as a JSON file (src/data/metrics.json). 12 months of data (Jan-Dec 2025). Each month contains: 
-revenue (dollar amount, trending upward with some variation)
- visitors (number, seasonal pattern - higher in summer)
- conversions (percentage, fluctuates between 2-5%)
- orders (number, correlates loosely with visitors)

## Layout (Vuetify)
- v-app-bar at the top with dashboard title and month picker
- month picker defaults to showing ALL months
- When specific month slected all cards and charts filter to that month. when "all" is selected, show full year
- below app bar: row of 4 summary cards (v-card) showing key metrics - revenue, visitors, conversions, orders
- below the cards: row of 2 charts
    - Left: bar chart showing monthly revenue
    - Right: line chart showing visitors over time
- below that: one full-width area chart showing conversion trend
- use v-container, v-row, v-col for reponsive grid layout

## Interactions
- Month picker in app bar filters EVERYTHING - summary cars show that month's numbers, charts highlight or filter to that month
- When "all" is selected summary cards show yearly totals/averages and charts show all 12 months
- Cards should small up/down arrow or collo indicating change from previous month

## Style
- Dark theme by default (Vuetify dark theme)
- Clean, minimal, a lot of whitespace
- Charts us cohesive color pallette, natural colors
- Mobile reponsive - cards stack on small screens

## Tech
- Vue 3 + Typescript + Vuetify 3
- Chart.js via vue-chartjs for all charts
- Fake data from a local JSON file (no API calls)
- Single page - no routing needed for this app