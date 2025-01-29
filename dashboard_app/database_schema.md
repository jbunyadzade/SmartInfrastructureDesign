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
| `location`          | `GEOMETRY`     | Geospatial coordinates.                                                 |
| `installation_date` | `DATE`         | When the structure was commissioned.                                    |
| `status`            | `VARCHAR(20)`  | Current operational status (`operational`, `under_repair`, `critical`). |

**Indexes:**

-   **Primary Key**: `structure_id`.

---

## **2. `reports` (Aggregated Sensor Data per Structure)**

Stores **processed data** that the **Worker Service** generates from raw sensor inputs.

| Column Name        | Data Type      | Description                                                 |
| ------------------ | -------------- | ----------------------------------------------------------- |
| `report_id`        | `UUID` (PK)    | Unique identifier for the report.                           |
| `structure_id`     | `UUID` (FK)    | The structure associated with this report.                  |
| `region`           | `VARCHAR(100)` | Geographical region where the structure is located.         |
| `metric`           | `VARCHAR(50)`  | Type of metric (e.g., stress load, vibration, temperature). |
| `aggregated_value` | `FLOAT`        | Aggregated value (average, min, max).                       |
| `aggregation_type` | `VARCHAR(20)`  | Type of aggregation (`average`, `max`, `min`).              |
| `timestamp`        | `TIMESTAMP`    | When the report was generated.                              |
| `status`           | `VARCHAR(20)`  | Condition assessment (`healthy`, `warning`, `critical`).    |
| `anomaly_detected` | `BOOLEAN`      | Whether an anomaly was detected.                            |

**Indexes:**

-   **Index on `structure_id`** for fast retrieval of reports.
-   **Index on `timestamp`** for time-series queries.

---

## **3. `maintenance_logs` (Records Past and Upcoming Repairs)**

Tracks **repair history** for structures rather than sensors.

| Column Name            | Data Type      | Description                                                    |
| ---------------------- | -------------- | -------------------------------------------------------------- |
| `log_id`               | `UUID` (PK)    | Unique identifier for the maintenance log.                     |
| `structure_id`         | `UUID` (FK)    | The structure undergoing maintenance.                          |
| `maintenance_type`     | `VARCHAR(100)` | Type of maintenance (`inspection`, `repair`, `reinforcement`). |
| `performed_by`         | `VARCHAR(100)` | The engineer responsible for the work.                         |
| `description`          | `TEXT`         | Details of what was done.                                      |
| `timestamp`            | `TIMESTAMP`    | When the maintenance took place.                               |
| `next_maintenance_due` | `TIMESTAMP`    | When the next maintenance is scheduled.                        |

**Indexes:**

-   **Index on `structure_id`** to track repairs on each structure.
-   **Index on `next_maintenance_due`** for scheduling upcoming work.

---

## **4. `alerts` (Stores Critical Issues for Dashboard Alerts)**

Stores **active alerts** that notify engineers about **critical structural issues**.

| Column Name    | Data Type     | Description                                                    |
| -------------- | ------------- | -------------------------------------------------------------- |
| `alert_id`     | `UUID` (PK)   | Unique identifier for the alert.                               |
| `structure_id` | `UUID` (FK)   | The affected structure.                                        |
| `alert_type`   | `VARCHAR(50)` | Type of alert (`threshold_exceeded`, `structural_failure`).    |
| `severity`     | `VARCHAR(20)` | Severity of the issue (`low`, `moderate`, `high`, `critical`). |
| `timestamp`    | `TIMESTAMP`   | When the alert was triggered.                                  |
| `resolved`     | `BOOLEAN`     | Whether the alert has been acknowledged/resolved.              |

**Indexes:**

-   **Index on `structure_id`** for structure-based issue tracking.
-   **Index on `timestamp`** for recent alerts.

---

## **5. `sensor_metadata` (Metadata Table for Sensors)**

Even though reports are **per structure**, we still track which **sensors** contribute to the reports.

| Column Name         | Data Type          | Description                                                        |
| ------------------- | ------------------ | ------------------------------------------------------------------ |
| `sensor_id`         | `VARCHAR(50)` (PK) | Unique identifier for the sensor.                                  |
| `structure_id`      | `UUID` (FK)        | The structure this sensor is monitoring.                           |
| `region`            | `VARCHAR(100)`     | Geographical region.                                               |
| `installation_date` | `DATE`             | When the sensor was installed.                                     |
| `last_calibrated`   | `DATE`             | Last time the sensor was calibrated.                               |
| `status`            | `VARCHAR(20)`      | Current operational status (`active`, `faulty`, `decommissioned`). |

**Indexes:**

-   **Primary Key on `sensor_id`**.
-   **Index on `structure_id`** to find all sensors monitoring a structure.

---

## **How the Tables Interact**

-   `structures` defines **what is being monitored and repaired**.
-   `reports` **summarizes sensor data** per structure.
-   `maintenance_logs` **tracks repair history** for structures.
-   `alerts` **stores active issues** that need attention.
-   `sensor_metadata` **links sensors to structures** but does not directly affect dashboard reports.
