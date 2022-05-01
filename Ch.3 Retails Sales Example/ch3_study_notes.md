# chapter 3: Retails Sales Study Notes
This chapter focuses on the build of a typical fact table by introducing the relationship between the built of a fact table to a business process (in this example, it is a retail sales business process). 

## Retail Store Business Case

### Business Summary
- large grocery chain with 100 grocery stores in 5 states
- each store sells different types of products (totalling ~6000 SKU on shelves)
** Data Collection**
- cash register: POS system that scan bar codes; customers receive a receipt upon transaction with list of item and total price charged
- back order system for vendor and delivery
**Business Problem**
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

**Advantage**
- means computed consistently in the ETL process
- eliminates the possibility of user calculation errors
- ensures all users and BI reporting applications refer to the derived fact consistently

**Disadvantage**
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

## Calendar date dimensions & time-of-day
## Casual dimensions
## Degenerate dimensions
## Nulls in a dimensional model
## Extensibility of dimensional models
## Factless fact tables
## Surrogate, natural, and durable keys
## Snowflaked dimension attributes
## Centipede fact tables with "too many dimensions"
