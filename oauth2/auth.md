# Step-by-Step Explanation: Authentication and Authorization Flow

## 1. **User Authentication (OAuth2 + OpenID Connect)**

- The user initiates login by clicking "Sign in with..." on the Dashboard application.
- The Dashboard App redirects the user to the **External Auth Service** with the following details:
  - `client_id`: Identifies the Dashboard application.
  - `redirect_uri`: Where to redirect the user after authentication.
  - `scope`: Includes "openid" (OpenID Connect) and other requested scopes (e.g., `profile`, `email`).
  - `response_type`: Usually `code` for the authorization code flow.
  - `state`: A unique value to prevent CSRF attacks.
- The **External Auth Service** displays a login form for the user.
- The user enters their credentials (e.g., username and password).
- Upon successful authentication, the **External Auth Service**:
  - Redirects the user back to the `redirect_uri` with an **authorization code** and the original `state`.

## 2. **Token Exchange**

- The Dashboard App exchanges the **authorization code** for tokens by making a back-channel POST request to the **External Auth Service**, including:
  - The `authorization_code`.
  - The `client_id` and `client_secret`.
  - The `redirect_uri`.
- The **External Auth Service** responds with:
  - An **ID Token** (contains user identity information, used by the Dashboard App).
  - An **Access Token** (used to access Resources).
  - Optionally, a **Refresh Token** (to obtain new access tokens).

## 3. **Authorization Policy Check**

- The Dashboard App sends the **access token** to the **User Data/Policy DB**.
- The **User Data/Policy DB** verifies the access token and fetches the user's roles, permissions, or attributes.
- Based on the configured policies:
  - The server determines what actions the user is allowed to perform on resources.

## 4. **Resource Access**

- The client sends the **access token** along with a request to the **Resource Server**.
- The **Resource Server** performs the following checks:
  - Validates the access token by communicating with the **External Auth Service**.
  - Ensures the user has the necessary permissions for the requested action using the information provided by the **User Data/Policy DB**.
- If the token is valid and permissions allow, the **Resource Server** grants access to the resource.
- If the token is invalid or the user lacks permissions, the **Resource Server** denies access.