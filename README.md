**Course:** INSY 8311 - Database Development with PL/SQL  
**Student:** Umunyana Celine| 29588| Group A  
**Instructor:** Eric Maniraguha  


 
**Business Problem**



**Business Context**

The organization is a national logistics and delivery company operating
in the transport and supply chain industry.
The analysis is conducted for the Operations and Performance
Analytics Department.

**Data Challenge**
 
The company handles thousands of package deliveries across multiple
regions and drivers, but lacks clear insight into delivery performance,
inactive drivers, unused delivery routes, and delivery trends over time.
Management finds it difficult to identify top-performing routes, delayed
deliveries, and changes in delivery volume across months.

**Expected Outcome**

The analysis aims to generate insights on delivery efficiency, driver
activity, route utilization, and delivery trends, supporting better route
planning, workforce management, and service optimization.


Success Criteria

The analysis will achieve exactly five measurable objectives:
1. Identify the top 5 delivery routes per month using RANK()
2. Calculate running monthly delivery totals using SUM() OVER()
3. Measure month-over-month delivery growth using LAG()
4. Segment drivers into four performance quartiles using NTILE(4)
5. Compute three-month moving averages of deliveries using AVG()
OVER()

ENTITY RELATIONSHIP DIAGRAM 

![WhatsApp Image 2026-02-08 at 17 45 48](https://github.com/user-attachments/assets/b85a5c33-31a0-474e-93d8-2856b618e5da)


### Tables
1. **Drivers Table** 
2. **Routes Table** 
3. **Deliveries Table**

### AS follow : 

 #### Drivers Table
```sql
CREATE TABLE drivers (
driver_id INT PRIMARY KEY,
full_name VARCHAR(100) NOT NULL,
region VARCHAR(50),
hire_date DATE
);
```

 #### Routes Table
```sql
CREATE TABLE routes (
route_id INT PRIMARY KEY,
route_name VARCHAR(100) NOT NULL,
distance_km DECIMAL(6,2)
);
```

 #### Deliveries Table
```sql
CREATE TABLE deliveries (
delivery_id INT PRIMARY KEY,
driver_id INT,
route_id INT,
delivery_date DATE,
packages_delivered INT,
CONSTRAINT fk_driver
FOREIGN KEY (driver_id)
REFERENCES drivers(driver_id),
CONSTRAINT fk_route
FOREIGN KEY (route_id)
REFERENCES routes(route_id)
);
```
**Part A: SQL JOINs**

**SQL JOINs Implementation**

 **1. INNER JOIN **
```sql
 SELECT
d.full_name,
r.route_name,
dl.packages_delivered,
dl.delivery_date
FROM deliveries dl
INNER JOIN drivers d ON dl.driver_id = d.driver_id
INNER JOIN routes r ON dl.route_id = r.route_id;
```

**Result:**![INNER JOIN](https://github.com/user-attachments/assets/ebd535f8-4ccc-450c-983a-dd062c210d0c)

Business Interpretation:
Shows completed deliveries used to analyze performance across drivers
and routes.

### 2. LEFT JOIN 
```sql
SELECT d.driver_id, d.full_name
FROM drivers d
LEFT JOIN deliveries dl ON d.driver_id = dl.driver_id
WHERE dl.delivery_id IS NULL;
```
**Result:** ![LEFT JOIN](https://github.com/user-attachments/assets/fdd1a093-0313-4c68-a690-8ec12d7de2b7)

Business Interpretation:
Inactive drivers may indicate training needs or staffing issues.

### 3. RIGHT JOIN 
```sql
SELECT r.route_name
FROM deliveries dl
RIGHT JOIN routes r ON dl.route_id = r.route_id
WHERE dl.delivery_id IS NULL;
```
**Result:**![RIGHT JOIN](https://github.com/user-attachments/assets/071e9f68-81eb-4d2e-a092-3f56c3ef98e3)

Business Interpretation:
Unused routes may require reassessment or operational adjustment.

### 4. FULL OUTER JOIN
```sql
SELECT d.full_name, r.route_name
FROM drivers d
FULL OUTER JOIN routes r
ON d.region = r.route_name;
```
**Result:** ![FULL OUTER JOIN](https://github.com/user-attachments/assets/1109fe60-8187-427f-aa92-27278b0ffd04)

Business Interpretation:
Highlights mismatches useful for strategic allocation analysis.

### 5. SELF JOIN
```sql
SELECT
a.driver_id AS driver_1,
b.driver_id AS driver_2,
a.delivery_date
FROM deliveries a
JOIN deliveries b
ON a.delivery_date = b.delivery_date
AND a.driver_id <> b.driver_id;
```
**Result:**![SELF JOIN](https://github.com/user-attachments/assets/7c97dfcc-ca82-499a-894b-724b7c425f0a)

Business Interpretation:
Allows comparison of driver workload during the same day

Window Functions Implementation

Ranking Functions
- ROW_NUMBER(): Track purchase sequences
 ```sql
SELECT    month,    route_id,    total_packages,    RANK() OVER (        PARTITION BY month        ORDER BY total_packages DESC    ) AS route_rank FROM (    SELECT        TO_CHAR(delivery_date, 'MM') AS month,        route_id,        SUM(packages_delivered) AS total_packages    FROM deliveries    GROUP BY        TO_CHAR(delivery_date, 'MM'),        route_id) t;

```
<img ![Ranking Functions](https://github.com/user-attachments/assets/4a46593d-dd9f-46cb-9a6d-bbaf67a8928d)

 Aggregate Window Functions
- Running totals using SUM()
 ```sql
  SELECT
delivery_date,
packages_delivered,
SUM(packages_delivered) OVER (
ORDER BY delivery_date
ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
) AS running_total
FROM deliveries;
 ```

<img ![Aggregate Window Functions](https://github.com/user-attachments/assets/b85b16bc-cd88-492d-9915-9f2ca797451f)


### Navigation Functions
Use Case: Month-over-month delivery growth.
 ```sql
 SELECT
delivery_date,
packages_delivered,
packages_delivered - LAG(packages_delivered)
OVER (ORDER BY delivery_date) AS delivery_growth
FROM deliveries;
 ```

<img ![Navigation Functions](https://github.com/user-attachments/assets/69a27ae9-f3e9-495e-8501-6c62c9a625a7)

### Distribution Functions
Use Case: Driver performance segmentation
 ```sql
SELECT
driver_id,
SUM(packages_delivered) AS total_packages,
NTILE(4) OVER (ORDER BY SUM(packages_delivered) DESC) AS
performance_quartile
FROM deliveries
GROUP BY driver_id;
 ```
<img ![Distribution Functions](https://github.com/user-attachments/assets/86668a84-d5e7-4949-a3c0-d49d41906fad)

Result analysis

Descriptive Analysis
Delivery volumes vary significantly by route and driver.
Diagnostic Analysis
Some drivers and routes remain underutilized, reducing efficiency.
Prescriptive Analysis
Reassign drivers, optimize routes, and reward top-performing drivers to
improve overall performance.



 References
 
A. PostgreSQL Window Functions Guide
Oracle SQL Analytical Functions Documentation

B. W3Schools SQL JOINs

C. Supply Chain Analytics Resources


Academic Integrity Statement

All sources were properly cited. Implementations and analysis represent original work. No AI-generated content was copied without attribution or adaptation.

**Signature:** Umunyana Celine



**Email:** umunyanaceline77@GMAIL.COM





