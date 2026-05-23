# PGMREC63 - COBOL Control Break Program

## Overview
**PGMREC63** is a COBOL program that implements a **control break processing system** (corte de control) to read customer records from a file, group them by customer type, and generate subtotals and grand totals for financial data.

## Purpose
This program processes customer financial data, grouping records by customer type and calculating running totals for:
- **Patrimony (Assets)** - Financial holdings
- **Debt (Liabilities)** - Outstanding obligations
- **Record counts** - Number of records processed per group and overall

## Key Features

### 1. **Control Break Logic**
The program monitors the customer type field (`WS-DATO-TIP-CL`) and triggers a "corte" (break/subtotal) whenever the customer type changes:
- Processes records while the customer type remains the same
- Accumulates subtotals for each group
- Displays subtotals when the type changes
- Resets counters for the next group

### 2. **Customer Types Supported**
The program recognizes five customer categories:
- **1A** - Resident customer in the country
- **1B** - Non-resident customer  
- **1C** - PYME (Small/Medium Enterprise)
- **1D** - Unicorn enterprise (high-growth startup)
- **1E** - Multinational enterprise

### 3. **Data Processing Workflow**

```
1. INITIALIZATION (1000-INICIO)
   ├─ Open input file CLIENTE
   ├─ Read first record
   └─ Store first customer type as reference

2. MAIN PROCESSING LOOP (2000-PROCESO)
   ├─ Compare current customer type with previous
   ├─ If same: accumulate subtotals
   ├─ If different: 
   │  ├─ Execute control break (2600-CORTE-EST)
   │  ├─ Display subtotals and customer type header
   │  └─ Reset counters
   └─ Read next record

3. READ OPERATION (2500-LEER)
   ├─ Read next record from file
   ├─ Check file status codes
   ├─ Count successful reads
   └─ Detect end-of-file (status 10)

4. CONTROL BREAK EXECUTION (2600-CORTE-EST)
   ├─ Display record count for current group
   ├─ Display subtotal patrimony
   ├─ Display subtotal debt
   ├─ Accumulate to grand totals
   ├─ Reset partial counters
   └─ Print next customer type header

5. FINAL PROCESSING (9999-FINAL)
   ├─ Close input file
   └─ Display grand totals (all records combined)
```

## Data Structure

### Input File (CLIENTE)
- **Record Format**: Fixed-length records (126 characters)
- **Contains**: Customer data including:
  - Customer type (`WS-DATO-TIP-CL`)
  - Patrimony amount (`WS-DATO-PATRIM`)
  - Debt amount (`WS-DATO-DEUDOR`)

### Working Storage Variables
- `WS-TOTAL-CORTE` - Record count for current group
- `WS-CORT-PATRIM-TOT` - Subtotal patrimony (packed decimal, 13 digits + 2 decimals)
- `WS-CORT-DEUDOR-TOT` - Subtotal debt (packed decimal, 13 digits + 2 decimals)
- `WS-GRAL-PATRIM-TOT` - Grand total patrimony
- `WS-GRAL-DEUDOR-TOT` - Grand total debt
- `WS-CANT-LEIDOS` - Total records read counter

## Output
The program displays:
1. **Initial header** showing first customer type classification
2. **Subtotal sections** each showing:
   - Number of records in group
   - Total patrimony (formatted with currency)
   - Total debt (formatted with currency)
   - Next customer type header
3. **Final summary** with:
   - Grand total patrimony across all groups
   - Grand total debt across all groups
   - Total records processed

## File Status Handling
- `'00'` - Successful read
- `'10'` - End-of-file reached (triggers final control break)
- `'Other'` - Error condition (sets return code to 9999)

## Configuration Notes
- **Decimal separator**: Comma (Spanish format - `DECIMAL-POINT IS COMMA`)
- **Record length**: Adjust `REG-CLIENTE PIC X(126)` if input file differs
- **File assignment**: `DDCLIEN` - Replace with actual dataset/file name
- Uses a COPY member `CPDATOCD` for data layout definition

## Return Codes
- `0` - Successful completion
- `9999` - Error encountered (file open/close/read issues)

## Dependencies
- `CPDATOCD` - COPY member containing the data record layout definition
- Input file `CLIENTE` assigned to `DDCLIEN`

## Notes for Maintenance
- This is a template program with comments suggesting customization
- The input file path/name should be updated in the FILE-CONTROL section
- The record length (126 characters) may need adjustment based on actual data layout
- The decimal mask format can be modified for different currency representations
