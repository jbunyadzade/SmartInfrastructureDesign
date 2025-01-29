# **Reports Database Schema**

The **Reports Database** serves as the primary storage for **processed infrastructure data**, tracking the **health of structures** and **maintenance history**, rather than individual sensors. It is optimized for **fast retrieval of aggregated reports, alerts, and maintenance records** that are used in the **Dashboard App**.

---

## **1. `structures` (Primary Table for Infrastructure Elements)**

This table stores details about physical structures (e.g., bridges, roads, pipelines) being monitored.

| Column Name         | Data Type      | Description                                                             |
| ------------------- | -------------- | ----------------------------------------------------------------------- |
| `structure_id`      | `UUID` (PK)    | Unique identifier for the structure.                                    |
| `name`              | `VARCHAR(255)` | Name of the structure.                                                  |
| `type`              | `VARCHAR(50)`  | Type of infrastructure (`bridge`, `road`, `pipeline`, `dam`, etc.).     |
| `region_id`         | `INTEGER`      | Unique identifier for the region, used for sharding.                    |
| `location`          | `GEOMETRY`     | Geospatial coordinates.                                                 |
| `installation_date` | `DATE`         | When the structure was commissioned.                                    |
| `status`            | `VARCHAR(20)`  | Current operational status (`operational`, `under_repair`, `critical`). |

**Indexes:**

-   **Primary Key**: `structure_id`.
-   **Index on `region_id`** to optimize queries and support sharding.

---

## **2. `reports` (Aggregated Sensor Data per Structure)**

Stores **processed data** that the **Worker Service** generates from raw sensor inputs.

| Column Name        | Data Type     | Description                                                 |
| ------------------ | ------------- | ----------------------------------------------------------- |
| `report_id`        | `UUID` (PK)   | Unique identifier for the report.                           |
| `structure_id`     | `UUID` (FK)   | The structure associated with this report.                  |
| `region_id`        | `INTEGER`     | Region ID, used for sharding and optimized retrieval.       |
| `metric`           | `VARCHAR(50)` | Type of metric (e.g., stress load, vibration, temperature). |
| `aggregated_value` | `FLOAT`       | Aggregated value (average, min, max).                       |
| `aggregation_type` | `VARCHAR(20)` | Type of aggregation (`average`, `max`, `min`).              |
| `timestamp`        | `TIMESTAMP`   | When the report was generated.                              |
| `status`           | `VARCHAR(20)` | Condition assessment (`healthy`, `warning`, `critical`).    |
| `anomaly_detected` | `BOOLEAN`     | Whether an anomaly was detected.                            |

**Indexes:**

-   **Index on `structure_id`** for fast retrieval of reports.
-   **Index on `region_id`** to optimize region-based queries and support sharding.
-   **Index on `timestamp`** for time-series queries.

---

## **3. `maintenance_logs` (Records Past and Upcoming Repairs)**

Tracks **repair history** for structures rather than sensors.

| Column Name            | Data Type      | Description                                                    |
| ---------------------- | -------------- | -------------------------------------------------------------- |
| `log_id`               | `UUID` (PK)    | Unique identifier for the maintenance log.                     |
| `structure_id`         | `UUID` (FK)    | The structure undergoing maintenance.                          |
| `region_id`            | `INTEGER`      | Region ID, used for sharding and optimized retrieval.          |
| `maintenance_type`     | `VARCHAR(100)` | Type of maintenance (`inspection`, `repair`, `reinforcement`). |
| `performed_by`         | `VARCHAR(100)` | The engineer responsible for the work.                         |
| `description`          | `TEXT`         | Details of what was done.                                      |
| `timestamp`            | `TIMESTAMP`    | When the maintenance took place.                               |
| `next_maintenance_due` | `TIMESTAMP`    | When the next maintenance is scheduled.                        |

**Indexes:**

-   **Index on `structure_id`** to track repairs on each structure.
-   **Index on `region_id`** to support sharding.
-   **Index on `next_maintenance_due`** for scheduling upcoming work.

---

