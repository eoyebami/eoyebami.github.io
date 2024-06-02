<h1>HTTP (Hyper Text Transfer Protocol) Requests</h1>

HTTP is responsible for communication between web servers and clients, it is the protocol of the web. Everytime you log on to a browser or site, you enter what is called the HTTP Request & Reponse cycle.
* HTTP is stateless: Meaning each transaction is completely independent
  - It does not store any data to remember previous transactions
* HTTPS (Hyper Text Transfer Protocol Secure): Data that is sent is encrypted
  - Data is encrypted by SSL (Secure Socket Layer) or by TLS (Transport Security Layer)

<h3>HTTP Methods:</h3>
When a request is made to a server it has so kind of method attached to it, the main ones are included below:
* GET: retrieves data from the server 
  - loading a webpage
  - loading assests
* HEAD: similar to a `GET` request but without the response body
* TRACE: performs a message loop-back test along the path to the target resource
  - used to echo/debug web server connections 
  - returns the full HTTP request to the client 
  - dangerous if enabled, skips authorization verification
* PATCH: applies partial modificiations to a resource 
* POST: posting or submitting data to the server
  - submitting a form 
  - submitting a blog post
* PUT: update data already on the server
  - updating a form 
  - updating a blog post
* DELETE: deletes data on the server
  - deleting a form
  - deleting a blog post

<h3>HTTP HEADER Fields:</h3>

|Method | Path                                         | Protocol |
|GET    | /tutorials/other/top-20-mysql-best-practices | HTTP/1.1 |

* GENERAL HEADER Fields:
  - Resquest URL: path
  - Request Method: HTTP method
  - Status Code: 100-500
  - Remote Address: ip-addr of web server
  - Referrer Policy: how much referrer info should be included with the request
* RESPONSE HEADER Fields:
  - Server: type of web server
  - Set-Cookie: used to send a cookue from the server to the user agent, so that the user-agent can send it back to the server (cookies are text files with small pieces of data that are used to identify your computer)
    * Browser cookes are identified and read by `name=value` pairs
    * You receive a cookie (holds data that indentifies you and your session in the server) to give back to the server at a later time (so the server can retrieve your session)
  - Content-Type: indicates the original media type (e.g `text/html; charset=utf-8`)
  - Content-Length: indicates the size of the message body, in bytes, sent to the recipient
  - Date: contains date and time at which message originated
* REQUEST HEADER Fields:
  - Accept-xxx: indicates which content types, the client is able to understand (expressed as MIME types; e.g type/subtype;parameter=value)
  - Cookies: contains stored HTTP cookies (associated with the server; i.e. previously send by the server with the `Set-Cookie` header)
  - Content-type: indicates original media type (e.g `application/json`)
  - Content-Length: indicates the size of the message body, in bytes, sent to the recipient
  - Authorization: can be used to provide credentials that authenticate a user-agent with a server
  - User-Agent: characteristic string that lets servers and network peers identify the app, OS, vendor, or version of the requestion user-agent
  - Referrer: contains the absolute or partial address from which a resouse has been requested
Testing:
- Go unto a site
- Right Click -> Inspect -> Network
- Reload Page
- See all the GET requests made to load site
- Click on a request -> Headers
  * Here you can viewe the `GENERAL, RESPONSE, and REQUEST` header fields

<h3>Error Codes:</h3>
* 1xx: Informational
  - Request received/processing
* 2xx: Success
  - Success received, understood, and accepted
  - `200`: OK
  - `201`: OK created
* 3xx: Redirect
  - Further action must be taked/ redirect
  - `301`: moved to new URL
  - `304`: Not modified
* 4xx: Client Error
  - Request does not have what it needs
  - `400`: Bad request
  - `401`: unauthorized
  - `404`: not found
* 5xx: Server Error
  - Server failed to fulfil an apparent valid request
  - `500`: Internal server error
  - `503`: Service Unavailable 
