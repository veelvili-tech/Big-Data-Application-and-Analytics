# Big-Data-Application-and-Analytics

# 🌍 Big Data Analysis — Greenhouse Gas Emissions
## WQD7009 Big Data Application and Analytics | University Malaya

---

## 📌 Overview
End-to-end big data pipeline analysing annual greenhouse gas (GHG) 
emissions across 6 regions and 10 industries using Apache Hive and 
HBase on a Cloudera Linux environment. The dataset was sourced from 
the IMS Climate Change Dashboard and covers emissions data for 2022 
and 2023.

---

## 🛠️ Tech Stack
- **Apache Hive** — data staging, table creation, SQL queries
- **Apache HBase** — NoSQL storage, DDL & DML shell commands
- **Cloudera** — Linux-based cluster environment (Quickstart VM)
- **Excel** — data preprocessing and cleaning
- **Hadoop / HDFS** — underlying distributed storage

---

## 📂 Dataset
| Attribute | Description |
|---|---|
| Object ID | Unique record identifier |
| Country | 6 regions: Africa, Asia, ANZ, Eastern Asia, Europe, Northern America |
| Gas Type | CO2, CH4, N2O, Fluorinated Gases (FG) |
| Industry | 10 sectors including Agriculture, EnergyCS, Manufacturing, Mining etc. |
| Y_2022 | Emissions in 2022 (Million metric tons CO₂ equivalent) |
| Y_2023 | Emissions in 2023 (Million metric tons CO₂ equivalent) |

- **Original dataset:** 1,186 records, 27 attributes
- **After preprocessing:** 223 records, 5 key attributes

---

## 🔄 Workflow — Step by Step

### Part A — Data Preparation
- Chose the annual greenhouse gas emissions dataset from the IMS Climate Change Dashboard
- Cleaned and narrowed down to 5 key attributes: Object ID, Country, Gas Type, Industry, and emissions for 2022 & 2023
- Renamed columns for consistency (e.g. "Carbon Dioxide" → CO2, "Australia and New Zealand" → ANZ)
- Pre-processed and validated data in Excel before importing into Cloudera
- Final dataset: **223 records across 5 attributes**

---

### Part B — Apache Hive
- Created a structured Hive table called `GreenHouseGas` to organise and stage the data
- Loaded the cleaned CSV data into the Hive table using `LOAD DATA LOCAL INPATH`
- Created a mapping table `GreenHouseGasAnnual` in both Hive and HBase using HBase Storage Handler
- Inserted data from `GreenHouseGas` into `GreenHouseGasAnnual` as part of the Hive → HBase pipeline
- Used Hive as a **staging layer** before the data was imported into HBase for NoSQL querying
```sql
-- Example: Create Hive Table
CREATE TABLE GreenHouseGas (
  Object_ID INT, Country STRING, Industry STRING,
  Gas_Type STRING, Y_2022 INT, Y_2023 INT
)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
STORED AS TEXTFILE;
```

---

### Part C — HBase Shell Commands

6 HBase shell commands demonstrated:

| Command | Purpose |
|---|---|
| `LIST` | View all tables in HBase — confirmed GreenHouseGasAnnual exists |
| `WHOAMI` | Checked current user — displayed 'cloudera' as default user |
| `STATUS` | Checked cluster health — 1 active master, 1 server |
| `VERSION` | Retrieved HBase version — 1.2.0-cdh5.10.0 |
| `TABLE_HELP` | Viewed available table operation commands |
| `COUNT` | Counted rows — confirmed **223 records** loaded successfully |
```bash
# Example
hbase(main):004:0> count 'GreenHouseGasAnnual'
223 row(s) in 0.5690 seconds
=> 223
```

---

### Part D — HBase DDL & DML Commands

**5 Data Definition Language (DDL) Commands:**

| Command | Purpose |
|---|---|
| `DESCRIBE` | Viewed table structure — column family 'cf' confirmed |
| `DISABLE` | Disabled the table to prevent modifications |
| `ENABLE` | Re-enabled the table to allow modifications |
| `DROP` | Deleted the GreenHouseGasAnnual table from HBase |
| `EXIST` | Confirmed the table no longer exists after DROP |

**10 Data Manipulation Language (DML) Commands:**

| Command | Purpose |
|---|---|
| `GET` | Verified individual records match the original CSV file |
| `PUT` | Inserted a new summary record (row 224) — total emissions across all countries, gas types, and industries |
| `APPEND` | Added GHG indicator to row 224 (All_Gas_Type = summation of all 4 gas types) |
| `SCAN (a)` | Queried CO2 emissions for Asia — 10 records returned |
| `SCAN (b)` | Queried EnergyCS industry records for Asia |
| `SCAN (c)` | Filtered CO2 + EnergyCS + Asia — confirmed emissions increased from 2022 to 2023 |
| `SCAN (d)` | Viewed CO2 emissions for 2022 across all countries |
| `SCAN (e)` | Viewed all gases emitted in 2022 (highest emission year) |
| `SCAN (f & g)` | Queried ANZ records — confirmed least emitting region, largest gas is CO2 |
| `DELETEALL` | Removed 24 Ind_HH rows as they represented totals (summation of all industries), not individual sectors — final record count: **200** |
```bash
# Example: SCAN with filter
hbase(main):029:0> scan 'GreenHouseGasAnnual', {
  FILTER => "(SingleColumnValueFilter('cf','Gas_Type',=,'binary:CO2'))
  AND (SingleColumnValueFilter('cf','Country',=,'binary:Asia'))"
}
```

---

## 📊 Key Findings

| Finding | Detail |
|---|---|
|  Largest emitted gas | **Carbon Dioxide (CO2)** — in both 2022 and 2023 |
|  Largest CO2 producing region | **Asia** |
|  Biggest industry contributor in Asia | **Energy sector (EnergyCS)** — emissions increased from 2022 to 2023 |
|  Least emitting region | **Australia & New Zealand (ANZ)** |
|  Least emitted gas | **Fluorinated Gases (FG)** — mainly from Manufacturing industry |
|  Least CO2 contributing industry | **Water supply, sewerage & waste management (WWS)** |

---

## 💡 Skills Demonstrated
- Linux terminal operations on a **Cloudera cluster environment**
- **Apache Hive** — table creation, data loading, SQL-based querying
- **Apache HBase** — full DDL & DML command execution (15 commands total)
- End-to-end **big data pipeline**: CSV → Excel → Hive → HBase
- **Data cleaning and preprocessing** for large unstructured datasets
- **Analytical querying** and insight generation from NoSQL databases
- Understanding of **distributed storage** concepts (HDFS, Hadoop)
