**Lecture 3:**
# Backend Notes (Exactly from the Video)

## What is a Backend?

* A backend is a **computer** that listens for requests.
* It can listen for:

  * HTTP requests
  * WebSocket requests
  * gRPC requests
  * Other types of requests
* It listens on an **open port** (for example 80 or 443) that is accessible over the Internet.
* Clients (browsers, apps, or other frontends) connect to the backend.
* Clients can:

  * Send data to the backend.
  * Receive data from the backend.
* It is called a **server** because it **serves** content.
* The content served can be:

  * Images
  * JavaScript files
  * HTML files
  * JSON
* The backend also accepts data sent by clients.

---

# How Backends Work

### Example Setup

The backend server is deployed on an **AWS EC2 instance**.

When the browser sends a request:

```
Browser
    ↓
Backend Server
    ↓
Response
```

The browser receives the response.

---

## Request Flow

### 1. Browser

The request starts from the browser.

---

### 2. Domain Name

The browser first uses the **domain name**.

Example:

```
backend-demo.domain.com
```

---

### 3. DNS Server

The request goes to the **DNS server**.

The DNS server contains different DNS records.

Two records mentioned are:

* **A Record**

  * Points to an IP address.

* **CNAME Record**

  * Points to another domain or subdomain.

---

### 4. A Record

The backend subdomain has an **A Record**.

```
backend-demo
      ↓
Public IP Address
```

The IP address points to the AWS EC2 instance.

---

### 5. AWS EC2 Instance

The request reaches the EC2 instance using the public IP address.

---

### 6. Firewall (Security Group)

Before entering the EC2 instance, the request passes through the AWS firewall.

The firewall decides which ports are allowed.

The video mentions allowing:

* SSH port
* HTTP (80)
* HTTPS (443)

If HTTP or HTTPS ports are **not allowed**, AWS blocks the request before it reaches the server.

---

### 7. Reverse Proxy (Nginx)

After passing the firewall, the request reaches **Nginx**.

The video describes Nginx as:

> A server that sits in front of other servers.

Purpose:

* Manage redirects
* Manage configurations from one place

---

### Nginx Configuration

Nginx:

* Listens on Port 80.
* Redirects traffic to HTTPS (443).
* Uses the configured domain name.
* Forwards requests to:

```
localhost:3001
```

This is where the Node.js backend is running.

---

### 8. Node Server

The backend server is a **Node.js server**.

It is managed using **PM2**.

The video shows two running processes:

* Frontend
* Backend

The backend listens on:

```
localhost:3001
```

Calling:

```
localhost:3001/users
```

returns the same response.

---

# Complete Backend Flow

```
Browser
      ↓
DNS Server
      ↓
AWS Public IP
      ↓
AWS Firewall
      ↓
EC2 Instance
      ↓
Nginx
      ↓
localhost:3001
(Node.js Backend)
```

---

# Why Do We Need Backends?

Example:

Instagram Like Button

When you press Like:

1. The app sends a request to the server.
2. The server identifies who performed the action.
3. The server saves the like.
4. The server identifies whose post was liked.
5. The server triggers a notification.
6. The friend receives the notification.

The backend stores information for **all users**, while each frontend only shows information relevant to the current user.

---

# Main Responsibility of a Backend

The speaker summarizes the backend's responsibility in one word:

**Data**

Backend is responsible for:

* Fetching data
* Receiving data
* Persisting (saving) data
* Performing actions involving data

---

# How Frontends Work

The frontend is deployed on the same AWS EC2 instance.

When the browser loads the frontend:

1. It requests the domain.
2. It first receives an HTML document.
3. Then it requests:

   * JavaScript files
   * CSS files
   * Images
   * Fonts

Each resource is fetched separately.

---

## Frontend Request Flow

The flow is almost the same:

```
Browser
      ↓
DNS
      ↓
AWS EC2
      ↓
Firewall
      ↓
Nginx
      ↓
localhost:3000
(Next.js Frontend)
```

The only difference is that Nginx forwards requests to **localhost:3000** instead of **localhost:3001**.

