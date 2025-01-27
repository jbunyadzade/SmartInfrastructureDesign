# **Sensor Input App**

The Sensor Input App is a core component of the Smart Infrastructure Management System. It is responsible for ingesting real-time data from IoT sensors deployed across the infrastructure. The app validates incoming data, pushes it to a buffer queue, ensures fault tolerance through retry mechanisms, and supports real-time monitoring of individual sensors through a WebSocket-based streaming service. It also exposes metrics for monitoring and alerting.

---

## **Endpoints**

### `POST /sensors/data`

This endpoint ingests real-time data from IoT sensors.

#### **Request Input**

1. `sensor_id` (string, required): A unique identifier for the sensor.
2. `timestamp` (ISO 8601 string, required): The timestamp when the sensor data was recorded.
3. `metric` (string, required): The type of metric being recorded (e.g., temperature, vibration, load).
4. `value` (float, required): The value of the recorded metric.
5. `region` (string, optional): The geographical region where the sensor is deployed.

#### **Request Response**

- `status` (string): A message indicating success or failure.
- `error` (string, optional): A description of the error (if any occurred).

#### **Example**

**Request:**

```json
POST /sensors/data  
{  
    "sensor_id": "sensor_12345",  
    "timestamp": "2025-01-27T12:34:56Z",  
    "metric": "temperature",  
    "value": 23.5,  
    "region": "north"  
}
```
**Response:**

```json
{  
    "status": "success"  
}
```

---

### `GET /sensors/:sensor_id`

This endpoint retrieves the most recent data for a specific sensor.

#### **Request Input**

1. `sensor_id` (string, required): A unique identifier for the sensor.

#### **Request Response**

1. `sensor_id` (string): The unique identifier of the sensor.
2. `last_timestamp` (ISO 8601 string): The timestamp of the last recorded data point.
3. `metric` (string): The type of metric recorded.
4. `value` (float): The last recorded value.
5. `region` (string): The geographical region of the sensor (if available).

#### **Example**

**Request:**

`GET /sensors/sensor_12345`

**Response:**

```json
{  
    "sensor_id": "sensor_12345",  
    "last_timestamp": "2025-01-27T12:34:56Z",  
    "metric": "temperature",  
    "value": 23.5,  
    "region": "north"  
}
```
---

### `GET /sensors/:sensor_id/stream`

This endpoint establishes a WebSocket connection for real-time monitoring of a specific sensor.

#### **Request Input**

1. `sensor_id` (string, required): A unique identifier for the sensor.

#### **Response**

- Real-time streaming of sensor data in JSON format:
    - `sensor_id` (string): The unique identifier of the sensor.
    - `timestamp` (ISO 8601 string): The timestamp of the current data point.
    - `metric` (string): The type of metric recorded.
    - `value` (float): The recorded value.
    - `region` (string): The geographical region of the sensor (if available).

---

## **Diagrams**


---

## **Monitoring and Metrics**

The Sensor Input App exposes the following metrics for monitoring via `/metrics`:

1. **Request Metrics**:
    - `sensor_data_ingestion_rate`: The rate at which data is being ingested from sensors.
    - `sensor_data_ingestion_errors`: The count of errors encountered during ingestion.

2. **Processing Metrics**:
    - `data_validation_failures`: The count of invalid data points rejected.
    - `buffer_queue_size`: The current size of the buffer queue.

3. **Performance Metrics**:
    - `request_latency`: The latency of processing incoming requests.
    - `task_retry_count`: The number of retry attempts for failed tasks.
    - `active_streams`: The number of active WebSocket connections for real-time monitoring.

---

## **Other Information**

- **Validation:** All incoming data is validated for format, completeness, and plausibility.
- **Retry Mechanism:** Data ingestion failures trigger retries through a retry queue.
- **Fault Tolerance:** A buffer queue ensures no data is lost during spikes in traffic or system downtime.
- **Real-Time Monitoring:** The app supports real-time monitoring of individual sensors through WebSocket streams, enabling live updates for subscribed clients.
- **Integration:** The Sensor Input App integrates with Prometheus for metrics exposure and supports robust fault handling with tools like RabbitMQ or Kafka for queuing.