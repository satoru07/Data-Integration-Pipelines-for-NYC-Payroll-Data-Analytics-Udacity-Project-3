# Data Integration Pipelines for NYC Payroll Data Analytics

## Overview

This project implements a comprehensive data integration pipeline using Azure Data Factory to process and analyze New York City payroll data. The solution ingests data from multiple sources, applies data transformation through dataflows, and loads the processed data into a SQL database for analytics and reporting.

## Project Architecture

The project is organized into five key components:

### Task 1: Resource Group & Database Setup (`Task1_RG`)
- **SQL_DB_Tables_Query.sql**: SQL schema definitions for the destination database
- Creates master tables for Employee, Title, and Agency dimensions
- Creates fact tables for payroll data (2020 and 2021)
- Creates summary table for aggregated payroll analytics

#### Tables Created:
- `NYC_Payroll_EMP_MD`: Employee master dimension table
- `NYC_Payroll_TITLE_MD`: Job title master dimension table
- `NYC_Payroll_AGENCY_MD`: Agency/department master dimension table
- `NYC_Payroll_Data_2020`: Payroll transactions for fiscal year 2020
- `NYC_Payroll_Data_2021`: Payroll transactions for fiscal year 2021
- `NYC_Payroll_Summary`: Aggregated payroll summary by agency and year

### Task 2: Linked Services (`Task2_LinkedService`)
Defines connections to data sources and destinations:
- **ls_SQLDB_nycpayroll.json**: Azure SQL Database connection to `project3sqldb_nycpayroll`
- **ls_adls_nycpayroll.json**: Azure Data Lake Storage connection for source data

### Task 3: Datasets (`Task3_DataSets`)
Defines the structure and location of data being integrated:

#### DataLake Storage Datasets:
- `AgencyMaster.json`: Agency dimension source file
- `EmpMaster.json`: Employee dimension source file
- `TitleMaster.json`: Job title dimension source file
- `nycpayroll_2020.json`: 2020 payroll data source file
- `nycpayroll_2021.json`: 2021 payroll data source file
- `ds_stagingTable.json`: Staging table for intermediate data

#### SQL Database Datasets:
- `NYC_Payroll_AGENCY_MD.json`: Agency dimension destination
- `NYC_Payroll_EMP_MD.json`: Employee dimension destination
- `NYC_Payroll_TITLE_MD.json`: Title dimension destination
- `NYC_Payroll_Data_2020.json`: 2020 payroll destination
- `NYC_Payroll_Data_2021.json`: 2021 payroll destination
- `NYC_Payroll_Summary.json`: Summary table destination

### Task 4: Data Flows (`Task4_dataFlows`)
Implements data transformation logic using Azure Data Factory Mapping Data Flows:
- `df_AgencyMaster.json`: Transforms agency master dimension data
- `df_EmpMaster.json`: Transforms employee master dimension data
- `df_TitleMaster.json`: Transforms job title master dimension data
- `df_nycpayroll_2020.json`: Transforms 2020 payroll data
- `df_nycpayroll_2021.json`: Transforms 2021 payroll data
- `df_summary.json`: Creates aggregated payroll summary with parameterized fiscal year

### Task 5: Pipeline Orchestration (`Task5_PipeLines`)
**project3_pipeline.json**: Main orchestration pipeline with the following flow:

#### Pipeline Activities:
1. **Master Data Loads** (Parallel Execution):
   - `Agency`: Loads agency master data
   - `Title`: Loads job title master data
   - `EmpMaster`: Loads employee master data

2. **Historical Data Loads** (After master data completion):
   - `2020`: Loads and transforms 2020 payroll data
   - `2021`: Loads and transforms 2021 payroll data

3. **Summary Generation** (After both years completed):
   - `Summary`: Generates aggregated payroll summary with fiscal year parameter

#### Compute Configuration:
- All data flows use 8-core General compute
- Trace level set to Fine for detailed monitoring
- 12-hour timeout per activity
- No retry logic configured

## Data Flow Architecture

```
Data Lake Storage
├── Agency Master
├── Employee Master
├── Title Master
├── Payroll 2020
└── Payroll 2021
        ↓
    Data Flows
    (Transform)
        ↓
   SQL Database
├── Agency MD
├── Employee MD
├── Title MD
├── Payroll 2020
├── Payroll 2021
└── Summary
```

## Key Features

- **Dimensional Modeling**: Implements a star schema with master dimensions and fact tables
- **Multi-Year Support**: Handles payroll data from multiple fiscal years (2020, 2021)
- **Parameterized Flows**: Summary dataflow accepts fiscal year as a parameter for flexibility
- **Parallel Processing**: Master data loads execute in parallel for efficiency
- **Dependency Management**: Proper task sequencing ensures data integrity
- **Azure-Native**: Leverages Azure Data Factory for serverless, scalable data integration

## Prerequisites

- Azure subscription with Data Factory instance
- Azure SQL Database instance
- Azure Data Lake Storage Gen2 account
- Source payroll data files in the data lake
- Appropriate SQL user credentials configured

## Database Connection Details

**Database Server**: `project3sqlsb-server.database.windows.net`  
**Database Name**: `project3sqldb_nycpayroll`

## Usage

1. Deploy the SQL schema from `Task1_RG/SQL_DB_Tables_Query.sql` to your SQL database
2. Configure linked services in Azure Data Factory with appropriate credentials
3. Import datasets, dataflows, and pipeline definitions
4. Upload source data files to the data lake
5. Trigger the pipeline to start the data integration process
6. Monitor pipeline execution and review data quality

## Data Insights

The pipeline enables analysis of:
- Employee compensation by agency and title
- Salary trends across fiscal years
- Overtime and regular pay distribution
- Work location analysis by borough
- Agency-level payroll expenditure summaries

## GitHub Repository

Repository: https://github.com/satoru07/Project3

## Author

Created as part of an Azure Data Integration and Analytics project.

## License

Please refer to the repository for license information.

---

**Last Updated**: January 2026  
**Version**: 1.0
