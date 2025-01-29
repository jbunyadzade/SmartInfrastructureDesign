# **Dashboard App**

The **Dashboard App** is a key component of the **Smart Infrastructure Management System**, providing a user interface for monitoring, analyzing, and managing infrastructure health. It enables engineers, planners, and administrators to view sensor data, track infrastructure performance, and receive alerts on anomalies or failures. The app integrates with the **Sensor Input App**, **Prometheus**, and the **Notification System** to provide actionable insights through visual dashboards.

---

## **Endpoints**

### `GET /dashboard/overview`

This endpoint retrieves a summary of the current infrastructure status, including key metrics and alerts.

#### **Request Input**

-   None

#### **Request Response**

-   `total_sensors` (integer): Total number of sensors actively reporting data.
-   `active_alerts` (integer): Number of active alerts in the system.
-   `uptime` (float): System uptime in percentage.
-   `average_latency` (float): Average data processing latency (ms).

#### **Example**

**Request:**  
`GET /dashboard/overview`

**Response:**

```json
{
    "total_sensors": 1000000,
    "active_alerts": 120,
    "uptime": 99.98,
    "average_latency": 85.2
}
```

---

### `GET /dashboard/sensors/:sensor_id`

This endpoint retrieves the most recent readings and metadata for a specific sensor.

#### **Request Input**

1. `sensor_id` (string, required): A unique identifier for the sensor.

#### **Request Response**

-   `sensor_id` (string): The unique identifier of the sensor.
-   `last_timestamp` (ISO 8601 string): The timestamp of the last recorded data point.
-   `metric` (string): The type of metric recorded.
-   `value` (float): The last recorded value.
-   `region` (string): The geographical region of the sensor.
-   `health_status` (string): The health status of the sensor (`"healthy"`, `"warning"`, `"critical"`).

#### **Example**

**Request:**  
`GET /dashboard/sensors/sensor_12345`

**Response:**

```json
{
    "sensor_id": "sensor_12345",
    "last_timestamp": "2025-01-27T12:34:56Z",
    "metric": "temperature",
    "value": 23.5,
    "region": "north",
    "health_status": "healthy"
}
```

---

### `GET /dashboard/alerts`

This endpoint retrieves a list of active alerts affecting infrastructure components.

#### **Request Input**

-   None

#### **Request Response**

-   `alerts` (array): List of active alerts. Each alert contains:
    -   `alert_id` (string): Unique identifier for the alert.
    -   `sensor_id` (string, optional): The sensor reporting the issue (if applicable).
    -   `alert_type` (string): Type of alert (`"threshold_exceeded"`, `"system_failure"`, `"data_anomaly"`).
    -   `severity` (string): Severity of the alert (`"low"`, `"moderate"`, `"high"`, `"critical"`).
    -   `timestamp` (ISO 8601 string): When the alert was triggered.

#### **Example**

**Request:**  
`GET /dashboard/alerts`

**Response:**

```json
{
    "alerts": [
        {
            "alert_id": "alert_9876",
            "sensor_id": "sensor_12345",
            "alert_type": "threshold_exceeded",
            "severity": "high",
            "timestamp": "2025-01-27T12:45:00Z"
        },
        {
            "alert_id": "alert_9877",
            "sensor_id": "sensor_67890",
            "alert_type": "data_anomaly",
            "severity": "moderate",
            "timestamp": "2025-01-27T12:50:00Z"
        }
    ]
}
```

---

## **Diagrams**

Add `.drawio` diagrams here to visualize the **Dashboard App** architecture, data flow, and its interaction with the **Sensor Input App**, **Prometheus**, and **Notification System**.

---

## **Monitoring and Metrics**

The **Dashboard App** exposes the following metrics for monitoring via `/metrics`:

1. **User Metrics**:

    - `active_users`: The number of users currently accessing the dashboard.
    - `session_duration`: Average user session duration in minutes.

2. **Performance Metrics**:

    - `dashboard_response_time`: The average time taken to load dashboard pages.
    - `api_request_rate`: The number of API requests per second handled by the Dashboard App.

3. **System Health Metrics**:
    - `dashboard_error_rate`: The percentage of failed requests.
    - `notification_latency`: The time taken for alerts to be displayed after being triggered.

---

## **Performance Estimations**

The **Dashboard App** is expected to handle high volumes of concurrent requests from multiple users while maintaining low latency.

1. **Expected Concurrent Users**: Up to 10,000 concurrent users.
2. **Dashboard API Throughput**: Must handle at least 5,000 API requests per second.
3. **Alert Processing Latency**: Alerts should appear on the dashboard within **500ms** of being generated.
4. **Dashboard Load Time**: Pages must load within **200ms** under normal conditions.

### **Performance Breakdown**

-   **Rendering Latency**:

    -   Client-side rendering (JavaScript execution): **50-150ms**
    -   API request handling (including data fetch, processing, and response time): **20-100ms**
    -   Network latency: **1-100ms**

-   **Worst-Case Scenario (Heavy Load)**:
    -   High user traffic causing delays in request handling.
    -   Increased database query response times leading to slower page loads.

**Estimated Total Response Time:**

-   **Best case**: **50-100ms**
-   **Average case**: **100-250ms**
-   **Worst case (peak load, slow database responses)**: **250-500ms**

---

## **Data Processing Workflow**

1. **Sensor Data Aggregation**:

    - Periodic API calls fetch data from the **Sensor Input App** to update dashboard metrics.

2. **Alert Processing**:

    - The dashboard queries **Prometheus** and **Alertmanager** at regular intervals to fetch active alerts.

3. **Database Optimization**:

    - Frequently accessed data is cached to improve response times.
    - Indexed queries minimize latency when retrieving sensor and alert data.

4. **Load Balancing**:
    - Requests are distributed across multiple backend instances using an **AWS ALB/NGINX load balancer** to handle high traffic loads.

---

## **Other Information**

-   **Caching:**

    -   Frequently accessed data (e.g., sensor lists, system status summaries) is cached in **Redis** for rapid retrieval.

-   **Database Optimization:**

    -   Queries are indexed to minimize response times.
    -   Time-series sensor data is partitioned by region and time for fast lookups.

-   **Scalability Plan:**
    -   Uses **Kubernetes** for auto-scaling instances based on traffic.
    -   **Load balancers** distribute requests across multiple backend instances.
    -   **CDN caching** minimizes redundant API requests for static data.
