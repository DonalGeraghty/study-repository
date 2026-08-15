# REST APIs and HTTP Testing

REST, or **Representational State Transfer**, is an architectural style for networked systems. It is not a synonym for CRUD and it does not define the HTTP methods. HTTP supplies protocol semantics; a REST-style API applies those semantics to resources and representations.

Many APIs described as “REST APIs” are more precisely HTTP APIs that exchange JSON. That distinction is useful: an API can use `GET`, `POST`, and JSON without satisfying every REST constraint.

## REST Concepts

REST describes constraints including:

- **client-server separation**: user-interface concerns and data-storage concerns can evolve independently;
- **stateless requests**: each request contains the information required to understand it;
- **cacheable responses**: responses identify whether and how they may be reused;
- **uniform interface**: resources are identified consistently and manipulated through representations with self-descriptive messages;
- **layered system**: clients do not need to know whether they communicate with the origin server, a gateway, or another intermediary;
- **code on demand**, optionally: a server may extend client behaviour by transferring executable code.

A strict REST interpretation also includes hypermedia-driven state transitions. Many production JSON APIs adopt only part of this model, so clarify what “REST” means in the system being discussed.

## Resources, Representations, and URIs

A **resource** is the conceptual thing identified by a URI, such as a customer, order, or collection. A **representation** is the transferred form of its current or intended state, commonly JSON.

```text
Resource:       order 42
URI:            /orders/42
Representation: { "id": 42, "status": "paid" }
```

Prefer stable resource-oriented paths:

```text
GET    /orders
POST   /orders
GET    /orders/42
PUT    /orders/42
PATCH  /orders/42
DELETE /orders/42
GET    /orders/42/items
```

Common guidelines are:

- use nouns for resources rather than RPC-style verb paths where resource semantics fit;
- use consistent pluralisation and naming;
- put resource identity and hierarchy in the path;
- put filtering, sorting, pagination, and projection controls in the query component;
- avoid exposing database table structure directly;
- do not place credentials or other sensitive values in a URI because URIs commonly appear in logs, browser history, and monitoring systems.

Actions that do not map naturally to CRUD may still be modelled explicitly. For example, `POST /orders/42/cancellations` can create a cancellation request and preserve domain meaning better than a generic `/cancelOrder` endpoint.

## Anatomy of an HTTP Exchange

A request contains a method, target, protocol version, header fields, and optionally content:

```http
POST /orders HTTP/1.1
Host: api.example.com
Authorization: Bearer token-value
Content-Type: application/json
Accept: application/json, application/problem+json

{
  "productId": "P100",
  "quantity": 2
}
```

A response contains a status code, header fields, and optionally content:

```http
HTTP/1.1 201 Created
Location: /orders/42
Content-Type: application/json
ETag: "order-42-v1"

{
  "id": 42,
  "productId": "P100",
  "quantity": 2,
  "status": "pending"
}
```

Do not validate only the JSON body. Method semantics, status, headers, media type, caching instructions, and timing are also part of the contract.

## Method Properties

### Safe

A safe method communicates that the client is requesting read-only semantics. Logging, metering, and other incidental server activity can still occur, but the client did not request an application-state change.

### Idempotent

An idempotent method has the same intended effect when the same request is performed multiple times as when it is performed once. Responses may differ—for example, a repeated `DELETE` might return `404` after the first deletion—even though the intended final state is unchanged.

Idempotency is about semantics, not whether the server executed code only once.

### Common Methods

| Method | Typical purpose | Safe | Idempotent |
| :--- | :--- | :---: | :---: |
| `GET` | Retrieve a selected representation | Yes | Yes |
| `HEAD` | Retrieve response metadata without response content | Yes | Yes |
| `POST` | Ask a resource to process enclosed content | No | No, unless the API defines additional guarantees |
| `PUT` | Create or replace state at the target URI | No | Yes |
| `PATCH` | Apply a partial modification | No | Depends on the patch semantics |
| `DELETE` | Remove the association represented by the target URI | No | Yes |
| `OPTIONS` | Discover communication options | Yes | Yes |
| `QUERY` | Perform a safe, idempotent query described by request content | Yes | Yes |

