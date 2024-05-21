# Types of Load

## Initial Load
First (real) run to extract all data from all data sources.

- First initial extraction from source data
- After discussion with the business users + IT
	- What data is needed
	- When is a good time to load the data (Night? Weekends?)
	- Smaller extractions to test
- Initial Load to Core with Transformations
- After all the transformation steps have been designed
- Just done for all data from Staging (no filtering)

## Delta Load
Subsequent runs on only additional data from data sources.

- Incremental periodic Extraction / Load
- Delta column for every table

![[ETL Load DL 1.png]]Example: Transaction date, create_date, etc.

![[ETL Load DL 2.png]]Example: Incrementing number (Suitable primary key)
- Remember MAX(Sales_Key)
- MAX(Sales_Key) -> Variable X
- Next run: Sales_Key -> X

### What if there is no delta column?
- Some tools can capture automatically which data has been already loaded
- Just full load every time and compare the data with data that is already loaded
- Depending on the data volumes -> performance

# Load data from Staging to Core

![[ETL Load S2C 1.png]]
## Insert/Append record in Core
![[ETL Load S2C 2.png]]

## Update record in Core
![[ETL Load S2C 3.png]]
## Delete record from Core
![[ETL Load S2C 4.png]]
Typically we don't delete data