# chapter 3: Retails Sales Study Notes
This chapter focuses on the build of a typical fact table by introducing the relationship between the built of a fact table to a business process (in this example, it is a retail sales business process). 

## Retail Store Business Case

### Business Summary
- large grocery chain with 100 grocery stores in 5 states
- each store sells different types of products (totalling ~6000 SKU on shelves)

#### Data Collection
- cash register: POS system that scan bar codes; customers receive a receipt upon transaction with list of item and total price charged
- back order system for vendor and delivery

#### Business Problem
Management concerned with:
  - ordering logistics
  - stocking
  - profitability
    - pricing
    - promotions

## Four-Step Process for Designing Dimensional Models
1. **Selecting Business Process to model for**
  - business process are activities (can be described using action verbs)
  - numerous operating systems are used to help tracking these business activities
  - KPI and metrics are derived from these captured business process
  - think of business process as a flow system that takes inputs and delivers outputs to the next business process; 
    the series of processes results in a series of fact tables
2. **Declare the model's granularity**
  - at the lowest level, what does a single **fact table** row represent?
    e.g. 1 record in a fact table row represent a credit card transaction
  - **Develop dimensional models representing the most detailed atomic information captured by a business process**
    - **atomic data** is highly dimensional and most flexible
3. **Identify all the dimensions to be captured to the model**
  - how do we describe each record in the fact table?
  - who, what, where, when, why, how...etc of a single record
4. **Identify the facts associated with the chosen business process per step 1**
  - what does the chosen business process measure?
  - fact must be true to the grain

### Retail Store Dimensional Model Design using Four-Step Process
1. POS retail sales transactions chosen as the **business process**;
2. The most **granular data**: an individual product transaction line item on a POS transaction;
3. Date, product, store, promotion, cashier, and payment method are good candidates for fact table dimensions
4. Facts collected by POS:
   - datetime of transaction: **Date Key (FK)**
   - product sold: **Product Key (FK)**
   - store that process the transaction: **Store Key (FK)**
   - promotion given: **Promotion Key (FK)**
   - cashier that processed the transaction: **Cashier Key (FK)**
   - payment method: **Payment method key (FK)**
   - POS transaction unique identifier: **POS Transac. No. (DD)**
   - sales quantity: **numeric measure**
   - per unit price: **numeric measure**
   - discount: **numeric measure**
   - net paid prices: **numeric measure**
   - extended cost dollar amounts: **numeric measure**

## Additive, non-additive, and derived facts
### Additive Facts
Fact measures from each row record that can be added across the table to provide summary measures. (can be aggregated using SUM() to provide high-level total amounts.)
  - sales quantity
  - extended discount
  - sales
  - cost dollar amounts
  are additive facts for the retails store case

### Derived Facts
A fact derived from performing calculations based on existing fact. e.g. calculating gross profit that's derived from the existing extended cost dollar amounts and sale price.

#### Should you store a calculated derived fact in the database?
- generally recommended to be stored physically for **consistency**

#### Advantage
- means computed consistently in the ETL process
- eliminates the possibility of user calculation errors
- ensures all users and BI reporting applications refer to the derived fact consistently

#### Disadvantage
- potential storage cost and processing pressure

### Non-additive Facts
Fact measures from each row that cannot be added or summarized along any dimension.
- gross margin is calculated by **division**: gross profit / sales revenue
- unit price is price per unit cannot be summed across any dimensions
Percentages and ratios are always non-additive. The numerator and denominator should be stored in the fact table. The ratio can then be calculated in BI tool for any slice of the fact table. Notice they should be calculated as **ratio of the sums** not the sum of ratios.

### Transaction Fact Tables
- the most common type of fact table
- characteristics:
  1. atomic grain: 1 row per transaction line
  2. they tend to be highly dimensional
  3. metrics resulted from the events are typically additive as long as they have been **extended** by quantity amount rather than per unit metrics
### Estimate Number of Rows
- very important to evaluate at early stage of design
- estimate number of new rows added per year: annual gross revenue / average item selling price

## Dimension attributes: indicators, numeric descriptions, and multiple hierarchies

### Date Dimension
- dimensional models always need an explicit date dimension table
- every business process captures a time series of performance metrics
- date dimension can be built in advance with each row representing a particular day
  - 20 years worth of date dimensions ~ 7300 rows
- a date dimension is needed b.c it allows for flexible filtering to be applied on the data
  - SQL functions do not allow filtering by weekdays, weekends, holidays, fiscal periods etc.
#### Set Meaningful and Self-explanatory Flags and Indicators
-  date dimension's holiday indicator has 2 potential values (boolean)
-  always think about what **values** would the flag/indicator serve
   - need to give **meaningful values** even though it is a boolean
   - `Y/N` vs. `Holiday/ Non-Holiday`
   - meaning ful domain translate indicator into a more self-explanatory report
   - you do not have to spend time decode the meaning of the boolean/ flag values
#### Relative Date Attributes
- most of dimension attributes are not subject to **frequent updates**
- some will change over time relative to the passage of time
  - is_current_day / is_current_month
  - is_prior_60_days
  - unique corporate financial reporting calender e.g. is_fiscal_month_end
- some date dimensions should include updated **lag attributes**
  - 0: today; +1: tomorrow; -1: yesterday
  - recommends **computed column** rather than physically stored
  - if BI tools automatically performas this calculation, then no need to add lag columns

#### Time-of-day should be captured as a Fact using timestamps
- time-of-day typically separated from date dimension to avoid row count explosion in date dimension
- date dimension should be kept small and manageable
- think of granularity: 
  - date granular: 7000 rows for each day in 20 years
  - *1440 mins per day if granularity at minutes
- time-of-day should be handled as a simple date/time fact in the fact table

## Product Dimension

- describes every SKU in grocery store
- sourced from operational product master file

### Flatten Many-to-one Hierarchies
Typical merchandise hierarchy contains:
- individual SKUs >> brands >> categories >> departments
- each of the roll-up is a **many-to-one** relationship 
- no need to seoarate repeated values into a second normalized table to save space (b.c vs fct tables, they are relatively small)
- **keep the repeated low cardinality values in primary dimension is a fundamental modeling technique**
- some attribute do not necessarily form any hierarchy e.g. `packaging_type`, b.c. any row can have one of these values

### Attributes with Embedded Meaning
Natural key identifiers (e.g. SKUs) can contain different parts in the code, with each part carrying different meaning. These multi parts in the natural key identifiers should be:
- have the entire code be preserved as a unique identifier
- the parts broken down into component parts representing different attributes

### Numeric values as Attributes or Facts

1. Dimension tables should be consistent
2. Data involved in calculations should be in fact tables
3. Data involved in constraints, groups, and labels should be in dimension tables

- Would the numeric value be used primarily for calculation purpose? Yes: fact table; No: dim table
- Would the numeric value used more for grouping or filtering? Yes: dimtable; No: fact table

Sometimes, numerci values can serve calculation and filtering purpose.
- store value in both fact and dimension tables

Date Elements are used both for fact calculations and dimension constrainin and should be stored in both locations.

### Drilling Down on Dimension Attributes
More dimenions adds more capabilities for users to group and filter their analysis and hierarchies.

## Store Dimension
Describes every store in grocery chain. Notice that, compared to product dimensions, each store may have different source file.

### Multiple Hierarchies in Dimension Tables

## Casual dimensions
## Degenerate dimensions
## Nulls in a dimensional model
## Extensibility of dimensional models
## Factless fact tables
## Surrogate, natural, and durable keys
## Snowflaked dimension attributes
## Centipede fact tables with "too many dimensions"
