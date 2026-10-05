Industry-leading API design standards—established by organizations like Google, Microsoft, AWS, OpenAPI Initiative, and OWASP—follow 30 essential best practices for RESTful web services.

## Resource Design & URI Structure

1. **Use Plural Nouns for Resource URIs (Not Verbs)**: Endpoints represent resource entities rather than actions. Use `GET /users` or `POST /orders` instead of `/getUsers` or `/createOrder`. As detailed in the [Microsoft Azure REST API Guidelines](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design), the HTTP method defines the operation, while the URI path specifies the target entity.
2. **Use Sub-Resources for Hierarchical Relationships**: Express ownership or child resources through logical path nesting (e.g., `/customers/42/orders` to retrieve orders belonging to customer 42). Limit nesting depth to 1–2 levels (`/collection/id/subcollection`) to avoid unwieldy and brittle URIs.
3. **Use Lowercase and Kebab-Case Formatting**: Standardize URIs using lowercase letters and hyphen separators (`/user-profiles`) to enhance readability and avoid case-sensitivity ambiguities. Avoid `snake_case` or `camelCase` in URI path segments.
4. **Keep URIs Clean, Extension-Free, and Without Trailing Slashes**: Do not append file extensions like `.json` or `.xml` into path names, and strip trailing slashes (`/users` instead of `/users/`) to ensure deterministic resource identifiers. Rely on request headers for payload format negotiation.
5. **Standardize Payload Property Naming**: Choose a single field-casing convention (typically `camelCase` or `snake_case`) and apply it consistently across all JSON request payloads and response bodies across all services.

## HTTP Methods & Semantics

6. **Strictly Adhere to Standard HTTP Verbs**: Follow HTTP/1.1 (RFC 7231) semantics for standard CRUD operations:
* `GET`: Retrieve a resource or collection.
* `POST`: Create a new sub-resource or trigger a processing task.
* `PUT`: Completely replace an existing resource or create it at a specified URI.
* `PATCH`: Apply partial modifications to a resource.
* `DELETE`: Remove a resource.


7. **Ensure Safe and Idempotent Operations**: Design `GET`, `HEAD`, and `OPTIONS` calls as safe (read-only). Ensure that `GET`, `PUT`, and `DELETE` requests are idempotent—meaning issuing the exact same request multiple times yields the same server state as a single invocation.
8. **Use `PATCH` for Partial Updates**: Use `PATCH` (RFC 5789) or JSON Merge Patch (RFC 7396) when clients need to update specific fields without sending the entire resource object in the payload.

## HTTP Status Codes & Error Handling

9. **Return Precise HTTP Status Codes**: Return accurate standard status codes:
* **2xx (Success)**: `200 OK` (retrieval/update), `201 Created` (resource creation), `204 No Content` (deletion).
* **4xx (Client Errors)**: `400 Bad Request`, `401 Unauthorized`, `403 Forbidden`, `404 Not Found`, `409 Conflict`, `422 Unprocessable Entity`.
* **5xx (Server Errors)**: `500 Internal Server Error`, `502 Bad Gateway`, `503 Service Unavailable`.


10. **Adopt Standardized Error Formats (RFC 7807)**: Return consistent error structures using the `application/problem+json` standard. Include standard properties such as `type` (URI identifier), `title`, `status`, `detail`, and `instance`.
11. **Provide Helpful, Safe Error Messages**: Include both machine-readable error codes (e.g., `INVALID_EMAIL_FORMAT`) and user-safe human-readable explanations. Never leak internal stack traces, system paths, or database schemas in production error responses.

## API Security & Access Control

12. **Enforce HTTPS / TLS Encryption Everywhere**: Encrypt all traffic in transit using TLS 1.2+ to safeguard credentials, tokens, and sensitive data payloads. Reject plain HTTP connections or redirect them immediately.
13. **Use OAuth 2.0 and OIDC with Short-Lived JWTs**: Authenticate and authorize API consumers using OAuth 2.0 (RFC 6749) access tokens and OpenID Connect. Issue short-lived JSON Web Tokens (JWTs) alongside secure refresh tokens.
14. **Validate Object-Level Authorization (Prevent BOLA)**: Prevent Broken Object Level Authorization (OWASP API1) by validating that the authenticated identity has explicit rights to access or alter the specific resource ID requested (e.g., `/orders/{id}`).
15. **Sanitize and Validate Inputs**: Validate all incoming parameters, path variables, query parameters, and JSON payloads against strict schema rules before processing to prevent Injection and SSRF attacks.
16. **Configure Restrictive Cross-Origin Resource Sharing (CORS)**: Set explicit `Access-Control-Allow-Origin` domains rather than using permissive wildcard headers (`*`) on endpoints handling authentication or non-public data.

