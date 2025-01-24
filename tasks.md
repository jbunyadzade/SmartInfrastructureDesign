# Tasks to do so far

-   **List of endpoints to implement**.
    -   Think what each role would need to request.
    -   What they need to input and what answer they need to get.
    -   Write it out as a separate file with a list of endpoints in the `/endpoints` folder. Use the `sample.md` file as a template.
-   **Come up with solutions for notifications**.
    -   Think about business logic for this. Who should receive which notifications?
    -   Come up with a breakdown diagram for the worker cluster responsible for this.
-   **Authentication/authorization**.
    -   How should we authenticate and authorize users/sensors?
    -   Breakdown diagram for this flow.
    -   Some ideas: sensors need to be authenticated using certificates, while users could be authenticated via OAuth2 flow.
