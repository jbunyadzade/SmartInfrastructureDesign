# Authentication and Authorization

**1.User Accesses the Application:**

The Resource Owner (typically a user) accesses the Application (client) wanting to use some protected resources.

**2.Application Redirects User to Authorization Server:**

The Application redirects the Resource Owner to the Authorization Server(via a URL) with an authorization request, including the `client_id`, `redirect_uri`, `response_type=code`, and `scope`.

The URL contains details like:

-   `client_id`: Identifies the application.
-   `redirect_uri`: A URL in your app where the user will be redirected after successful authentication and authorization.
-   `response_type=code`: Indicates that the app wants an authorization code.
-   `scope`: Defines the level of access being requested (e.g., profile, email, etc.).

**3.User Authenticates and Authorizes Access :**

-   The user logs in by entering their credentials (e.g., username, password) on the Authorization Server.
-   Once authenticated, the server asks the user to grant permission for the requested scopes. For example:
    -   Access to their profile information.
    -   Access to their email address.
        What Happens Here?
-   If the user approves, the Authorization Server moves to the next step.
-   If the user denies, the flow ends, and the application does not receive any token.

**4.Authorization Server Redirects Back to Application with Authorization Code:**

After successful authentication and authorization, the Authorization Server redirects the user back to the Application's `redirect_uri` with an `authorization_code`. The Authorization Code is a temporary code that the client can use to exchange for an Access Token. This code is short-lived and can only be used once.

**5.Application Exchanges Authorization Code for Access Token:**

The Application sends the `authorization_code`, `client_id`, `client_secret`, and `redirect_uri` to the Authorization Server in a back-channel request to obtain an Access Token.

-   The `authorization_code` received earlier.
-   The `client_id` and `client_secret` to authenticate the client.
-   The original `redirect_uri` for verification.

**6.Authorization Server Issues Access Token:**

The Authorization Server validates the Authorization Code and returns an Access Token (and optionally a Refresh Token) to the Application.

**7.Request Resource with Access Token:**

The Application sends the Access Token to the Resource Server to request protected resources.

**8.Return Resource:**

The Resource Server validates the Access Token and, if valid, returns the requested resource to the Application.

**Diagram**

Link to draw.io diagram: [OAuth2 Diagram](https://viewer.diagrams.net/?tags=%7B%7D&lightbox=1&highlight=0000ff&edit=_blank&layers=1&nav=1&title=oauth2.drawio#Uhttps%3A%2F%2Fraw.githubusercontent.com%2Fjbunyadzade%2FSmartInfrastructureDesign%2Fmain%2Foauth2%2Foauth2.drawio)

![OAuth2 Diagram](oauth2.png)
