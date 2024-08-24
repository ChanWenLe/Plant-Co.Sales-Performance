# Plant-Co.Sales-Performance

The error is occurring because Tableau does not allow a mix of aggregated and non-aggregated calculations in an `IF` statement. Also, the issue arises from referencing a field (`[Selected Year]`) that is not defined as a parameter yet.

Here’s how to fix it:

### Step 1: Create the Parameter
You need to first create the `[Selected Year]` parameter.

1. Go to **Analysis > Create Parameter**.
2. Name it `Selected Year`.
3. Set the data type to **Integer**.
4. Input a list of years (2021, 2022, 2023, 2024).
5. Click OK.

### Step 2: Create YTD Sales Calculation
Now that the `Selected Year` parameter is defined, let’s adjust the calculation to avoid mixing aggregates.

1. **Remove the SUM() inside the IF condition**:
   Tableau's `IF` statement should handle row-level operations, and the aggregation (`SUM()`) should happen outside of the condition.

2. The corrected formula should look like this:

```tableau
IF YEAR([Date Time]) = [Selected Year] THEN [Sales_USD] END
```

This will give you the row-level data for the selected year.

### Step 3: Aggregate the Result
In Tableau, the aggregation happens after the `IF` condition. You can now place `YTD Sales` in your view and aggregate it using `SUM()`, either by dragging it into the columns/rows shelf or by creating another calculated field if needed.

For example:
```tableau
SUM(IF YEAR([Date Time]) = [Selected Year] THEN [Sales_USD] END)
```

### Step 4: Test and Apply
1. After creating the calculated field, you can test it by placing it in your rows/columns or within your visualizations.
2. Use the **Parameter Control** to dynamically switch between years and see how the `YTD Sales` value updates.

Let me know if this resolves the issue or if you need further clarification!





------------








------------
Thank you for the clarification! Let's work on a more flexible approach so that you can compare any year with its previous year dynamically. I'll guide you through building a more dynamic solution where you can switch between years (e.g., 2024 vs 2023, 2023 vs 2022, etc.).

### Steps to Build a Flexible `YTD vs PYTD` Comparison

1. **Prepare the Data:**
   - Ensure that your dataset includes a `Date` field and `Sales_USD` field. The data should be structured with one row per transaction (or summary), including columns for `Year`, `Month`, and `Sales`.

2. **Create Calculated Fields for YTD and PYTD:**

   To make this flexible, we need to create calculated fields for the **current year** and **previous year** dynamically.

   **a. YTD Sales Calculation:**
   This will calculate the Year-To-Date (YTD) sales for the selected year.
   - Go to **Analysis > Create Calculated Field** and name it `YTD Sales`.
   - Use the following formula:
     ```tableau
     IF YEAR([Date Time]) = [Selected Year]
     THEN SUM([Sales_USD])
     END
     ```

   **b. PYTD Sales Calculation:**
   This will calculate the Previous Year-To-Date (PYTD) sales dynamically.
   - Create another calculated field and name it `PYTD Sales`.
   - Use the following formula:
     ```tableau
     IF YEAR([Date Time]) = [Selected Year] - 1
     THEN SUM([Sales_USD])
     END
     ```

3. **Create a Parameter to Select Year:**
   To allow flexibility in selecting different years to compare:
   - Go to **Analysis > Create Parameter**.
   - Name the parameter `Selected Year`.
   - Set the data type to **Integer** and input a list of years (2021, 2022, 2023, 2024).
   - Click OK.

4. **Create YTD vs PYTD Difference Calculation:**
   Now, let's create a field that calculates the difference between the selected year's YTD and the previous year's PYTD.
   - Create another calculated field called `YTD vs PYTD Difference`.
   - Use the following formula:
     ```tableau
     SUM(IF YEAR([Date Time]) = [Selected Year] THEN [Sales_USD] END)
     -
     SUM(IF YEAR([Date Time]) = [Selected Year] - 1 THEN [Sales_USD] END)
     ```

5. **Create the Rank for Bottom 10:**
   To rank the countries based on the `YTD vs PYTD Difference`, create a calculated field for the rank.
   - Create another calculated field and name it `Rank by YTD vs PYTD Difference`.
   - Use the following formula:
     ```tableau
     RANK_UNIQUE(SUM([YTD vs PYTD Difference]), 'asc')
     ```

6. **Set Up the Visualization:**
   Now that the calculations are in place, you can create your visualizations.

   **a. Bar Chart for Bottom 10 Countries:**
   - Drag `Country` to **Rows** and `SUM([YTD vs PYTD Difference])` to **Columns**.
   - Apply the **Rank by YTD vs PYTD Difference** filter, setting the range from `1` to `10`.

   **b. Apply Filters and Parameters:**
   - Add the `Selected Year` parameter to your view to allow switching between years.
   - You can also show the parameter control by right-clicking the parameter and selecting **Show Parameter Control**.

7. **Dashboard Flexibility:**
   Now, you have a flexible setup that allows you to:
   - Compare any year to the previous year by selecting the year dynamically through the `Selected Year` parameter.
   - Display the bottom 10 countries ranked by the difference in `YTD vs PYTD`.

### Final Steps for Flexibility:
- Ensure that all calculations are based on the dynamically selected year.
- Test the parameter by switching between different years and observing how the `YTD vs PYTD Difference` and the ranks change accordingly.

This setup should allow you to compare different years flexibly, as well as filter for the bottom 10 countries dynamically. Let me know how it goes and if you need further clarification!
