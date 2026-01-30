# Tableau Desktop Development

Data visualization and analytics using Tableau Desktop.

## Tech Stack

- **Application**: Tableau Desktop 2024.x
- **Data Sources**: SQL databases, Excel, CSV, cloud data
- **Calculations**: Tableau calculation language
- **Output**: Workbooks (.twb), Packaged (.twbx)

## Project Structure

- `workbooks/` - Tableau workbook files
- `data/` - Local data files
- `templates/` - Reusable templates
- `documentation/` - Data dictionaries, design docs

## Development Workflow

1. Connect to data source
2. Build data model
3. Create calculated fields
4. Design worksheets
5. Assemble dashboards
6. Test and validate
7. Package or publish

## Coding Guidelines

### Calculated Fields

#### Naming Standards
```
// Prefix by type
calc_Revenue_YTD       // Calculations
param_Date_Range       // Parameters  
set_Top_Customers      // Sets
grp_Product_Category   // Groups
```

#### Basic Calculations
```
// Simple arithmetic
[Sales] - [Cost]

// Conditional logic
IF [Region] = "West" THEN "Western Region"
ELSEIF [Region] = "East" THEN "Eastern Region"
ELSE "Other"
END

// CASE statement (preferred for multiple conditions)
CASE [Status]
    WHEN "Open" THEN 1
    WHEN "Pending" THEN 2
    WHEN "Closed" THEN 3
    ELSE 0
END
```

#### Date Calculations
```
// Current year filter
YEAR([Order Date]) = YEAR(TODAY())

// Date difference
DATEDIFF('day', [Start Date], [End Date])

// Date truncation
DATETRUNC('month', [Order Date])

// Rolling 12 months
[Order Date] >= DATEADD('month', -12, TODAY())
AND [Order Date] <= TODAY()
```

#### String Functions
```
// Concatenation
[First Name] + " " + [Last Name]

// Extract portion
LEFT([Product Code], 3)
MID([SKU], 4, 5)

// Clean data
TRIM(UPPER([Category]))

// Contains check
CONTAINS([Description], "Premium")
```

### LOD Expressions

```
// Customer-level metrics (ignoring viz granularity)
{ FIXED [Customer ID] : SUM([Sales]) }

// First purchase date per customer
{ FIXED [Customer ID] : MIN([Order Date]) }

// Add dimension to calculation
{ INCLUDE [Product] : AVG([Price]) }

// Remove dimension from calculation
{ EXCLUDE [Month] : SUM([Sales]) }

// Nested LOD
{ FIXED [Customer ID] : 
    COUNTD({ FIXED [Customer ID], [Order ID] : MIN([Product]) })
}
```

### Table Calculations

```
// Running sum
RUNNING_SUM(SUM([Sales]))

// Moving average (3 periods)
WINDOW_AVG(SUM([Sales]), -2, 0)

// Percent difference from previous
(SUM([Sales]) - LOOKUP(SUM([Sales]), -1)) / 
ABS(LOOKUP(SUM([Sales]), -1))

// Rank
RANK(SUM([Sales]))

// Percent of total
SUM([Sales]) / TOTAL(SUM([Sales]))

// Index for sorting
INDEX()
```

### Parameters

```
// Date range parameter usage
[Order Date] >= [Start Date Parameter]
AND [Order Date] <= [End Date Parameter]

// Top N parameter
RANK(SUM([Sales])) <= [Top N Parameter]

// Metric selector
CASE [Metric Selector]
    WHEN "Sales" THEN SUM([Sales])
    WHEN "Profit" THEN SUM([Profit])
    WHEN "Quantity" THEN SUM([Quantity])
END
```

### Sets

```
// Combined sets
[High Value Customers] AND [Recent Purchasers]

// Set action target
IF [Customer Set Action] THEN "Selected" 
ELSE "Not Selected" 
END
```

## Data Modeling

### Relationships
- Use relationships for multi-table models
- Define cardinality correctly
- Set referential integrity when known

### Joins (when needed)
- Prefer inner joins for clean data
- Use left joins to preserve records
- Filter before joining when possible

### Data Prep
- Clean data at source when possible
- Use Tableau Prep for complex transformations
- Document data transformations

## Performance Optimization

### Extract Best Practices
- Hide unused fields
- Aggregate to visible dimensions
- Filter unnecessary rows
- Use incremental refresh

### Calculation Efficiency
- Avoid nested LODs
- Use native functions over string parsing
- Boolean calculations over IF statements
- Context filters for large datasets

### Dashboard Performance
- Limit worksheet count
- Reduce marks count
- Use extract for complex calculations
- Enable query caching

## Dashboard Design

### Layout
- Consistent margins (8-16px)
- Aligned objects using containers
- Responsive sizing where appropriate

### Interactivity
- Filter actions for cross-filtering
- Highlight actions for context
- URL actions for drill-through
- Parameter actions for dynamic analysis

## Key Guidelines

1. Comment complex calculations
2. Use consistent naming conventions
3. Test with full data volumes
4. Optimize before publishing
5. Document data sources and logic
