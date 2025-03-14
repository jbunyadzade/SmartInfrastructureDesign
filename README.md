# Smart Infrastructure Design

## Introduction

Repo containing diagrams and documentation for the Smart Infrastructure Management System project.

The Smart Infrastructure Management System is a comprehensive solution for monitoring and managing critical infrastructure elements such as bridges, roads, pipelines, and dams. The system leverages IoT sensors, real-time data processing, and predictive analytics to ensure the safety, reliability, and longevity of these structures.

The requirements document can be found [here](REQUIREMENTS.md).

## Modules

1. [Sensor Input App](sensor_input/sensor_input.md)
1. [Time-series Database](sensor_input/tsdb_description.md)
1. [Dashboard App](dashboard_app/dashboard_app.md)
1. [Maintenance Scheduler](maintenance_scheduler/maintenance_scheduler.md)
1. [Notification System](notifications/notifications.md)
1. [Reports Database](dashboard_app/database_schema.md)
1. [External Authentication Service](oauth2/oauth2.md)
1. [Sensor Authentication](sensor_auth/sensor_auth.md)

## System Overview

In general, the system can be divided into 2 major parts:

1. Sensor Data Ingestion and Processing
2. Delivering reports and notifications to relevant stakeholders

The Sensor Data enters the system via the [Sensor Input App](sensor_input/sensor_input.md), which is responsible for ingesting and storing the data in the [Time-series DB](/sensor_input/tsdb_description.md). The sensors must go through [Sensor Authentication](sensor_auth/sensor_auth.md) before they can send data to the system.

A fleet of "Data-churning" workers then processes the data, generating reports, maintenance schedules and storing them in the [Reports Database](dashboard_app/database_schema.md). They also proactively analyze the latest incoming data to detect anomalies and, if necessary, send notifications to the users via the [Notification System](notifications/notifications.md).

There is also a dedicated [Maintenance scheduler](maintenance_scheduler/maintenance_scheduler.md), which queries the reports from the [Reports Database](dashboard_app/database_schema.md) send notifications to the users via the [Notification System](notifications/notifications.md).

The [Dashboard App](dashboard_app/dashboard_app.md) is the user-facing part of the API. It queries the [Reports Database](dashboard_app/database_schema.md) to display the latest data and alerts to the users. The users are authenticated via the [External Authentication Service](oauth2/oauth2.md).

## Diagram

Link to draw.io diagram: [Smart Infrastructure Diagram](https://viewer.diagrams.net/?tags=%7B%7D&lightbox=1&highlight=0000ff&edit=_blank&layers=1&nav=1&title=main.drawio#Uhttps%3A%2F%2Fraw.githubusercontent.com%2Fjbunyadzade%2FSmartInfrastructureDesign%2Fmain%2Fmain.drawio)

![Main Diagram](main.png)
