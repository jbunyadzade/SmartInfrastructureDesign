# Authentication and Authorization of Sensor

This document explains the authentication and authorization process for a sensor in a system.

---

## **1. Sensor Provides Credentials (Authentication Step)**
- The **sensor** (IoT device) sends its credentials to the **Authentication System** for verification.
- **Credentials may include:**
  - **Device ID**: A unique identifier for the sensor.
  - **API Key**: A pre-issued key used to authenticate the sensor.
  - **Client Certificate**: Used in mutual TLS authentication.
  - **JSON Web Token (JWT) Refresh Token**: If the sensor is using OAuth2-based authentication.

---

## **2. Authentication System Verifies Credentials**
- The authentication system checks the provided credentials against an **authentication service**.
- If the credentials are **valid**, the system issues an **Access Token** to the sensor.

---

## **3. What is in the Access Token?**
The **Access Token** is a digitally signed token used for **Authorization**. It typically contains:

- **Sensor ID**: Identifies the sensor that made the request.
- **Expiration Time**: Defines when the token becomes invalid.
- **Permissions/Scopes**: Lists the actions the sensor is allowed to perform.
- **Issuer Information**: Identifies the authentication system that issued the token.
- **Signature**: A cryptographic signature to prevent tampering.

---

## **4. Authorization Step**
- The **Authorization System** checks whether the authenticated sensor has the necessary permissions to access specific resources.
- It validates the **Access Token** and checks **permissions** based on roles or access control lists (ACLs).

---

## **5. Resource Access**
- If authorization is **successful**, the sensor is granted access to the requested resources.
- If authorization **fails**, a `403 Forbidden` response is returned.

---

## **6. Summary**
- **Credentials** are used for authentication.
- If valid, an **Access Token** is issued.
- The **Access Token** contains the sensor's identity, permissions, and expiration.
- Authorization determines if the sensor has permission to access resources.

---

## **Diagram**

Link to draw.io diagram: [Sensor Auth Diagram](https://viewer.diagrams.net/?tags=%7B%7D&lightbox=1&highlight=0000ff&edit=_blank&layers=1&nav=1&title=sensor_auth.drawio#R%3Cmxfile%3E%3Cdiagram%20name%3D%22Page-1%22%20id%3D%22mMZeZgjikk1Z3pZBnvqS%22%3E1VjLctowFP0alunIEgZ7mZCUTGc6Q0sfyaqj2jdYxUiMLF75%2BkpYxlbVBDoBQ1e2jq6kq3OOHnaHDGbroaTz7KNIIe9glK475LaDcYBCpB8G2ZRIaEoGmEiW2qAaGLNnqFpadMFSKJxAJUSu2NwFE8E5JMrBqJRi5YY9idwddU4n4AHjhOY%2B%2Bp2lKivRqJqWwe%2BBTbJq5ADZmhmtgi1QZDQVqwZE7jpkIIVQ5dtsPYDckFfxUrZ7%2F0LtLjEJXB3S4EN2H08nX3%2F1JB1nPz5NyVX%2BdBWVvSxpvrATHgMvhLQpq03FwypjCsZzmpjySmvdITeFkmK6o0VP6MbPyaa5BKlg3YBsjkMQM1Byo0NsLYksX9YwOLKGWdX0B6hXYlmD%2BrBrVbeKT3Zd16ToF8vLP3AUexxdL1SmZ8gSqpjgum68KRTMzkcaRl2HtC6KPNJ23bZCWrV%2BG6wNgYOkSq9mjK6TBArz8kWTwc9JnOu2kIQ%2BcQS1SVzgEWfsJiR7vhy3xQ5pPRL7pAW4TdKwR9pnKMRCai5qs51tS%2Bv2HL76Pd9kQT9sky%2Fi8TWSYrk9ajEaSEjN9kZzn7RkIZdgug62bFGprs0pqwEuuI65AZ5WyM9cJFMDrZl60GX0DhFbfLQ96GHkpqwLq%2BJjGWqqpVjwdDscek0Kq%2FTeY06nOwG1f6uH1LkU%2BMJKyPViXLp3hL%2BptG2q%2BaCbRsBcMK6KRs8jAzT8EoSOX0gf%2F6F42WOt%2Fy61N1ii61niG81Zajfs03kibFqilP1FVxzREvGBlqgOstY84SzsYwgbesKeUsGW5Ktk2a9f8L%2Fr1%2FP0G2SQTM2qHIGcsaLQ14KTrkpXUxycTNTgUFHxRW7UfRy3sFH3PT%2Fob%2B9tnsh8uKFb4My5ZB%2FVGP244QzkOiOOms445gFeCb7XGdFFGCMM8WvG2Bv%2FxhNfF%2Bv%2FCmV4%2FXeG3P0G%3C%2Fdiagram%3E%3C%2Fmxfile%3E)

![Sensor Auth Diagram](sensor_auth.png)