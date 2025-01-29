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