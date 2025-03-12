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

### 1. Normalizer Approach

#### Overview
This document explains the Normalizer approach used to split date ranges in an Informatica mapping. The approach involves various transformations to achieve the required data transformation while handling limitations.

#### Pipeline Flow
![Normalizer Flow](images/Date_Split_Normalizer_Mapping.png)

#### Step-by-Step Transformation Details

1. **Source Definition (flat_date2 - Flat File)**
   - Reads input data from a flat file.
   - Ensure to configure the file source properties correctly, specifying the `date format` used in the input file.

2. **Source Qualifier (SQ_flat_date2)**
   - Converts the source data into native Informatica data types.

3. **First Expression Transformation (EXPTRANS)**
   - Converts `Start_Date` and `End_Date` fields from date to string format since the Normalizer transformation does not accept date types.
   - Calculates the difference in years between `Start_Date` and `End_Date` using an expression.
     
     ```
     # Years Difference
     ABS(TO_INTEGER(TO_CHAR(Start_Date,'YYYY'))-TO_INTEGER(TO_CHAR(End_Date,'YYYY')))
     ```

4. **Normalizer Transformation (NRMTRANS)**
   - Uses the **occurrence** property to generate multiple records.
   - **Limitation:** If the occurrence is set to 50 and the `Years_Diff` is more than or equal to 50, this approach will not handle the excess records.

5. **Second Expression Transformation (EXPTRANS1)**
   - Converts `Start_Date` and `End_Date` back to date format from string.
   - Generates a sequence number for each record.
  
     ```
     # Squence Number (v_Seq)
     IIF(ID=previous_ID,v_seq+1,1)
     ```

6. **Filter Transformation (FILTRANS)**
   - Filters out unnecessary records based on the `Years_Diff` value, keeping only the required rows for further processing.

     ```
     # Filter Condition
     o_seq<=Years_Diff+1
     ```

7. **Third Expression Transformation (EXPTRANS2)**
   - Transforms the `New_Start` and `New_End` dates as per the required logic.

     ```
     # New Start Date
     IIF(o_seq>1  AND o_seq<=Years_Diff+1,TRUNC(ADD_TO_DATE(Date_ST,'YYYY',o_seq-1),'YYYY'),Date_ST)

     # New End Date
     IIF(o_seq<Years_Diff+1,LAST_DAY(ADD_TO_DATE(TRUNC(ADD_TO_DATE(Date_ST,'YYYY',o_seq-1),'YYYY'),'MM',11)),Date_ET)
     ```

8. **Target Definition (FLAT_DATE21 - Oracle)**
   - The final processed records are written to the target table.

### Key Considerations
- **Normalizer Limitation:** The occurrence value must be carefully chosen to avoid losing records with `Years_Diff` exceeding the threshold.
- **Date Handling:** String conversion of date fields is necessary before and after the Normalizer transformation to maintain proper data format.
- **Filtering Logic:** Ensure the filter transformation retains only the necessary records to optimize performance and avoid unnecessary data processing.

This approach provides a structured way to split date ranges while handling multiple occurrences and transformations effectively.

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
 ├── 📂 Mappings            # XML export files of Informatica mappings
 ├── 📂 Workflows           # XML export files of Informatica workflows
 ├── 📂 Images              # Screenshots of mappings, workflows, and results
 ├── 📂 SQL_Scripts         # SQL scripts used in the stored procedure and SQL approach
 ├── 📂 Source_Data         # Sample source data files 
 ├── 📂 Target_Data         # Expected target data files
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
