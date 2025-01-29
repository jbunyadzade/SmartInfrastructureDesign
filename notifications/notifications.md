Here’s the **integrated Notification System description** with the chosen technologies, followed by an **updated diagram** that reflects these choices.

---

# **Notification System**

The **Notification System** is responsible for delivering **real-time alerts** and **maintenance updates** to relevant users. It ensures **timely, scalable, and reliable** delivery via multiple channels, prioritizing **critical notifications** while handling large-scale event processing efficiently.

---

## **1. Notification Types and Events**

The system listens for two major **event categories**, each processed through dedicated queues.

### **A. Anomaly Alerts (Real-Time Alerts)**

📌 **Source**: **Anomaly Detection System**  
📌 **Event Queue**: `anomaly_alerts_queue` (RabbitMQ)  
📌 **Recipients**: **Field Engineers, Supervisors**

| **Event Type**       | **Trigger Condition**                                       | **Priority** | **Notification Channels**                      |
| -------------------- | ----------------------------------------------------------- | ------------ | ---------------------------------------------- |
| `threshold_exceeded` | Sensor metric surpasses predefined threshold                | **High**     | **Push (FCM), SMS (Twilio)**                   |
| `structural_failure` | Immediate risk detected (e.g., extreme stress on a bridge)  | **Critical** | **Push (FCM), SMS (Twilio), Email (SendGrid)** |
| `data_anomaly`       | Sensor data deviates significantly from historical patterns | **Medium**   | **Push (FCM), In-App (WebSockets)**            |

---

### **B. Maintenance Notifications (Task Updates)**

📌 **Source**: **Maintenance Scheduler**  
📌 **Event Queue**: `maintenance_notifications_queue` (RabbitMQ)  
📌 **Recipients**: **Assigned Engineers, Admins**

| **Event Type**          | **Trigger Condition**                | **Priority** | **Notification Channels**                      |
| ----------------------- | ------------------------------------ | ------------ | ---------------------------------------------- |
| `maintenance_scheduled` | New maintenance task is created      | **Medium**   | **Email (SendGrid), In-App (WebSockets)**      |
| `task_reminder`         | Task starts in 1 hour                | **High**     | **Push (FCM), SMS (Twilio)**                   |
| `task_in_progress`      | Engineer marks task as "In Progress" | **Low**      | **In-App (WebSockets)**                        |
| `task_completed`        | Maintenance task is completed        | **Low**      | **In-App (WebSockets), Email (SendGrid)**      |
| `task_overdue`          | Task not completed by due time       | **High**     | **Push (FCM), SMS (Twilio), Email (SendGrid)** |

---

## **2. Message Flow & Processing**

The Notification System follows an **event-driven, queue-based** model with **RabbitMQ** for decoupling event producers from consumers.

### **A. Event Sources**

-   **Anomaly Detection System** → Publishes alerts to `anomaly_alerts_queue`.
-   **Maintenance Scheduler** → Publishes updates to `maintenance_notifications_queue`.

### **B. Processing Logic**

1️⃣ **Notification Worker fetches events from the queue** (Celery).  
2️⃣ **Applies filtering and deduplication** to prevent redundant alerts.  
3️⃣ **Determines recipients** based on event type and urgency.  
4️⃣ **Formats the message** appropriately for each delivery method.  
5️⃣ **Sends the notification via the correct channel**.

### **C. Message Queues and Workers**

| **Queue Name**                    | **Message Source**       | **Consumers (Workers)**             |
| --------------------------------- | ------------------------ | ----------------------------------- |
| `anomaly_alerts_queue`            | Anomaly Detection System | **Celery Worker (High Priority)**   |
| `maintenance_notifications_queue` | Maintenance Scheduler    | **Celery Worker (Medium Priority)** |

---

## **3. Database Schema**

### **`notifications` Table**

Stores all sent notifications for **auditing, tracking, and retry handling**.

| Column Name       | Data Type     | Description                                       |
| ----------------- | ------------- | ------------------------------------------------- |
| `notification_id` | `UUID` (PK)   | Unique identifier.                                |
| `user_id`         | `UUID` (FK)   | Recipient of the notification.                    |
| `event_type`      | `VARCHAR(50)` | Type (`anomaly_alert`, `maintenance_update`).     |
| `severity`        | `VARCHAR(20)` | Severity (`low`, `moderate`, `high`, `critical`). |
| `delivery_method` | `VARCHAR(50)` | Channel (`email`, `SMS`, `push`, `in-app`).       |
| `status`          | `VARCHAR(20)` | Delivery status (`pending`, `sent`, `failed`).    |
| `timestamp`       | `TIMESTAMP`   | When the notification was sent.                   |

**Indexes:**

-   **Index on `user_id`** for quick lookups.
-   **Index on `event_type`** to filter notifications by category.
-   **Index on `timestamp`** for time-based retrieval.

---

## **4. Retry and Failure Handling**

| **Failure Case**     | **Resolution**                                        |
| -------------------- | ----------------------------------------------------- |
| **Push/SMS Failure** | Retry **3 times** with exponential backoff.           |
| **Email Failure**    | Move to **dead-letter queue** and retry after 1 hour. |
| **Queue Overload**   | Auto-scale Celery workers.                            |

---

## **5. Final Technology Stack**

| **Component**          | **Technology Choice**                 |
| ---------------------- | ------------------------------------- |
| **Message Queue**      | RabbitMQ                              |
| **Database**           | PostgreSQL (indexed for fast lookups) |
| **Worker Framework**   | Celery (Python)                       |
| **Push Notifications** | Firebase Cloud Messaging (FCM)        |
| **SMS Delivery**       | Twilio                                |
| **Email Service**      | SendGrid                              |
| **In-App Alerts**      | WebSockets + Redis Pub-Sub            |

---

## **6. Summary**

✅ **RabbitMQ handles priority-based message queues.**  
✅ **Celery workers process events asynchronously and retry failures.**  
✅ **FCM (Push), Twilio (SMS), and SendGrid (Email) ensure reliable delivery.**  
✅ **In-App alerts via WebSockets for real-time dashboard updates.**
