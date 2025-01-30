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

Link to draw.io diagram: [OAuth2 Diagram](https://viewer.diagrams.net/?tags=%7B%7D&lightbox=1&highlight=0000ff&edit=_blank&layers=1&nav=1&title=oauth2.drawio#R%3Cmxfile%3E%3Cdiagram%20name%3D%22Page-1%22%20id%3D%22A6TEADBUKVytIuzO8XLI%22%3E7Ztfc5s4EMA%2Fjed6D%2FUIBBge4zjJZa43zdRtr3dvCiixpjKiQo7tfvqTQGCEbIfMxS5Oks6kZlnxZ%2Fen3dXKGcDz%2BeqKo2z2F0swHbggWQ3gZOC6jgt9%2BZ%2BSrEvJezgKg1J0z0mi1TaCKfmJtRBo6YIkODcUBWNUkMwUxixNcSwMGeKcLU21O0bNu2boHluCaYyoLf2bJGJWSkMfbOR%2FYHI%2Fq%2B7sAH1mjiplLchnKGHLhgheDOA5Z0yUn%2Barc0yV%2BSq7lOMud5ytH4zjVHQZkF99%2Bfzzz%2BvVBWeX0fn1v06Wi%2FcQlpd5QHSh33iCY5ITlg7gmTxxRql8aPdygtO1fg%2BxrowTL%2FgDVpd3BnCcC8TFmTK5FKQslTpjnCaV5Jay%2BLsSrYj4Jo%2FBMIL68B91WGgLvv6mL1ccFGeGo0gec7ZIk%2BJmStV%2BeW0PnBj%2B1Ka4wmyO5fWkAscUCfJguhdpSu5rPT1UPj1aNxQyRlKRN658owRSQSMfjTTx64qH0De98tgA4EXNAfJD%2BQzbhwe%2BYwyPIncYBKD%2Bab2fdNE9Fvoaxk0aNtqICoKeQJPnWzR9wmLBUyn7zL7jtJi98teXHPNnpclpojSEzh6agpOByQGjyIQjCKK9NNkjYASegJMfjlrDoTv0jstQYDE0lY5vELQkMrK64BP%2BscC5eN6gZGAUjJoYgSGITJKAczIoyUixl4vHB%2Fiu%2FwSQvMhvgxQMndB8q5wteIwPRpITORZKWwOPzMyZ%2BriY0w%2FkDlNSYJJhTuQjSH04oVp8s5GNlzMi8DRDsRq6lPWPlMkqRCCpxzV%2BCWfZ52K%2BaD5iRinKcnJb3FtJOJbA5tLxn3BeVj9KyhZC3e%2B8rmqUMGNcSEkuOCIFXhjlYqnmAByneHkhEZtWbzWSKAY%2FFqq4GOP6RC0qknt9nt6yxuiNknveUtpxgQfMBZE1046hekY2xwJDoZpDpsZooiZwaZPA3ze51P3xau%2F0qs56wODS86EWLDfVnVNVdzOzsmsFwuakNNh9OqiuXYWhfHbLEFeB7yzL3ph9vczCVlLuCbP2yuFiJSlLlUnB2aLI0VPMH4iE7Y3eV0vvCAQd6A2CI9PrWfTeMEritYb2rUp4zcxGrtvHiLttdV3W0G%2FUvlHrOm7FXr%2BwtRf0iNrL9gaolxzNFaWJrIHrlXyxwsX84gGrhW4JjYWgxId9l2RRxourwnHxT56p39ZVepU9IHgGu3utHpzvhtAyewgj2%2BwucA9m9ZFl9YE%2FlnN8TnLV3c3VXcveLkBxjPN84E8sp0gLCMusuo1yRyhtiRAl96k8jHFaxpFq8pzpE3OSJOraWwOM7YpHgOpeQdvTIARb%2FXEwd4SPuyMpWux7vFFNER3EjemxzaBt6wumpBTdYnrDciKKJr%2FtrA8thdpplnt3T7cD%2BLJukAfdJhc8mC8jy5fXqTQWEli5kbJ7klq%2Ba%2FvilgnB5kYCkznPTFZVYoKTOgnVCUcnWKP5uKWxuTO86bqhQ%2FdMVAF2t6aOYr%2B4xwm91jo5gHu2Up6ry%2BhWG35GWZYQrmobF7z7qJbC6vzAld4AHzOcXk8KWxTt699fAiluZ1JgL0gJYKtQCvSDHZgUux89IXlG0boRN8Ad4%2FOXAAXsDEU%2Ftkh8pwXFSMeTA0Nh936ni9s5UY8KYo5lVSBTC81fAhLdM0o%2F4oTvmJW9F%2BopfGAk7NZquY2eb8pDtZFeboe%2BQ8XuaJFUCtmLSCndo0c%2Fio8AtoqP0N%2BzX%2Fp8qNh9TLVbvg2U02eie5nh9YKJEQxNJiJwlILU7hNeYhHPitXJIsf8N%2FWBM1oIyuiRNVeiLVKML1RscWnHb0B0cLB3Yg52gNf2cGAUkl1GHKf0tJtwm5Syz%2FknGCa6U9SP1DHyWv1DcJzC0%2B4Q7kodOkjwHd%2B4OkFGuqcSvxeMhK3v9fnOcSpRu235FVGS6FbXi6ks%2FM449GNhItNI0OLhOOWm3fo8Z%2Bkd4fO8lJKECPt74idIRPf1Rz8ChCQCmkS4Ryk2Hbv7%2BRVzckewVVmAdyxTWwqIvogVaveY0ZNitd75rQkJjlF6Vj2UBiFXHBWPKt%2B4%2Bv7Ca%2BKhH2VnGLomDjDwjoGD3e%2Bc4LQMF1Xd%2BQbDL4fBC45RTzj7Op2b4AAYVyaS%2FuEvAY7ui49%2B7IzAduLw3f9VWsjDzZ89luqbPx%2BFF%2F8B%3C%2Fdiagram%3E%3C%2Fmxfile%3E)

![OAuth2 Diagram](oauth2.png)