# ETL Customer Sales Demo

## Overview

This project demonstrates a complete ETL (Extract, Transform, Load) process developed by Mr Vivian Ferguson using SQL Server Integration Services (SSIS) to transform customer sales data from a source system into a dimensional data warehouse for reporting and analytics.

The solution showcases professional ETL development practices including data quality validation, error handling, audit logging, and integration with Power BI for visualisation.

## Architecture

```
External Source System
        ↓
   Source Table (Staging)
        ↓
    SSIS Package (ETL)
   ├── Data Quality Checks
   ├── Dimension Lookups
   ├── Calculated Fields
   └── Error Handling
        ↓
   Data Warehouse (Star Schema)
   ├── Dim_Customer
   ├── Dim_Product
   ├── Dim_SalesRep
   └── Fact_Sales
        ↓
   Power BI Dashboard
```

## Features

- ✅ **Star Schema Design** - Proper dimensional modelling with fact and dimension tables
- ✅ **Data Quality Validation** - Checks and transformations to ensure data integrity
- ✅ **Lookup Transformations** - Efficient dimension key lookups (CustomerKey, ProductKey, SalesRepKey)
- ✅ **Derived Columns** - Calculated fields and data transformations
- ✅ **Error Handling** - Comprehensive error logging and no-match row handling
- ✅ **Audit Logging** - Complete ETL execution tracking
- ✅ **Stored Procedures** - Reusable ETL logic for dimension loading
- ✅ **Power BI Integration** - Ready-to-use views for reporting
- ✅ **Automated ETL** - SSIS package for scheduled execution

## Technology Stack

- **SQL Server** - Database and data warehouse
- **SSIS (SQL Server Integration Services)** - ETL orchestration
- **Power BI** - Data visualisation and reporting
- **T-SQL** - Stored procedures and data transformations

## Project Structure

```
ETL_CustomerSales_Demo/
│
├── Package.dtsx                    # Main SSIS package
├── ETL_Demo_SQL_Setup.sql         # Database setup script
├── Documentation/
│   ├── ETL_Demo_README.md         # Detailed setup guide
│   ├── SSIS_Package_Guide.md      # SSIS configuration guide
│   └── Lookup_Configuration.md    # Lookup transformation guide
└── README.md                       # This file
```

## Prerequisites

- SQL Server (2016 or later)
- SQL Server Integration Services (SSIS)
- Visual Studio with SSDT (SQL Server Data Tools)
- Power BI Desktop (optional, for visualisation)

## Setup Instructions

### Step 1: Database Setup

1. Open **SQL Server Management Studio (SSMS)**
2. Connect to your SQL Server instance
3. Open `ETL_Demo_SQL_Setup.sql`
4. Execute the entire script
5. This creates:
   - Source staging table (`Source_CustomerSales`)
   - Dimension tables (`Dim_Customer`, `Dim_Product`, `Dim_SalesRep`)
   - Fact table (`Fact_Sales`)
   - Stored procedures for ETL logic
   - Audit logging table (`ETL_ExecutionLog`)
   - Power BI view (`vw_SalesSummary`)

### Step 2: Load Sample Data

```sql
-- Insert source data
INSERT INTO dbo.Source_CustomerSales 
    (CustomerCode, CustomerName, ProductCategory, ProductName, SaleDate, Quantity, UnitPrice, TotalAmount, Region, SalesRep)
VALUES
    ('CUST001', 'Acme Corporation', 'Electronics', 'Laptop Pro 15', '2024-01-15', 5, 1299.99, 6499.95, 'North', 'John Smith'),
    ('CUST002', 'Tech Solutions Inc', 'Electronics', 'Wireless Mouse', '2024-01-16', 25, 29.99, 749.75, 'South', 'Sarah Johnson'),
    -- ... (see ETL_Demo_SQL_Setup.sql for complete data)

-- Load dimensions
EXEC dbo.usp_LoadCustomers;
EXEC dbo.usp_LoadProducts;
EXEC dbo.usp_LoadSalesReps;
```

### Step 3: Configure SSIS Package

1. Open `Package.dtsx` in Visual Studio
2. Configure connection managers:
   - `SourceDB` - Points to source database
   - `DestinationDB` - Points to data warehouse database
3. Verify all components are connected correctly
4. See `SSIS_Package_Guide.md` for detailed configuration

### Step 4: Execute SSIS Package

1. Right-click package → **Execute Package**
2. Monitor execution in Progress tab
3. Verify data loaded into `Fact_Sales` table

### Step 5: Connect Power BI (Optional)