---

## Browser Rendering Process

After receiving the HTML:

* Browser downloads CSS.
* Browser downloads JavaScript.
* Browser downloads fonts.
* Browser applies styles.
* Browser runs JavaScript.
* JavaScript adds event listeners.
* Buttons and interactions begin working.

---

# Important Difference

### Backend

* Request is sent to the server.
* Server executes the code.
* Server sends back the result.

### Frontend

* Server sends the code.
* Browser executes the code on the user's machine.

---

# Browser Sandbox

Browsers execute JavaScript inside a **sandbox**.

This means JavaScript has limited access.

Browser JavaScript can access:

* DOM
* Local Storage
* Cookies
* Browser APIs

Browser JavaScript cannot freely access the operating system.

---

# CORS

The speaker introduces **CORS**.

It is described as:

* A browser security policy.
* It prevents JavaScript from calling APIs on different domains unless proper headers are provided.

A detailed explanation is left for a later video.

---

# Why Can't We Put Backend Logic in the Frontend?

The speaker gives four reasons.

### 1. Security

Browsers are highly restricted.

Backends often need access to:

* File system
* Environment variables
* Log files

Browsers do not allow this.

---

### 2. External APIs

Browsers cannot freely call external APIs.

They are restricted by CORS.

Backend servers do not have this limitation.

---

### 3. Databases

Backend servers use native database drivers.

Examples mentioned:

* PostgreSQL (`pg`)
* MongoDB

Backend servers maintain **connection pools**.

This avoids creating and destroying database connections for every request.

Browsers cannot maintain these persistent database connections.

If every browser connected directly to the database, the database would become overwhelmed.

---

### 4. Computing Power

Frontend applications run on many different devices.

Some devices may have:

* Low RAM
* Slow processors

Heavy business logic could slow down or crash the application.

Backend servers can be upgraded with more:

* CPU
* Memory

making them better suited for heavy processing.

---

 ** 5. Understanding HTTP for backend engineers, where it all starts **

### *1. Core Principles of HTTP*
HTTP (Hypertext Transfer Protocol) is an application-layer protocol (Layer 7 in the OSI model) used by clients and servers to communicate. It is built on two fundamental ideas:
*   *Statelessness:* The server retains no memory of past interactions. Every request is entirely self-contained and must include all the necessary information (like authentication tokens or cookies) for the server to process it. 
    *   Benefits: This simplifies server architecture and improves scalability, because a single server doesn't need to keep track of user sessions, and a server crash won't destroy a client's state.
*   *Client-Server Model:* Communication is always initiated by the client (e.g., a web browser) to request resources or actions, and the server waits for these requests to process and respond.

### *2. Transport Protocol & HTTP Versions*
HTTP relies on a reliable, connection-based transport protocol, almost universally **TCP (Transmission Control Protocol)**. Over the years, HTTP has evolved to improve how these TCP connections are handled:
*   *HTTP 1.0:* Opened a new TCP connection for every single request and response, which was highly inefficient and slow.
*   *HTTP 1.1:* Introduced *persistent connections* (`keep-alive`) as the default, allowing multiple requests to be sent over a single reused connection.
*   *HTTP 2.0:* Introduced multiplexing (multiple requests/responses concurrently on one connection), binary framing, header compression, and server push.
*   *HTTP 3.0:* Replaced TCP with QUIC (built over UDP) to establish faster connections and handle packet loss better, eliminating head-of-line blocking.

### *3. Anatomy of HTTP Messages*
Client-server communication happens via structured text messages.
*   *Request Message (Client to Server):* Contains a Request Method (e.g., GET/POST), the Resource URL, the HTTP version, Host domain, Headers, a blank line, and an optional Request Body.
*   *Response Message (Server to Client):* Contains the HTTP version, a Status Code (e.g., 200), a Status Value (e.g., OK), Headers, a blank line, and the Response Body.