## **4. `alerts` (Stores Critical Issues for Dashboard Alerts)**

Stores **active alerts** that notify engineers about **critical structural issues**.

| Column Name    | Data Type     | Description                                                    |
| -------------- | ------------- | -------------------------------------------------------------- |
| `alert_id`     | `UUID` (PK)   | Unique identifier for the alert.                               |
| `structure_id` | `UUID` (FK)   | The affected structure.                                        |
| `region_id`    | `INTEGER`     | Region ID, used for sharding and optimized retrieval.          |
| `alert_type`   | `VARCHAR(50)` | Type of alert (`threshold_exceeded`, `structural_failure`).    |
| `severity`     | `VARCHAR(20)` | Severity of the issue (`low`, `moderate`, `high`, `critical`). |
| `timestamp`    | `TIMESTAMP`   | When the alert was triggered.                                  |
| `resolved`     | `BOOLEAN`     | Whether the alert has been acknowledged/resolved.              |

**Indexes:**

-   **Index on `structure_id`** for structure-based issue tracking.
-   **Index on `region_id`** to support sharding.
-   **Index on `timestamp`** for recent alerts.

---

## **5. `sensor_metadata` (Metadata Table for Sensors)**

Even though reports are **per structure**, we still track which **sensors** contribute to the reports.

| Column Name         | Data Type          | Description                                                        |
| ------------------- | ------------------ | ------------------------------------------------------------------ |
| `sensor_id`         | `VARCHAR(50)` (PK) | Unique identifier for the sensor.                                  |
| `structure_id`      | `UUID` (FK)        | The structure this sensor is monitoring.                           |
| `region_id`         | `INTEGER`          | Region ID, used for sharding and optimized retrieval.              |
| `installation_date` | `DATE`             | When the sensor was installed.                                     |
| `last_calibrated`   | `DATE`             | Last time the sensor was calibrated.                               |
| `status`            | `VARCHAR(20)`      | Current operational status (`active`, `faulty`, `decommissioned`). |

**Indexes:**

-   **Primary Key on `sensor_id`**.
-   **Index on `structure_id`** to find all sensors monitoring a structure.
-   **Index on `region_id`** to support sharding.

---

# **Scaling and Performance**

The **Reports Database** is designed to scale efficiently using a **combination of sharding and replication**. Sharding distributes data across multiple database nodes, while replication ensures high availability and read scalability.

### **Sharding Strategy (Write Scaling)**

-   **2 initial shards**, each handling a subset of the data.
-   **Sharding is based on `region_id` and time-based partitioning** (e.g., reports by month).
-   **Future scaling:** Add more shards as data grows beyond **20,000 TPS per shard**.

### **Replication Strategy (Read Scaling & Failover)**

Each **shard has 3 replicas**:

-   **2 Read Replicas** – Handle API queries and dashboard requests.
-   **1 Hot Standby Replica** – Ensures automatic failover.

| **Shard**              | **Primary Node** | **Read Replicas** | **Standby Replica** |
| ---------------------- | ---------------- | ----------------- | ------------------- |
| **Shard One**          | 1                | 2                 | 1                   |
| **Shard Two**          | 1                | 2                 | 1                   |
| **Future Shard Three** | 1                | 2                 | 1                   |
| **Future Shard Four**  | 1                | 2                 | 1                   |

### **Performance Optimization**

-   **Reads go to replicas**, **writes go to primary nodes** to avoid contention.
-   **Time-based partitioning** ensures fast retrieval of historical data.
-   **Redis caching** is used for frequently accessed queries.

### **Scalability Summary**

| **Component**          | **Scaling Plan**                                                    |
| ---------------------- | ------------------------------------------------------------------- |
| **Sharding**           | Start with **2 shards**, scale to **4+** when needed.               |
| **Replication**        | **3 replicas per shard** (2 read, 1 standby).                       |
| **Query Optimization** | Read-heavy queries go to **replicas**, batch inserts to **shards**. |
| **Caching**            | **Redis caches** frequently accessed reports.                       |