## Method Details

### GET

`GET` requests transfer of a current selected representation of the target resource.

```http
GET /orders/42 HTTP/1.1
Host: api.example.com
Accept: application/json
```

Content in a `GET` request has no generally defined semantics and may be rejected or mishandled by intermediaries and implementations. Use query parameters for ordinary searches. For a large or structured safe query, consider `QUERY` where the complete ecosystem supports it, or use a documented API-specific alternative.

### HEAD

`HEAD` asks for the same header fields that a corresponding `GET` would return, without response content. It can check existence, metadata, validators, or modification time without transferring the representation.

### POST

`POST` asks the target resource to process enclosed content according to that resource's semantics. Creation is common but not its only use.

When a new resource is created, `201 Created` is appropriate and the `Location` response field should identify the created resource when available. Other valid outcomes include `200 OK`, `202 Accepted`, and `204 No Content`, depending on the contract.

Because `POST` is not inherently idempotent, retrying after a timeout can accidentally repeat an operation. APIs may define an idempotency-key mechanism for operations such as payments or order creation, but its storage period, request matching, and replayed response behaviour must be documented and tested.

### PUT

`PUT` creates or replaces the state of the resource at the target URI with the supplied representation.

```http
PUT /profiles/42 HTTP/1.1
Content-Type: application/json

{
  "displayName": "Ava",
  "timeZone": "Europe/Dublin"
}
```

Clients need to understand whether omitted fields are removed, defaulted, or rejected. `PUT` is idempotent because repeating the same requested replacement has the same intended effect.

### PATCH

`PATCH` applies a partial modification. The media type defines the patch document's semantics; examples include JSON Patch and JSON Merge Patch.

```http
PATCH /profiles/42 HTTP/1.1
Content-Type: application/merge-patch+json

{
  "timeZone": "Europe/Paris"
}
```

Do not assume every `PATCH` is idempotent. Setting a field to a value can be idempotent, while an operation such as “increment balance” might not be.

### DELETE

`DELETE` requests removal of the association between the target URI and its current functionality. Common successful responses include:

- `204 No Content` when deletion is complete and no representation is returned;
- `200 OK` when a result representation is returned;
- `202 Accepted` when deletion will happen asynchronously.

The method is idempotent even though repeated requests can return different status codes.

### OPTIONS

`OPTIONS` describes communication options for the target resource. A response may include an `Allow` field listing supported methods. Browser CORS preflight requests also use `OPTIONS`, but CORS is a browser access-control mechanism—not authentication or authorisation.

### QUERY

`QUERY` was standardised in RFC 10008 in June 2026. It supports safe and idempotent server-side queries whose input is carried in request content rather than forced into a URI.

```http
QUERY /orders HTTP/1.1
Host: api.example.com
Content-Type: application/json
Accept: application/json

{
  "status": ["pending", "paid"],
  "createdAfter": "2026-01-01T00:00:00Z",
  "limit": 50
}
```

This is especially useful for complex queries that are too large or awkward for query parameters. Adoption across frameworks, clients, gateways, CORS policies, caches, and monitoring products may lag behind the standard, so verify end-to-end support before relying on it.

## Status Codes

A status code communicates the generic result at the HTTP layer. The response content can supply domain-specific details, but it should not contradict the status.

### Successful Responses

| Code | Meaning and common use |
| :--- | :--- |
| `200 OK` | The request succeeded and a representation is returned. |
| `201 Created` | One or more resources were created. Use `Location` when a primary resource URI is available. |
| `202 Accepted` | Processing was accepted but has not completed. Provide a way to observe progress where practical. |
| `204 No Content` | Processing succeeded and there is no response content. |
| `206 Partial Content` | A range request succeeded for part of a representation. |

