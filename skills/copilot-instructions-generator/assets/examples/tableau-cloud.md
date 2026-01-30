# Tableau Cloud Development

Analytics and visualization development for Tableau Cloud.

## Tech Stack

- **Platform**: Tableau Cloud
- **Languages**: Tableau calculations, SQL
- **Data Sources**: Live connections, Extracts
- **APIs**: REST API, Metadata API
- **Embedding**: Tableau Embedding API v3

## Project Structure

- `workbooks/` - Tableau workbook files (.twb, .twbx)
- `datasources/` - Published data sources (.tds, .tdsx)
- `scripts/` - Automation scripts
  - `publish/` - Publishing automation
  - `extract/` - Extract refresh scripts
- `documentation/` - Design docs, data dictionaries

## Development Workflow

1. Develop in Tableau Desktop
2. Test with sample data
3. Validate calculations
4. Publish to Development project
5. Review and test
6. Promote to Production

## Coding Guidelines

### Calculated Fields

#### Naming Conventions
- Prefix with type: `calc_`, `param_`, `set_`
- Use descriptive names: `calc_YTD_Revenue`
- Group related calcs: `Sales - YTD`, `Sales - QTD`

#### Best Practices
```
// Use comments for complex logic
// YTD Revenue: Calculates year-to-date revenue
IF YEAR([Order Date]) = YEAR(TODAY()) 
   AND [Order Date] <= TODAY()
THEN [Revenue]
END

// Use parameters for flexibility
IF [Date Granularity Parameter] = "Month" 
THEN DATETRUNC('month', [Order Date])
ELSEIF [Date Granularity Parameter] = "Quarter"
THEN DATETRUNC('quarter', [Order Date])
END
```

#### Performance
- Avoid nested IFs when CASE works
- Use FIXED LOD for pre-aggregation
- Minimize row-level calculations
- Use native date functions

### LOD Expressions
```
// FIXED - independent of viz dimensions
{ FIXED [Customer ID] : SUM([Revenue]) }

// INCLUDE - adds dimensions
{ INCLUDE [Product] : AVG([Price]) }

// EXCLUDE - removes dimensions
{ EXCLUDE [Region] : SUM([Sales]) }
```

### Table Calculations
```
// Running total
RUNNING_SUM(SUM([Sales]))

// Percent of total
SUM([Sales]) / TOTAL(SUM([Sales]))

// Year-over-year growth
(ZN(SUM([Sales])) - LOOKUP(ZN(SUM([Sales])), -1)) 
/ ABS(LOOKUP(ZN(SUM([Sales])), -1))
```

### Parameters
- Use for user input and interactivity
- Set appropriate data types
- Define allowable values when possible
- Document purpose in description

### Data Source Best Practices

#### Extracts
- Schedule refreshes appropriately
- Use incremental refresh when possible
- Filter unnecessary data
- Hide unused fields

#### Live Connections
- Optimize underlying queries
- Use custom SQL sparingly
- Ensure proper indexing
- Monitor query performance

### Dashboard Design

#### Layout
- Use consistent spacing and alignment
- Design for target screen size
- Use containers for responsive design
- Keep filters accessible

#### Performance
- Limit marks on screen
- Use context filters appropriately
- Minimize dashboard actions
- Optimize extract size

#### User Experience
- Clear, descriptive titles
- Intuitive filter placement
- Consistent color schemes
- Helpful tooltips

### Publishing

#### Permissions
- Use project-level permissions
- Follow least-privilege principle
- Document permission requirements

#### Naming
- Environment prefix: `DEV_`, `PROD_`
- Descriptive workbook names
- Version numbers if needed

## Tableau REST API

```python
import tableauserverclient as TSC

# Authentication
tableau_auth = TSC.PersonalAccessTokenAuth(
    token_name='token_name',
    personal_access_token='token_value',
    site_id='site_name'
)
server = TSC.Server('https://10ax.online.tableau.com')

# Publish workbook
with server.auth.sign_in(tableau_auth):
    project = server.projects.get_by_id('project_id')
    new_workbook = TSC.WorkbookItem(project.id)
    workbook = server.workbooks.publish(
        new_workbook, 
        'workbook.twbx',
        mode=TSC.Server.PublishMode.Overwrite
    )
```

## Testing Checklist

- [ ] Calculations return expected results
- [ ] Filters work correctly
- [ ] Dashboard actions function properly
- [ ] Performance is acceptable
- [ ] Data refreshes successfully
- [ ] Permissions are correct

## Key Guidelines

1. Document all calculated fields
2. Use extracts for better performance
3. Design mobile-friendly dashboards
4. Test with production-like data volumes
5. Follow naming conventions consistently