1. Open Power BI Desktop
2. **Get Data** → **SQL Server**
3. Connect to your database
4. Select `vw_SalesSummary` view
5. Create visualisations

## ETL Process Flow

### Control Flow Tasks

1. **Log_ETL_Start** - Logs package execution start
2. **Load_Customers** - Loads customer dimension
3. **Load_Products** - Loads product dimension
4. **Load_SalesReps** - Loads sales rep dimension
5. **Load_FactSales** - Executes data flow to load fact table
6. **Log_ETL_Success** - Logs successful completion

### Data Flow Transformations

1. **OLE DB Source** - Reads from `Source_CustomerSales`
2. **Derived Column** - Calculates `TotalAmount` if needed
3. **Lookup_CustomerKey** - Gets CustomerKey from Dim_Customer
4. **Lookup_ProductKey** - Gets ProductKey from Dim_Product
5. **Lookup_SalesRepKey** - Gets SalesRepKey from Dim_SalesRep
6. **OLE DB Destination** - Writes to `Fact_Sales`

## Database Schema

### Source Table
- **Source_CustomerSales** - Staging table for incoming data

### Dimension Tables (Star Schema)
- **Dim_Customer** - Customer dimension with CustomerKey
- **Dim_Product** - Product dimension with ProductKey
- **Dim_SalesRep** - Sales representative dimension with SalesRepKey

### Fact Table
- **Fact_Sales** - Sales transactions fact table

### Supporting Objects
- **ETL_ExecutionLog** - ETL process audit log
- **vw_SalesSummary** - Power BI-ready view

## Key Features Demonstrated

### 1. Dimensional Modelling
- Proper star schema design
- Surrogate keys (CustomerKey, ProductKey, SalesRepKey)
- Slowly changing dimensions

### 2. Data Quality
- Lookup transformations for data validation
- Error handling for unmatched rows
- Data type validation

### 3. ETL Best Practices
- Stored procedures for reusable logic
- Audit logging for tracking
- Error handling and recovery
- Performance optimisation (caching)

### 4. Automation
- SSIS package for scheduled execution
- SQL Server Agent job integration
- Parameterised packages

## Sample Data

The project includes 10 sample sales transactions covering:
- 5 unique customers
- 5 unique products
- 5 sales representatives
- Multiple regions
- Date range: January 2024

## Execution Logging

All ETL executions are logged in `ETL_ExecutionLog` table:
- Package name
- Task name
- Execution time
- Status (Running/Success/Failed)
- Rows processed
- Error messages (if any)

## Verification Queries

### Check Data Loaded
```sql
SELECT 'Source_CustomerSales' AS TableName, COUNT(*) AS RowCount FROM dbo.Source_CustomerSales
UNION ALL
SELECT 'Dim_Customer', COUNT(*) FROM dbo.Dim_Customer
UNION ALL
SELECT 'Dim_Product', COUNT(*) FROM dbo.Dim_Product
UNION ALL
SELECT 'Dim_SalesRep', COUNT(*) FROM dbo.Dim_SalesRep
UNION ALL
SELECT 'Fact_Sales', COUNT(*) FROM dbo.Fact_Sales;
```

### View Sales Summary
```sql
SELECT * FROM dbo.vw_SalesSummary ORDER BY SaleDate DESC;
```

### Check Execution Log
```sql
SELECT * FROM dbo.ETL_ExecutionLog 
ORDER BY ExecutionStartTime DESC;
```

## Documentation

Detailed documentation is available in the project:
- **ETL_Demo_README.md** - Complete setup and usage guide
- **SSIS_Package_Guide.md** - SSIS package configuration
- **Lookup_Configuration.md** - Lookup transformation details

## Author

**Mr Vivian Ferguson**

This project demonstrates professional ETL development skills including:
- SQL Server Integration Services (SSIS)
- Dimensional data modelling
- Data quality and validation
- Error handling and logging
- End-to-end BI solutions

## Portfolio Highlights

This project showcases:
- ✅ Complete ETL pipeline development
- ✅ Star schema dimensional modelling
- ✅ SSIS package design and implementation
- ✅ Data quality and error handling
- ✅ Professional documentation
- ✅ End-to-end BI solution (ETL → Data Warehouse → Power BI)

## License

This project is for portfolio and demonstration purposes.

## Acknowledgements

Developed by **Mr Vivian Ferguson** as part of a comprehensive ETL and data warehousing demonstration project.

---

**Note:** This project is designed for educational and portfolio purposes to demonstrate ETL development capabilities.