### *4. HTTP Headers*
Headers are key-value pairs that act as metadata for the package being transmitted, allowing the system to be highly extensible and act as a "remote control" to dictate server behavior. 
*   *Request Headers:* Sent by the client to provide context (e.g., `User-Agent` identifies the browser, `Authorization` sends credentials).
*   *General Headers:* Apply to both requests and responses (e.g., `Date`, `Connection`, `Cache-Control`).
*   *Representation Headers:* Describe the message body (e.g., `Content-Type` for media format like JSON/HTML, `Content-Length` for byte size, `Content-Encoding` for gzip compression).
*   *Security Headers:* Protect against attacks (e.g., `Strict-Transport-Security` forces HTTPS, `Content-Security-Policy` prevents cross-site scripting, `Set-Cookie` with HTTP-only flags).

### *5. HTTP Methods and Idempotency*
Methods define the semantic *intent* of the client's request.
*   *GET:* Fetches data from the server without modifying anything.
*   *POST:* Submits new data to the server (includes a request body).
*   *PATCH:* Partially updates an existing resource.
*   *PUT:* Completely replaces an existing resource with the provided body.
*   *DELETE:* Removes a resource.
*   *OPTIONS:* Inquires about the server's capabilities (used heavily in CORS).

*Idempotency* is a crucial concept here:
*   *Idempotent Methods:* Can be executed multiple times and yield the exact same result on the server state (e.g., GET, PUT, DELETE).
*   *Non-Idempotent Methods:* Running them multiple times creates different results (e.g., submitting a POST request twice creates two separate resources).

### *6. Cross-Origin Resource Sharing (CORS)*
Browsers enforce a Same-Origin Policy, blocking web apps from making requests to different domains (origins). CORS is a security mechanism to bypass this safely.
*   *Simple Requests:* (Usually GET or POST with standard headers/content types). The browser automatically adds an `Origin` header. If the server allows the request, it replies with the `Access-Control-Allow-Origin` header containing the client's domain (or a `*` wildcard). If missing, the browser blocks the response.
*   *Pre-flight Requests:* Triggered if a request uses a non-simple method (PUT/DELETE), requires authorization headers, or uses a `application/json` content type. 
    *   The browser first fires an *OPTIONS* request asking the server if the route supports the intended method and headers.
    *   The server replies with a `204 No Content` status, explicitly listing allowed origins, methods, headers, and a `max-age` to cache this configuration. 
    *   If successful, the browser then sends the actual, original request.

### *7. Standardized Status Codes*
Status codes are three-digit numbers that act as a universal language to indicate the outcome of a request.
*   *1xx (Informational):* Indicates headers received; client can proceed (e.g., `100 Continue` for large uploads).
*   *2xx (Success):* 
    *   `200 OK`: Successful operation.
    *   `201 Created`: Usually follows a POST request.
    *   `204 No Content`: Successful, but no body to return (used in OPTIONS or DELETE).
*   *3xx (Redirection):*
    *   `301 Moved Permanently`: The resource has a new URL.
    *   `302 Found/Temporary Redirect`: Temporarily forward to a new route.
    *   `304 Not Modified`: Tells the client to use its locally cached version.
*   *4xx (Client Errors):* 
    *   `400 Bad Request`: Invalid data format sent by client.
    *   `401 Unauthorized`: Missing or invalid authentication token.
    *   `403 Forbidden`: Authenticated, but lacks necessary permissions.
    *   `404 Not Found`: Incorrect URL or deleted resource.
    *   `405 Method Not Allowed`: Using the wrong method for a route.
    *   `409 Conflict`: Business logic violation (e.g., duplicate username).
    *   `429 Too Many Requests`: Client has hit rate limits.
*   *5xx (Server Errors):*
    *   `500 Internal Server Error`: An unhandled exception crashed the server.
    *   `501 Not Implemented`: Feature not yet supported.
    *   `502 Bad Gateway` / `504 Gateway Timeout`: Issues originating from proxies or load balancers failing to reach upstream servers.
    *   `503 Service Unavailable`: Server down or under maintenance.

