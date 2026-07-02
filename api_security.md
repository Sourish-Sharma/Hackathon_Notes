# API Security

We usually look for API's that are:
    - Over permissioned
    - Access unauthorized functions
    - Return too much info
    - Expose logic flaws

*API1: Broken Object Level Auth (BOLA)*
    - accesing 

# Portswigger

- identify api endpoints, like in  `GET /api/books HTTP/1.1 \n Host: example.com`, /api/books is the endpoint of this request

## Discovering Hidden API Documentation

* **Core Concept:** If API documentation isn't publicly available, find it by analyzing applications interacting with the API.

### Discovery Methods

* **Automated:** Use **Burp Scanner** to crawl the API.
* **Manual:** Browse via **Burp's browser** to look for documentation endpoints.
* **Brute-Force:** Use **Burp Intruder** with a list of common paths to fuzz for documentation.

### Key Target Endpoints

Look for specific documentation paths, such as:

* `/api`
* `/swagger/index.html`
* `/openapi.json`

### Base Path Investigation

If a specific resource endpoint is found (e.g., `/api/swagger/v1/users/123`), strip it back iteratively to check all base paths:

1. `/api/swagger/v1`
2. `/api/swagger`
3. `/api`

- These all checks and manupilation will be done in the url
- Also keep a lookout for js files and their names

## Identifying Supported HTTP Methods

* **Core Concept:** Testing all potential HTTP methods on a single API endpoint can reveal hidden functionality, expanding the available attack surface.

### Common HTTP Methods
* **GET:** Retrieves data from a resource.
* **PATCH:** Applies partial changes to a resource.
* **OPTIONS:** Retrieves supported request methods for a resource.

### Multi-Method Endpoint Example (`/api/tasks`)
* `GET /api/tasks` – Retrieves a list of tasks.
* `POST /api/tasks` – Creates a new task.
* `DELETE /api/tasks/1` – Deletes a specific task.

### Testing Strategy & Automation
* **Automation:** Use **Burp Intruder** with the built-in "HTTP verbs" list to automatically cycle through different methods.
* **Safety Constraint:** Always target **low-priority objects** during testing to avoid unintended consequences (e.g., destroying critical data or flooding logs/databases).

- Always try out /api whenever you can to check the docs, also can try /openapi.json
- Always change a get request to options to check if some other info is there