A `204` response cannot carry response content. Do not return JSON alongside it.

### Redirection and Caching Responses

| Code | Meaning and common use |
| :--- | :--- |
| `301 Moved Permanently` | The resource has a new permanent URI; historical clients may change `POST` to `GET`. |
| `302 Found` | Temporary redirection; historical clients may change `POST` to `GET`. |
| `303 See Other` | Retrieve another resource using `GET`, often after processing a `POST`. |
| `304 Not Modified` | A conditional request can reuse its cached representation; this is not an ordinary redirect and has no response content. |
| `307 Temporary Redirect` | Temporary redirection that preserves the method and content. |
| `308 Permanent Redirect` | Permanent redirection that preserves the method and content. |

### Client-Error Responses

| Code | Meaning and common use |
| :--- | :--- |
| `400 Bad Request` | The server cannot process the request due to malformed syntax, framing, or another generic request problem. |
| `401 Unauthorized` | Authentication credentials are missing or invalid. Despite the name, this primarily describes authentication and normally includes `WWW-Authenticate`. |
| `403 Forbidden` | The server understood the request but refuses to authorise it. Re-authentication may not help. |
| `404 Not Found` | No current representation was found, or the server chooses not to reveal a protected resource's existence. |
| `405 Method Not Allowed` | The method is known but unsupported for this resource. Include `Allow`. |
| `406 Not Acceptable` | No available representation satisfies the client's `Accept` preferences. |
| `409 Conflict` | The request conflicts with the resource's current state. |
| `410 Gone` | The resource is intentionally unavailable and the condition is expected to be permanent. |
| `412 Precondition Failed` | A conditional request such as `If-Match` did not satisfy its precondition. |
| `415 Unsupported Media Type` | The request content format is unsupported. |
| `422 Unprocessable Content` | The syntax and media type are understood, but the instructions cannot be processed. |
| `428 Precondition Required` | The server requires a conditional request to prevent lost updates. |
| `429 Too Many Requests` | The client exceeded a rate limit. `Retry-After` may indicate when to retry. |

Do not use `404` for every failure or return `200` with an error object merely to avoid status-code handling.

### Server-Error Responses

| Code | Meaning and common use |
| :--- | :--- |
| `500 Internal Server Error` | An unexpected server condition prevented completion. |
| `501 Not Implemented` | The server does not support the functionality required to fulfil the request. It is not a generic application-development placeholder. |
| `502 Bad Gateway` | A gateway or proxy received an invalid response from an upstream server. |
| `503 Service Unavailable` | The service is temporarily unable to handle the request. `Retry-After` may be appropriate. |
| `504 Gateway Timeout` | A gateway or proxy did not receive a timely upstream response. |

Avoid exposing stack traces, database messages, internal hostnames, or secrets in error responses.

## Important Header Fields

| Field | Purpose |
| :--- | :--- |
| `Accept` | Media types the client can process. |
| `Content-Type` | Media type of the request or response content. |
| `Authorization` | Credentials used to authenticate or authorise a request. |
| `Location` | URI associated with a created resource or redirect. |
| `Allow` | Methods supported by a resource, especially with `405` or `OPTIONS`. |
| `ETag` | Validator identifying a selected representation version. |
| `If-None-Match` | Makes a request conditional, commonly for cache revalidation. |
| `If-Match` | Applies an operation only if the current validator matches. |
| `Cache-Control` | Directives governing cache reuse and storage. |
| `Retry-After` | Suggested delay or time before another request. |
| `WWW-Authenticate` | Describes an authentication challenge. |
| `Vary` | Identifies request fields that influenced representation selection. |
| `traceparent` | Propagates distributed tracing context when W3C Trace Context is used. |

Header names are case-insensitive. Their values and combination rules are field-specific.

