# Table of Contents

- [Keycloak Setup](#keycloak-setup)
  - [Configuring Client Credentials in Keycloak](#configuring-client-credentials-in-keycloak)
    - [Creating a Client](#creating-a-client)
    - [Adding Client Credentials](#adding-client-credentials)
    - [Adding a User](#adding-a-user)
    - [Applying your credentials in the Swagger UI](#applying-your-credentials-in-the-swagger-ui)
  - [Hardcode Claims Within A Client](#hardcode-claims-within-a-client)

# Keycloak Setup
Once you start the application and navigate to the Swagger UI to test the API endpoints, there is going a lock icon next to the endpoint you want to test. When you click on the icon, you are required to implement your client id and secret to sucessfully send a request. Otherwise, a "401 unauthorized" is returned from the service. Keycloak is configured to submit secured between requests, utilizing the authorization and OAuth2 strategy. You must configure client credentials in Keycloak to submit API request in Swagger UI.

## Configuring Client Credentials in Keycloak
Log into the Keycloak admin console with the admin credentials configured in your `env` file.

Once logged into the portal, you are navigated to the master realm by default. You will need to create a new realm, so you can use the credentials and generated tokens to make secured requests against the API.

### Creating a Client
  - Navigate to **Manage Realms** -> **Create Realm** and name the realm of your choosing.
  - Once the realm is created, click on the newly created realm in the list and navigate to the **Clients** tab.
  - Click on the **Create Client** button. This is where you can configure client credentials for your service.
  - Provide a **Client ID** for your client `(Ex: job-board-api-client)`. Add a name and description *(optional)*.
  - Within the capability config, toggle the **Client configuration** option to on. This will enable the Credentials tab in the Keycloak config and the authentication step in the Swagger UI to enter client credentials.
  - If you are making client configurations for the Job Portal Service, add the following configurations within the login settings.
    - Add `http://localhost:5000/*` and `http://localhost:5000/docs/oauth2-redirect` in the **Valid indirect URIs** section and add `http://localhost:5000` in the **Web origin**.
  - If you are making client configurations for the Job Board API client, add the following configurations within the login settings.
    - Add `http://localhost:8082/*` and `http://localhost:8082/swagger-ui/oauth2-redirect.html` in the **Valid inderect URIs** section and add `http://localhost:8082` in the **Web origin**.
  - Click save.
### Adding Client Credentials
  - Once you've successfully created a client for your application, you should be navigated to the to the settings. If not, navigate to **Clients** in the side bar and selected the client you just created. You should be able to see the Credentials tab where you can fetch the generated client secret.
    - **Note:** If you don't see the Credentials tab, you may forgot to toggle the **Client configuration** option to on. Scroll down to the **Capability config** section and check if the option is toggled on. Once enabled, you should see the Credentials tab displayed.
  - Copy the client secret and store it in your env file including your client id.
  - *(A more secure approach when sending requests through the browser)* Using client secrets are acceptable when making secured API requests through Postman, Curl, or integrating with Azure Key Vault and HashiCorp Vault where secrets are abstracted away from plain site. Referencing the client secret when submitting requests from the Swagger UI may not be the best alternative since the secret is normally stored in the browser when making calls using FastAPI. This approach may be okay for testing, but if you don't prefer to utilize client secrets through Swagger, you can disable it from the auth flow. To disable, click on **Clients**, click on the client id associated with the application, and within the client details, scroll down and toggle the **Client configuration** option to off. This will allow you to authenticate without entering your client secret. You should be able to gain access to the endpoints by entering your client id and leaving the client secret field blank.
### Adding a User
  - We need to add a user in order to authenticate a request.
  - Navigate to the **Users** link in the side bar click the **Add User** button to a add a user.
  - Provide the user a username and click save.
  - Once you successfully create a user, navigate to the **Credentials** tab and set a password for the user. The credentials are used to authenticate a user before submitting a request.
  - If you would like to add a user with admin access, you can do so by assigning the user the admin role by navigating to **Role Mappings** -> **Realm Roles**. If the admin role is not displayed within the list of realm roles, go to **Realm roles** -> **Create role** to add the role. Name the role `admin` and provide a description. Create a user and assign the user the `admin` role. Confirm the role was assigned successfully to the user by logging into the user and submitting a request to one API endpoints that require admin access. If you prefer assigning roles to a specifice client, you can follow the same process configuring roles through the **Clients** tab.
### Applying your credentials in the Swagger UI
  - Navigate to [Job Board API Swagger](#http://localhost:5000/docs#) or the [Job Portal Service Swagger](#http://localhost:8082/swagger-ui/index.html#). Click the `Authorized` button and enter the client id and client secret you stored in the env file.
  - Once you successfully submit your client crendentials, you may be navigated to the Keycloak admin login page to enter user credentials for the first time.
  - Once you've successfully submit the user credentials created in the previous step, you should be able to hit one of API endpoints with no issues.

## Hardcode Claims Within A Client
After authenticating and submitting a request, you may run into a `Invalid token: Invalid audience` being returned in the response. If you run into this error, go into the developer tools to grab the auth token *(Ex: **Inspect** -> **Network** -> name of the endpoint -> **Headers** -> copy the token under the Authorization header)*.

Navigate to [JWT Debugger](#https://www.jwt.io/) and paste your generated token into the text area to encode and validate the JWT. Head over to the right of the page under the "Decoded Payload" section and look for the `aud` field. You may notice that the field contained the `account` within the payload. This is the default value that is populated whenever a token is generated. We can modify claims in Keycloak to include claims that are associated with our client to address the error.
  - Log into the Keycloak admin console.
  - Go to **Client** and click on the name of your client.
  - Within the client details, navigate to the **Client Scopes** tab and click on the client scope that has "dedicated" appended to the name.
  - Within dedicated scopes, click **Add Mapper** -> **By Configuration**.
  - Scroll down and select **Hardcoded Claim**
  - Provide a name for the mapper.
  - Type in `aud` for the **Token Claim Name**.
  - Store the **Claim Value** the same as your client id.
  - Save your changes.

Submit another request using one of the API endpoints in Swagger and verify you can return a successful response. Follow the same steps earlier in the wiki for validating your JWT and confirm that the `aud` field contains the appropriate values. When you examine the decoded payload on the right of the page, the field should contain an array of claims including the claim value that was created in Keycloak.