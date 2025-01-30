# Authentication and Authorization Process

This document provides a detailed explanation of the authentication and authorization flow, combining OAuth2 principles, OpenID Connect.

---

## **1. User Accesses the Application**
The **User** (Resource Owner) initiates the login process by accessing the **Dashboard App** (Client Application), wanting to use protected resources.

---

## **2. Application Redirects User to Authorization Server**
The **Dashboard App** redirects the **User** to the **External Auth Service** (Authorization Server) with an authorization request containing:
- `client_id`: Identifies the application.
- `redirect_uri`: The URL where the user will be redirected post-authentication.
- `response_type=code`: Requests an authorization code.
- `scope`: Specifies access levels (e.g., profile, email, etc.).

**What happens here?**
- The **User** is redirected to the login page hosted by the **External Auth Service**.

---

## **3. User Authenticates and Authorizes Access**
- The **User** enters their credentials (username, password) on the **External Auth Service**.
- After authentication, the **User** is asked to grant permission for the requested scopes:
  - Access to their profile.
  - Access to their email address.

### **Decision Flow**
- ✅ If the user approves → The flow continues.
- ❌ If the user denies → The flow stops, and the **Dashboard App** does not receive any token.

---

## **4. Authorization Server Redirects Back to Application with Authorization Code**
After successful authentication and authorization, the **External Auth Service** redirects the **User** to the **Dashboard App**'s `redirect_uri` with an `authorization_code`.

This Authorization Code:
- Is short-lived.
- Can only be used once.
- Needs to be exchanged for an Access Token.

---

## **5. Application Exchanges Authorization Code for Access Token**
The **Dashboard App** sends a back-channel request to the **External Auth Service** to exchange the `authorization_code` for an Access Token.
This request includes:
- `authorization_code`: The temporary code received earlier.
- `client_id` and `client_secret`: Authenticates the client.
- `redirect_uri`: Ensures validity.

---

## **6. Authorization Server Issues Access Token**
The **External Auth Service** validates the `authorization_code` and returns:
- An **Access Token** (used to access protected resources).
- An **ID Token** (optional, for OpenID Connect authentication).
- A **Refresh Token** (optional, for obtaining a new Access Token without re-authenticating).

---

## **7. Application Sends Access Token to Policy Server**
The **Dashboard App** sends the Access Token to the **Policy Server** to fetch the user's roles and permissions.

- The **Policy Server** verifies the token and retrieves the user's roles and permissions.
- It returns the permissions to the **Dashboard App**.

---

## **8. Request Resource with Access Token**
The **Dashboard App** sends the Access Token along with a resource request to the **Resource Server**.

---

## **9. Resource Server Validates Token**
The **Resource Server** performs:
1. **Token Validation**:
   - The **Resource Server** sends the token to the **Policy Server**.
   - The **Policy Server** verifies the token's validity.
   - If valid, the **Policy Server** confirms it.

2. **Permissions Verification (Optional)**:
   - The **Resource Server** checks if the user has the necessary permissions.

---

## **10. Access Control Decision**
Based on the permissions:
- ✅ **If permissions allow access** → The **Resource Server** grants access.
- ❌ **If permissions deny access** → The **Resource Server** denies access.

---

## **11. Return Resource or Error**
- If access is granted, the **Resource Server** returns the requested resource.
- If access is denied, an error message is returned to the **Dashboard App**.

---

## **Summary of Flow**
1. **User initiates login.**
2. **Dashboard App redirects to External Auth Service.**
3. **User authenticates and grants permissions.**
4. **External Auth Service issues Authorization Code.**
5. **Dashboard App exchanges Authorization Code for Access Token.**
6. **Access Token is sent to the Policy Server for permission validation.**
7. **Access Token is used to request resources from the Resource Server.**
8. **Resource Server validates token and permissions.**
9. **Access is either granted or denied based on permissions.**

This ensures a secure authentication and authorization process using OAuth2, OpenID Connect.


**Diagram**

Link to draw.io diagram: [OAuth2 Diagram](https://viewer.diagrams.net/?tags=%7B%7D&lightbox=1&highlight=0000ff&edit=_blank&layers=1&nav=1&title=oauth2.drawio#Uhttps%3A%2F%2Fraw.githubusercontent.com%2Fjbunyadzade%2FSmartInfrastructureDesign%2Fmain%2Foauth2%2Foauth2.drawio)

![OAuth2 Diagram](oauth2.png)
