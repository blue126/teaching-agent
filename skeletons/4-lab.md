# Session 4 – GraphQL - The Efficient Alternative (Lab)

## Summary
REST is great, but sometimes it's "chatty" or returns too much data ("overfetching"). GraphQL allows us to ask for exactly what we need—nothing more, nothing less. In this session, we explore the NetBox GraphQL API. We will learn to write queries that stitch together related data (e.g., "Give me all devices, their sites, and their primary IPs") in a single request.

## Content
*   **The GraphQL Problem**: Why REST doesn't scale for complex data relationships.
*   **The Language**:
    *   **Queries**: Asking for data.
    *   **Fields**: Selecting specific attributes.
    *   **Filtering**: Narrowing down the result set.
*   **GraphiQL**: The interactive explorer tool built into NetBox.
*   **Python Integration**: How to send GraphQL queries using standard HTTP POST requests.

## Activities
*   **Lab 4.1**: Using the GraphiQL interface to explore data relationships.
*   **Lab 4.2**: Writing a query to fetch `site name`, `device name`, and `interface count` in one go.
*   **Lab 4.3**: Implementing a Python script `query_graphql.py` to fetch data for inventory reporting.

## References & Sources
*   [GraphQL.org](https://graphql.org/)
*   NetBox GraphQL Documentation
*   "REST vs GraphQL" comparison
