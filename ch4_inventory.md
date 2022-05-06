# Inventory

## Value Chain
**Value chain** - natural, logical flow of an organization's primary activities. How do the company deliver value to customers?
- operational source systems produce transactions/snapshot at each step of the value chain
- within each step, performance metrics can be tracked
- each process spawns one or more fact tables
- the value chain can usually provide high-levl insight into the overall data architecture for an enterprise DW/BI environment

## Inventory Models

3 Models are discussed around inventory:

1. periodic snapshot model
2. inventory transaction model
3. inventory accumulating snapshot

### Inventory Periodic Snapshot


**Business Context**

- what is the optimized inventory level?
- what is the daily inventory level by product and store?

**4 step dimensional design**
1. Business process: periodic snapshotting of retail store inventory
2. Granularity: daily inventory for each product in each store
3. Dimensions: date, product, and store

