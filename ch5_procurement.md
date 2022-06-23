# Chapter 5 Summary
Techniques for handling dimension table attribute value changes.

## Case
Multiple vendors, thus multiple procurement transactions.

## Single Versus Multiple Transaction Fact Tables
1. Business users describe different procurement process in different ways
2. The source systems for procurement processes vary by processes and vendors
3. Different transaction type would lead to different dimensionality

### Decision Making Considerations
- what are users' requirements?
- are there multiple busienss processes?
- are multiple source systems involved (with unique granularities)?
- what is dimensionality of thef acts?

**If all yes to above, multiple transacion fact tabls should be implemented**

## Would multiple fact table increase the complexity of ETL process?
- NO. It will actually simplify the ETL activities.
- Because once the different processes are segregated, compared to consolidating them together, the whole process will rerquire simpler ETL process.

## Always Consider COmplementary Procurement Snapshot
- an accumulating snapshot is meant to model process with well-defined milestones
- if process is a continuous flow that never really ends: then not a good candidate for accumulating snapshot

## Slowly Changing Dimension Basics

### Type 0: Retain Original
Dimension attribute value never changes, so facts are always group by its original value.
- appropriate for any attribute labelled `original `
  e.g. customer credit score
- also applies to most attributes in a date dimension

### Type 1: Overwrite
Overwrite old attribute value in the dimension row, replacing with current value. Hence attribute always reflects most recent assignment.

#### Pros and Cons
Pros
- simple
- fact table is untouched
Cons
- lose all history of attribute changes
- pre-existing aggregations based on old attribute will need to be rebult
Type 1 is only appropriate if the attribute change is an insignificant correction, and there is no value in keeping old dimension attributes.

### Type 2: Add New Row
Additional row in the dimensiontable with span of time: `effective_date`, `valid_from`, and `valid_to`.
The primary workhorse technique for accurately tracking slowly changing dimension attributes.
- the safest response if business is not certain about SCD
- the most recent record gets a DATE '9999-12-31' as the `valid_to` date
- create a new dimension row with new single-column primary key to uniquely identify new product profile and use it to join to the fact table

### Type 3: Add New Attribute

What if business users want to see the data as if the change never occurred against the change has been applied. Think of Financial reporting groups.
e.g. reorganizations
-  Type 2 response would not suppoet this requirement

In type 3:

- no need to issue a new dimensional row
- add a new column to capture attribute change
  e.g. `prev_dim_value`
- type 3 is not used quite often
- not useful for attributes that change unpredictably
- most appropriate when there is a significant change impact many rows in dimension table

### Type 4: Add Mini-Dimension
- Suitable for dimensions that changes more rapidly
- Type 4 allows for breaking off frequently changing attributes into a deparate dimension: i.e. creating a dimension table for every combination posibility
- Note a few:
  - continuously variable attributes are converted to banded ranges: attributes in mini-dimension forced to take on relatively small number of **discrete values**
  - every time a fact table row if built, 2 foreign keys related to  record will be included: one is for dimension key and another is for the mini-dimension key
 
 ## Hybrid Slowly Changing Dimension Techniques
 ### Type 5: Mini Dimension and Type 1 Outrigger
 Create an `outtrigger ` dimension key in a mini-dimension table so that the dimension table can refer to the mini-dimension values using that key. 
 SO the parimary dimension can join to the mini-dimension for the more frequent changing details.
 
 ### Type 6: Type 1 Attributes to Type 2 Dimension
 This allows the dimension table to have a validity tiem span columns `valid_from` and `valid_to` PLUS a current dimension value (so each row would have the same value pointing to the most updated value).
 - also has a current row indicator showing the status: `Expired`, `Current`
 
 ### Type 7: Dual Type 1 and Type 2 Dimensions
 Dual foreign keys created in the fact table linking to 2 dimension tables.
 - Dimension Table 1: the dimension table hosting a type 1: overwrite dimension (aka. current dimension)
 - Dimension Table 2: The added row Type 2 dimension table (a.k.a Dimension with effectiveness span)
 - delivers the same funcationality as Type 6
 - requires less ETL effort
 - incremental cost is additional column carried in the fact table
 - this gives users freedom to choose to join to either current dimension or dimension with historical attribute values vs. type 6 you join to more
 