## Content Negotiation

`Content-Type` describes what was sent; `Accept` describes what the recipient would like to receive.

```http
POST /orders HTTP/1.1
Content-Type: application/json
Accept: application/json
```

Test unsupported and missing media types, incompatible `Accept` values, character encoding, and whether responses declare their actual representation accurately.

## Error Responses

Use a consistent, machine-readable error contract. RFC 9457 defines **Problem Details for HTTP APIs** using `application/problem+json`.

```http
HTTP/1.1 422 Unprocessable Content
Content-Type: application/problem+json

{
  "type": "https://api.example.com/problems/validation-error",
  "title": "The request is not valid",
  "status": 422,
  "detail": "One or more fields failed validation",
  "instance": "/problems/7f3a1",
  "errors": [
    {
      "pointer": "/quantity",
      "detail": "must be greater than zero"
    }
  ]
}
```

Problem details describe the HTTP-facing error; they are not a debugging dump. Keep titles and types stable, make occurrence-specific detail useful, and avoid exposing sensitive implementation information.

## Collections, Filtering, and Pagination

Example collection request:

```http
GET /orders?status=paid&sort=-createdAt&limit=25&cursor=next-token HTTP/1.1
```

Document:

- supported filters and whether they can be combined;
- sort fields and direction syntax;
- default and maximum page sizes;
- whether pagination is offset-, page-, or cursor-based;
- how clients locate the next and previous page;
- whether totals are exact, approximate, optional, or expensive;
- ordering guarantees when data changes between requests.

Offset pagination is easy to understand but can become slow or inconsistent on changing datasets. Cursor pagination can provide more stable traversal but requires a well-defined ordering and opaque cursor lifecycle.

Tests should check page boundaries, empty collections, invalid cursors, duplicate or missing records across pages, stable ordering, maximum limits, and authorisation filters.

## Caching and Conditional Requests

A server can return a validator:

```http
HTTP/1.1 200 OK
ETag: "order-42-v3"
Cache-Control: private, max-age=60
Content-Type: application/json
```

The client can revalidate its cached representation:

```http
GET /orders/42 HTTP/1.1
If-None-Match: "order-42-v3"
```

If unchanged, the server can respond:

```http
HTTP/1.1 304 Not Modified
ETag: "order-42-v3"
```

Test cache directives carefully for authenticated or sensitive data. Shared caches must not receive reusable private responses accidentally, and representation variants need correct `Vary` behaviour.

## Optimistic Concurrency Control

Conditional requests can prevent a client from silently overwriting a newer update.

```http
PATCH /orders/42 HTTP/1.1
If-Match: "order-42-v3"
Content-Type: application/merge-patch+json

{
  "status": "cancelled"
}
```

If the representation has changed, the server can return `412 Precondition Failed`. Test two clients reading the same version, one updating successfully, and the other receiving a conflict rather than losing the first update.

## Asynchronous Operations

Long-running work can return `202 Accepted` instead of holding the connection open:

```http
HTTP/1.1 202 Accepted
Location: /operations/op-123
Retry-After: 2
Content-Type: application/json

{
  "id": "op-123",
  "status": "pending"
}
```

The operation resource should define terminal success and failure states, progress if available, expiry, cancellation behaviour, and whether polling is safe. Test duplicated submissions, retry behaviour, worker failure, eventual completion, and access control on operation status.

## Authentication, Authorisation, and API Security

Authentication establishes identity; authorisation decides whether that identity may perform this operation on this resource. A valid token is not proof of access to every object named in the request.

Security tests should include:

- missing, malformed, expired, revoked, and incorrectly scoped credentials;
- horizontal access attempts against another user's object;
- vertical access attempts against administrative operations;
- object identifiers changed in paths, query parameters, and bodies;
- field-level access for sensitive or privileged properties;
- excessive data returned by default;
- mass assignment of fields clients should not control;
- injection, unsafe deserialisation, path traversal, and SSRF inputs;
- rate limits and abuse of sensitive workflows;
- inventory and retirement of old API versions;
- misconfigured CORS, caching, debug endpoints, and error handling.

