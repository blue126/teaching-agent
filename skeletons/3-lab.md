# Session 3 – Mastering REST APIs with Python (Lab)

## Summary
The GUI is for humans; the API is for engineers. In this lab, we learn how to interact with NetBox (and network devices) using Python. We will cover the anatomy of a REST call, authentication tokens, and the standard CRUD operations (Create, Read, Update, Delete). We will perform these actions first via Postman/cURL, and then write robust Python scripts using the `requests` library.

## Content
*   **REST 101**: HTTP Methods (GET, POST, PUT, PATCH, DELETE) and Status Codes (200, 201, 400, 401, 403, 500).
*   **Authentication**: Handling API Tokens and Headers safely.
*   **Tooling**:
    *   **Postman**: For exploration and testing queries.
    *   **Python `requests`**: The Swiss Army knife for HTTP in Python.
    *   **json**: Parsing the returned data.
*   **NetBox API**: Exploring the Swagger/OpenAPI documentation (`/api/docs/`).

## Activities
*   **Lab 3.1**: "Hello World" - Get the status of NetBox via `curl` and Postman.
*   **Lab 3.2**: Writing a Python script to fetch all devices with role "Spine Switch".
*   **Lab 3.3**: Writing a Python script to programmatically create a new Prefix in IPAM.
*   **Lab 3.4**: Error Handling – What happens when the API is down or the token is wrong?

## References & Sources
*   NetBox API Documentation
*   [Python Requests Library](https://docs.python-requests.org/)
*   Packet Coders: NetBox API Guide