### *8. HTTP Caching*
Caching reuses previously downloaded responses to save bandwidth and load times.
*   When a client first fetches a resource, the server responds with the payload alongside three headers: `Cache-Control` (sets max duration), `ETag` (a unique hash of the payload), and `Last-Modified`.
*   On subsequent requests, the client sends conditional headers: `If-None-Match` (carrying the ETag) or `If-Modified-Since`.
*   If the data on the server hasn't changed, the server saves bandwidth by sending an empty `304 Not Modified` response, instructing the browser to use its cached copy. If it has changed, it sends a `200 OK` with the new data and a new ETag.

### *9. Content Negotiation and Compression*
Clients and servers can negotiate the best format to exchange data.
*   The client sends preferences via `Accept` (e.g., `application/json` vs `application/xml`), `Accept-Language` (e.g., `en` vs `es`), and `Accept-Encoding` (e.g., `gzip`).
*   The server responds with the appropriate format.
*   *Compression:* By negotiating an encoding like `gzip`, a server can drastically compress text responses (e.g., shrinking a 26MB JSON payload down to 3.8MB) to save massive amounts of network bandwidth.

### *10. Handling Large Data Transfers*
*   *Large Client Uploads (Images/Video):* Standard JSON is terrible for binary data. Instead, clients use a `multipart/form-data` request. This breaks the file into chunks separated by a unique string delimiter defined in the `boundary` header.
*   *Large Server Downloads:* To prevent timing out, the server streams the file in chunks using `Content-Type: text/event-stream` and `Connection: keep-alive`. The browser continually appends these chunks until the transfer finishes.

### *11. Security (SSL/TLS & HTTPS)*
*   *TLS (Transport Layer Security):* The modern, secure replacement for the outdated SSL protocol.
*   It encrypts data in transit to prevent interception (eavesdropping) or tampering, utilizing certificates to verify the server's identity.
*   *HTTPS:* Simply the standard HTTP protocol wrapped inside a secure TLS connection.


------- 

