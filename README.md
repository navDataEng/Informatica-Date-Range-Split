# Informatica Date Range Split

## Overview
This repository demonstrates multiple approaches to transforming date ranges into yearly records using **Informatica PowerCenter**. The solution takes an input file containing `ID, Start_Date, End_Date` and generates records by breaking the date range into yearly segments.

## Input File Format
Example:
```
ID,Start_Date,End_Date
106,02/21/2022,05/15/2025
107,06/19/2023,04/26/2024
108,08/24/2018,03/27/2024
109,04/16/2002,06/13/2015
110,05/26/2024,06/13/2024
```

## Expected Output Format
Example:
```
106    21-FEB-22    31-DEC-22
106    01-JAN-23    31-DEC-23
106    01-JAN-24    31-DEC-24
106    01-JAN-25    15-MAY-25
107    19-JUN-23    31-DEC-23
107    01-JAN-24    26-APR-24
...
```
## **Approaches Implemented**  

This repository demonstrates three different approaches to transforming date ranges into yearly records using **Informatica PowerCenter**. Each approach varies in implementation, leveraging different components to generate and process the required records efficiently.  

### **1. Informatica-Only Approach (Normalizer + Expression Transformation)**  
- Used **Normalizer Transformation** to generate multiple records for each date range.  
- Applied **Expression Transformation** to compute and adjust `Start_Date` and `End_Date` for each yearly split.  
- Filtered out unnecessary records based on the difference between the `Start_Date` and `End_Date`.  

### **2. SQL Query Approach (SQL Transformation)**  
- Wrote an SQL query to handle the expansion of date ranges into yearly records.  
- Used **SQL Transformation** in **Informatica PowerCenter** to execute this query dynamically.  
- Passed required field values from the source file to the transformation and directed the output to the target table.  

### **3. Stored Procedure Approach (Recursive CTE + Temp Table)**  
- Developed a **Stored Procedure** using **Recursive CTE** to generate yearly records from the given date range.  
- Called this procedure from **Informatica PowerCenter** within the mapping flow.  
- The procedure processed and inserted the transformed data into a **temporary table**, which was then read and written to the target table in the next flow.  

## Folder Structure
```
📂 Informatica-Date-Range-Split
 ├── 📂 Mappings            # XML files of Informatica mappings
 ├── 📂 Workflows           # XML exports of Informatica workflows
 ├── 📂 SQL_Scripts         # SQL scripts used in the stored procedure approach
 ├── 📂 Sample_Input        # Sample input files
 ├── 📂 Sample_Output       # Expected output files
 ├── README.md              # Documentation
```

## Setup & Execution
### **Prerequisites**
- Informatica PowerCenter installed.
- Access to a database (for the stored procedure approach).

### **Steps to Run**
1. **Clone the repository**  
   ```sh
   git clone https://github.com/your-username/Informatica-Date-Range-Split.git
   ```
2. Import the **Mappings** and **Workflows** into Informatica PowerCenter.
3. Update the source and target connections.
4. Run the workflow and verify the output.

## License
This project is open-source under the MIT License.