## Performance & Data Optimization

17. **Implement Pagination for Large Collections**: Enforce pagination on collection endpoints using query parameters such as `?limit=20&cursor=xyz` (cursor-based pagination) or `?page=2&per_page=20` (offset-based). Return pagination metadata (total items, next/prev cursors) in response wrappers or HTTP `Link` headers.
18. **Support Filtering, Sorting, and Field Projections**: Allow clients to refine returned datasets via query parameters (e.g., `?status=active&sort=-created_at&fields=id,name,email`) to reduce over-fetching and network traffic.
19. **Leverage HTTP Caching with ETags**: Return `Cache-Control` directives along with `ETag` or `Last-Modified` headers. Support conditional requests via `If-None-Match` to return `304 Not Modified` responses when data hasn't changed.
20. **Enable Payload Compression**: Support content encoding (`Accept-Encoding: gzip, br`) to compress large response payloads during transfer and reduce latency.
21. **Enforce Rate Limiting and Quotas**: Guard against Denial of Service (DoS) and brute-force attempts by enforcing rate limits per API key or IP. Return HTTP status `429 Too Many Requests` alongside informational headers (`X-RateLimit-Limit`, `X-RateLimit-Remaining`, `Retry-After`).

## Payload & Data Contracts

22. **Envelope Top-Level JSON Responses**: Avoid returning raw JSON arrays (`[...]`) at the root level. Wrap responses in a top-level object (e.g., `{ "data": [...], "pagination": {...} }`) to allow adding top-level metadata in the future without breaking schema contracts.
23. **Use Content Negotiation Headers**: Honor the HTTP `Content-Type` header on incoming requests and the `Accept` header on outgoing responses (e.g., `application/json`).
24. **Provide Navigation Links via HATEOAS**: Where dynamic navigation is required (REST Maturity Level 3), include relation links (such as `_links` or `links`) within the payload to allow clients to discover valid state transitions.

## API Versioning & Lifecycle

25. **Implement an Explicit Versioning Strategy**: As recommended in the [Postman API Best Practices Guide](https://blog.postman.com/rest-api-best-practices/), version APIs explicitly to introduce changes without breaking existing consumer integrations. Use major version numbers in the URI path (e.g., `/v1/users`) or custom request headers.
26. **Maintain Backward Compatibility with Semantic Versioning**: Follow Semantic Versioning (SemVer) principles. Introduce non-breaking updates (such as adding optional fields) without incrementing the major version, reserving major version updates strictly for breaking schema changes.
27. **Follow Standard Deprecation Protocols**: Mark deprecated fields or endpoints using HTTP `Deprecation` and `Sunset` headers (RFC 8594) alongside `deprecated: true` annotations in OpenAPI specifications, giving developers ample transition time.

## Observability, Governance & Documentation

28. **Maintain Machine-Readable OpenAPI / Swagger Specifications**: Maintain an OpenAPI 3.x specification as the single source of truth. Use tools as outlined in [Swagger API Design Guidelines](https://swagger.io/blog/api-design-best-practices/) to generate interactive documentation, client SDKs, and automated contract tests.
29. **Pass Correlation IDs for Distributed Tracing**: Require or inject unique request trace IDs (e.g., `X-Request-ID` or W3C `traceparent` headers) across microservice boundaries to simplify end-to-end debugging.
30. **Implement Centralized Logging and Telemetry Without PII Leakage**: Log operational telemetry (request path, status code, latency, caller ID) centrally. Ensure log sanitization filters out Personally Identifiable Information (PII), bearer tokens, passwords, and API keys. For additional security governance, follow the checklist in the [OWASP API Security Guidelines](https://www.dexbytes.com/blogs/best-practice-for-rest-api-design).
