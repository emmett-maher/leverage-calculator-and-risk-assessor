# Build: Leverage Calculator and Risk Assessor

## Context

This app helps investors evaluate leverage opportunities across real estate, stocks, and business investments. Users input investment parameters like loan amount, interest rates, and asset values to see projected returns, monthly payments, and collateral requirements. The tool visualizes how leverage amplifies both gains and losses, helping users make informed decisions about their risk tolerance. Target users are real estate investors, stock traders using margin, and business owners considering loans. The idea stems from Sharran Srivatsaa's emphasis on understanding leverage as a wealth-building tool that requires careful risk assessment.

## Tech Stack

- **Next.js 14 with App Router** - Server components for data fetching and modern routing
- **Supabase** - PostgreSQL for storing user scenarios and market data with real-time updates
- **shadcn/ui + Tailwind** - Consistent, accessible UI components with responsive design
- **Recharts** - Interactive charts for visualizing returns and risk scenarios
- **Auth.js** - User authentication to save and compare scenarios
- **Alpha Vantage API** - Real-time stock market data for margin calculations
- **Plaid API** - Connect bank accounts to assess actual collateral positions

## Phase 1: Project Setup & Scaffolding

Create a Next.js app with TypeScript, install dependencies for shadcn/ui, Supabase client, Recharts, and Auth.js. Set up environment variables for `SUPABASE_URL`, `SUPABASE_ANON_KEY`, `ALPHA_VANTAGE_API_KEY`, `PLAID_CLIENT_ID`, `PLAID_SECRET`, and Auth.js secrets.

Core data model centers on **LeverageScenario** - an investment calculation with parameters, results, and risk metrics. Key entities include User (authenticated investor), Scenario (saved calculations), AssetType (real estate, stocks, business), and MarketData (current rates and valuations).

```typescript
interface LeverageScenario {
  id: string
  userId: string
  assetType: 'real_estate' | 'stocks' | 'business'
  principal: number
  leverageRatio: number
  interestRate: number
  projectedReturn: number
  riskScore: number
  createdAt: Date
}
```

## Phase 2: Core Data Model & Backend

Store scenarios in Supabase with tables for users, scenarios, and market_data. Each scenario links to a user and includes all calculation parameters and results. Market data table caches current interest rates, stock volatility indices, and real estate cap rates, updated hourly via scheduled functions.

Implement Row Level Security so users only see their own scenarios. Create database functions for complex calculations like risk scoring that factor in leverage ratio, asset volatility, and user's total exposure across all investments. Use Supabase realtime subscriptions to sync scenario updates across browser tabs.

## Phase 3: UI & Components

**CalculatorForm** - Multi-step form with tabs for each asset type. Collects investment amount, down payment, interest rate, expected returns. Shows live calculation preview as user types. Validates inputs and displays helpful tooltips explaining each field.

**ResultsPanel** - Split view showing projected returns chart on left, risk metrics on right. Chart displays monthly cash flow, total returns over time, and break-even point. Risk section shows leverage ratio gauge, margin call threshold, and required collateral.

**ScenarioComparison** - Side-by-side view of up to three saved scenarios. Synchronized charts highlight differences in returns and risk. Export comparison as PDF report.

**MarketDataWidget** - Live-updating panel showing current mortgage rates, margin rates, and business loan rates. Click to use current rate in calculator.

**PortfolioOverview** - Dashboard aggregating all user's scenarios into total leverage exposure, weighted risk score, and diversification metrics.

## Phase 4: Feature Implementation

**Real Estate Calculations** - Factor in property value, down payment percentage, mortgage rate, rental income, expenses, and appreciation. Calculate cash-on-cash return, cap rate, and debt service coverage ratio. Show how leverage amplifies returns but requires sufficient rental income to cover payments.

**Stock Margin Scenarios** - Connect to Alpha Vantage for real stock prices and volatility. Calculate margin requirements, maintenance margins, and margin call prices. Visualize how market drops trigger forced selling. Include portfolio margin vs standard margin options.

**Business Loan Analysis** - Model term loans, lines of credit, and SBA loans. Factor in revenue projections, debt service coverage, and personal guarantee requirements. Show how leverage enables growth but requires consistent cash flow.

**Risk Assessment Engine** - Assign risk scores based on leverage ratio, asset volatility, user's income stability, and total debt load. Provide personalized warnings when scenarios exceed prudent risk levels. Suggest alternative structures to reduce risk.

**Plaid Integration** - Optional connection to verify actual assets available as collateral. Updates risk scores based on real net worth and liquidity positions.

## Phase 5: Polish & Integration

Implement skeleton screens during calculations and API calls. Show progress indicators for multi-step risk analysis. Design mobile-first with collapsible panels and swipeable scenario comparisons.

Add keyboard shortcuts for power users - quick scenario duplication, parameter adjustments with arrow keys. Persist form state in localStorage to prevent data loss. Export scenarios as CSV for further analysis in Excel.

Create onboarding flow explaining leverage concepts with interactive examples. Add help tooltips throughout explaining financial terms. Implement dark mode with proper color contrast for financial data visualization.

## Verification

Run `npm run dev` and test creating scenarios for each asset type. Verify calculations match manual Excel models. Test edge cases like negative leverage, extreme interest rates, and margin calls.

Create multiple scenarios and compare side-by-side. Connect Plaid in development mode to test collateral verification. Check responsive design on mobile devices. Build with `npm run build` and verify no hydration errors.

Test risk warnings trigger appropriately for over-leveraged scenarios. Verify market data updates in real-time. Export and re-import scenarios to validate data integrity.