Use TLS for credentials and sensitive data. Never put bearer tokens in query parameters. Do not treat CORS as a server-to-server security control.

### JSON Web Tokens

A JSON Web Token (JWT) is a compact signed claims format, not an authorisation policy and not encryption. A service accepting a JWT must restrict allowed algorithms and validate the signature, issuer, audience, expiry, not-before time, and any application-required claims. Key selection and rotation also need an explicit trust model.

Keep claims minimal because bearer tokens can appear in clients and operational tooling. Revocation, logout, credential reset, and account deletion require a strategy beyond waiting for a long-lived token to expire. After token validation, still check the current user, tenant, role, and resource ownership for the requested operation.

## Retries and Resilience

Clients should retry only when method semantics and the application contract make repetition safe.

- Safe and idempotent methods are generally better retry candidates.
- A network timeout does not reveal whether a non-idempotent operation was applied.
- Respect `Retry-After` when the contract uses it.
- Use bounded attempts, backoff, jitter, and an overall deadline.
- Avoid synchronised retry storms.
- Test dependency timeouts, malformed upstream responses, partial failures, and recovery.

An idempotent method can still consume resources or trigger incidental effects, so retries must remain bounded.

## API Evolution and Versioning

Prefer compatible evolution when practical:

- add optional fields rather than changing existing meanings;
- tolerate unknown response fields in clients;
- define defaults for omitted request fields;
- avoid changing field types or enum behaviour unexpectedly;
- coordinate schema, client, and server rollout;
- publish deprecation and removal timelines;
- monitor usage before removing an operation or version.

Common versioning approaches include URI versions such as `/v1/orders`, media-type versions, and header-based negotiation. Each has trade-offs; consistency and a documented compatibility policy matter more than selecting a universally “correct” style.

## OpenAPI Contracts

An OpenAPI document can describe paths, operations, parameters, security requirements, schemas, and responses in a machine-readable form.

```yaml
openapi: 3.1.0
info:
  title: Orders API
  version: 1.0.0
paths:
  /orders/{orderId}:
    get:
      operationId: getOrder
      parameters:
        - name: orderId
          in: path
          required: true
          schema:
            type: string
      responses:
        "200":
          description: Order found
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/Order"
        "404":
          description: Order not found
components:
  schemas:
    Order:
      type: object
      required: [id, status]
      properties:
        id:
          type: string
        status:
          type: string
```

Validate both the implementation against the contract and the contract against intended behaviour. Schema conformance alone does not prove authorisation, state transitions, data integrity, or business correctness.

## Practical Requests

### cURL

Create a resource and include response headers:

```bash
curl --include \
  --request POST \
  --header "Content-Type: application/json" \
  --header "Accept: application/json" \
  --data '{"productId":"P100","quantity":2}' \
  https://api.example.com/orders
```

Retrieve a resource:

```bash
curl --include \
  --header "Accept: application/json" \
  https://api.example.com/orders/42
```

Fail a script when the server returns an HTTP error:

```bash
curl --fail-with-body --silent --show-error \
  https://api.example.com/orders/42
```

### Java HTTP Client

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

HttpClient client = HttpClient.newHttpClient();

HttpRequest request = HttpRequest.newBuilder()
        .uri(URI.create("https://api.example.com/orders/42"))
        .header("Accept", "application/json")
        .GET()
        .build();

HttpResponse<String> response = client.send(
        request,
        HttpResponse.BodyHandlers.ofString()
);

if (response.statusCode() != 200) {
    throw new AssertionError(
            "Expected 200 but received " + response.statusCode()
                    + " with body: " + response.body()
    );
}

