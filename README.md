# Global Security Monte Carlo Simulator

This tool simulates supply chain risk in global security operations
using **Monte Carlo simulation**. It helps analyze how disruptions,
price volatility, and capacity issues affect costs and delivery
performance.

------------------------------------------------------------------------

## 1. What this tool does

The simulator runs **10,000 randomized "what-if" scenarios** to
estimate: - Monthly cost range (average, median, worst-case) - Risk of
delivery shortfalls - Probability of exceeding a cost threshold - Key
cost drivers (demand, fuel price, corruption tax, etc.)

------------------------------------------------------------------------

## 2. How to use it

### Step 1: Open the page

Open the `index.html` file in your browser.

### Step 2: Load the example scenario

Click **"Load Example"** to auto-fill realistic baseline data.

### Step 3: Run the simulation

Click **"Run Simulation"** to generate results.

### Step 4: Review the results

-   **Mean Cost** -- Average monthly cost
-   **P50 Cost** -- Median monthly cost
-   **P95 Cost** -- "Worst-case" cost that happens only \~5% of the time
-   **Std Dev** -- Cost variability
-   **Pr(cost \> T)** -- Probability cost exceeds your chosen threshold
-   **P90 Shortfall** -- High-risk shortfall volume

### Step 5: Explore the charts

-   **Histogram** -- Shows the spread of monthly costs from best- to
    worst-case.
-   **Driver Correlation** -- Highlights which factors most affect cost
    risk.

### Step 6: Customize the inputs

Adjust demand, disruption probabilities, or penalty values to test your
own scenario, then re-run the simulation.

### Step 7: Save or share your results

-   **Download CSV** -- Export detailed results for Excel or BI tools.
-   **Export JSON** -- Save your scenario settings to reload later or
    share with others.
-   **Import JSON** -- Load a shared scenario for collaboration.

------------------------------------------------------------------------

## 3. Key concepts

  -----------------------------------------------------------------------
  Term                     Explanation
  ------------------------ ----------------------------------------------
  **Monte Carlo            Testing many "what-if" situations to reveal a
  Simulation**             range of possible outcomes.

  **Iteration**            A single simulated "what-if" scenario.

  **P50 / P95**            Percentiles. P50 is the midpoint, P95 shows
                           the top 5% of outcomes (near worst-case).

  **Correlation**          Shows how strongly a factor (like demand or
                           fuel price) influences total costs.
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## 4. Practical uses

-   **Risk planning** -- Understand likelihood of budget overrun or
    delivery failure.
-   **Scenario testing** -- Explore effects of shocks like higher fuel
    or more cyber outages.
-   **Decision support** -- Compare alternative strategies and
    mitigation approaches.

------------------------------------------------------------------------

## 5. Tips

-   Start with the **example scenario** first.
-   Change only a few inputs at a time to understand cause and effect.
-   Use the **random seed** field to make results reproducible.
-   Share the CSV with analysts or decision-makers for deeper insights.

------------------------------------------------------------------------