**Lecture 11:**
### *1. What is REST? (History & Definition)*
*   *The Origin:* In the 1990s, Tim Berners-Lee invented the World Wide Web. However, as it grew exponentially, it faced a massive scalability crisis. In 2000, Roy Fielding proposed a standardized architectural style to solve this, which he named **REST (Representational State Transfer)**.
*   *Breaking Down the Name:*
    *   *Representational:* Resources (data) on the web can have multiple representations based on who is asking. For an API client, it might be represented as JSON; for a browser, as HTML. 
    *   *State:* The current condition or attributes of a specific resource (e.g., the items and total price currently sitting in a user's shopping cart).
    *   *Transfer:* Moving these representations between the client and server using standard HTTP methods.

### *2. The 6 Constraints of REST Architecture*
To be truly "RESTful" and achieve high scalability, a system must follow these constraints proposed by Roy Fielding:
1.  *Client-Server:* A strict separation of concerns. The client handles the UI/UX, and the server handles data and business logic.
2.  *Uniform Interface:* A standardized way for all web components to communicate, providing a consistent interface across services.
3.  *Layered System:* Architecture is composed of hierarchical layers (e.g., adding load balancers or proxy servers in the middle). A layer can only see the immediate layer below it, improving security and scaling.
4.  *Cache:* Server responses must explicitly label themselves as cacheable or non-cacheable to help reduce server load and improve speed.
5.  *Stateless:* The server retains no memory of past requests. Every client request must contain all the information necessary to process it.
6.  *Code on Demand (Optional):* Servers can temporarily extend client functionality by sending executable code (like JavaScript) to the client.

### *3. Anatomy of a RESTful Route*
When designing the URL for an API, you should follow standard, hierarchical naming conventions:
*   *The Structure:* A typical API URL follows this format: `https://api.example.com/v1/books`. This includes the secure scheme (`https`), an API subdomain (`api.`), API versioning (`v1`), and the path/resource (`books`).
*   *Always Use Plural Nouns:* The path segment identifying a resource should always be a plural noun (e.g., `/books` or `/organizations`), even if you are only fetching a single item via an ID (e.g., `/books/123`).
*   *Formatting:* Never use spaces or underscores in a URL. If a resource has a readable slug (like "Harry Potter"), convert it to lowercase and replace spaces with hyphens (e.g., `/books/harry-potter`).
*   *Hierarchical Nesting:* The forward slash `/` denotes a hierarchy. For instance, `/organizations/123/projects` semantically means "fetch the projects that belong specifically to organization 123".

### *4. HTTP Methods and "Idempotency"*
*Idempotency* is a crucial concept: an operation is idempotent if performing it multiple times yields the exact same side-effects on the server as performing it just once.
*   *GET (Idempotent):* Used to fetch data. Calling it a thousand times won't alter the server's state.
*   *PATCH vs. PUT (Idempotent):* Both are used to update data, and applying the same update payload repeatedly doesn't change the final result. 
    *   *PATCH:* Used when updating only partial fields (e.g., just changing a user's status).
    *   *PUT:* Used when completely replacing the entire resource representation.
*   *DELETE (Idempotent):* Used to remove a resource. The first call deletes it; subsequent calls will return a 404 Not Found error, but no further side-effects happen to the server state.
*   *POST (Non-Idempotent):* Used to create new resources. If you send the same POST request 10 times, you will create 10 distinct resources with 10 different database IDs.

### *5. Custom Actions (The Exception to CRUD)*
Sometimes, an action doesn't fit neatly into standard CRUD (Create, Read, Update, Delete) boxes. For example, "cloning" a project or "archiving" an organization triggers a complex web of background tasks that goes beyond a simple database update.
*   *The Rule:* When an action does not fall under standard methods, REST specifications dictate making it a *POST* request. 
*   *Route Design:* Append the custom action as a verb at the very end of a specific resource route. Example: `POST /projects/123/clone` or `POST /organizations/5/archive`.

### *6. Designing robust "List" APIs (GET)*
When returning a list of items, an API must be equipped to handle heavy data loads using three features:
1.  *Pagination:* Sending thousands of records at once crashes clients and bottlenecks networks. Instead, send data in chunks (pages). A paginated response should include:
    *   `data`: The array of objects for that specific page.
    *   `total`: The absolute count of items in the database (useful for frontend UI).
    *   `page`: The current page number being viewed.
    *   `totalPages`: The maximum number of pages available.
2.  *Sorting:* Clients use query parameters like `sortBy=name` and `sortOrder=ascending` to organize the returned data.
3.  *Filtering:* Clients pass properties in the query parameters to filter results (e.g., `?status=active&name=org`).

### *7. Handling HTTP Status Codes Properly*
*   `200 OK`: Standard success response for fetching, updating, or performing custom actions.
*   `201 Created`: Explicitly returned when a POST request successfully creates a new database entity.
*   `204 No Content`: Returned after a successful DELETE operation, indicating success but an intentionally empty response body.
*   *The 404 (Not Found) Rule:* 
    *   Return a `404` only when a client requests a specific, single resource ID that does not exist (e.g., `GET /users/999`). 
    *   If a client requests a *List API* (e.g., fetching all users with the name "Zack") and no matches are found, do *not* return a 404. Instead, return a `200 OK` with an empty array `[]`. 

### *8. Golden Rules & Best Practices for API Engineers*
*   *Extract Nouns from UI:* Before coding, look at the frontend wireframes (like Figma) to figure out what data the user interacts with. Identify the "nouns" (e.g., projects, users, tasks); these become your API's core resources.
*   *Implement "Sane Defaults":* Your server should not crash if a client forgets to send optional data. If a client doesn't pass a pagination limit, default it to `10`. If they don't provide a sort order, default to sorting by `created_at` in `descending` order. If a new organization is created without a status, default it to `active`.
*   *Total Consistency:* Be ruthlessly consistent. JSON payloads should always use `camelCase`. If an endpoint expects a field called `description`, do not abbreviate it to `desc` in another endpoint. Inconsistencies force other developers to guess, leading to bugs.
*   *Interactive Documentation:* Always use tools like Swagger/OpenAPI to generate an interactive playground. This acts as both documentation for front-end developers and a testing ground for you.