String contentType = response.headers()
        .firstValue("Content-Type")
        .orElseThrow(() -> new AssertionError("Missing Content-Type"));

if (!contentType.startsWith("application/json")) {
    throw new AssertionError("Unexpected Content-Type: " + contentType);
}
```

A real test should parse the JSON and assert meaningful fields and invariants rather than relying on string matching.

## API Test Strategy

### Contract and Protocol

- supported methods and `Allow` behaviour;
- status codes and response content rules;
- required and optional headers;
- media types and content negotiation;
- request and response schema;
- caching and conditional-request behaviour;
- compatibility with published OpenAPI or consumer contracts.

### Functional Behaviour

- happy paths and important state transitions;
- required, optional, null, empty, minimum, maximum, and malformed values;
- duplicate and conflicting requests;
- filtering, sorting, pagination, and search;
- idempotency and repeated execution;
- persistence and side effects across dependent systems.

### Authentication and Authorisation

- every credential state;
- every role and permission boundary;
- object- and field-level access;
- tenant isolation;
- token audience, issuer, scope, and expiry where relevant.

### Reliability and Concurrency

- timeouts, retries, backoff, and rate limiting;
- concurrent updates and lost-update prevention;
- duplicated and out-of-order asynchronous messages;
- partial dependency failures;
- eventual consistency and polling behaviour;
- graceful degradation and recovery.

### Non-Functional Behaviour

- latency percentiles and throughput under representative load;
- payload and collection size limits;
- resource exhaustion and abuse cases;
- sensitive-data handling and auditability;
- observability through logs, metrics, and traces.

Test at the lowest level that proves the behaviour reliably. A small number of end-to-end API scenarios should not replace focused service, component, contract, and domain tests.

## Common Testing Mistakes

- Asserting only the status code.
- Treating every non-`200` response as failure even when another status is correct.
- Sending valid data only.
- Testing authentication without object-level authorisation.
- Sharing mutable test data across parallel tests.
- Depending on test execution order.
- Using fixed sleeps for asynchronous operations.
- Retrying failures until they disappear.
- Matching entire JSON strings when field-level assertions are clearer.
- Ignoring response headers and content types.
- Generating random data without recording it in failure output.
- Running destructive tests against an uncontrolled environment.

## Interview Approach

When asked how you would test an endpoint:

1. Clarify the resource, operation, users, and business risk.
2. Define the success contract: status, headers, representation, persistence, and side effects.
3. Partition inputs and identify boundaries.
4. Cover authentication, authorisation, tenant, and field-level access.
5. Test state transitions, idempotency, concurrency, retries, and failure paths.
6. Consider dependencies, asynchronous processing, caching, and observability.
7. Select suitable test levels and explain what to automate.
8. Describe test data, isolation, cleanup, and diagnostic evidence.

A strong answer prioritises risks instead of producing an unstructured list of status codes.

## Quick Checklist

You should be able to:

- distinguish REST constraints from HTTP and CRUD;
- explain safe and idempotent method semantics;
- choose appropriate methods and status codes;
- validate headers, media types, representations, and side effects;
- design stable resource URIs and collection operations;
- model consistent machine-readable errors;
- test caching and optimistic concurrency with validators;
- test authentication separately from authorisation;
- reason about retries, duplication, and asynchronous completion;
- assess schema compatibility and API evolution;
- design a layered, risk-based API test strategy;
- use cURL or an HTTP client to diagnose a real exchange.

## Further Reading

- [RFC 9110: HTTP Semantics](https://www.rfc-editor.org/rfc/rfc9110.html)
- [RFC 9457: Problem Details for HTTP APIs](https://www.rfc-editor.org/rfc/rfc9457.html)
- [RFC 10008: The HTTP QUERY Method](https://www.rfc-editor.org/rfc/rfc10008.html)
- [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [OWASP API Security Project](https://owasp.org/www-project-api-security/)

Return to [Quality Engineering](./README.md).
