<h1>API calls</h1>

<h3>REST Architectural Style:</h3>
REST stands for `Representational state transfer`, it is a style that promotes the transfer, accessing, and manipulation of textual data representations in a stateless manner. In laymen's terms, the request (via a url) from the client to the server must contain all the information necesary for the server to understnad and complete the request. A RESTful API service is exposed through a Uniform Resource Locator (URL). [I[O
  * You make a request to the API service through its URL (its locator, a request for a resource) in order to communicate with the web server. 
In a RESTful architecture, the web services are able to be accessed and manipulated using HTTP methods. 
  * The specific resources we will be accessing are identified by unique URLs. 
  * It is a client-server architecture: client and server are separate entities that communicate with each other over a network
  * Stateless: Each request has all the necessary infor required to carry out request, without server needing to store any session information about the client 
  * Cacheable: requires that a response be labled as cacheable or non-cacheable
    - if it is cacheable then the client application gets the right to reuse the response data for later requests anf a specified period

<h3>What is an API</h3>
An API is an `Application Programming Interface`, which is a set of rules that allow programs to talk to each other. An API is a set of protocols, routines,, and tools that enable different software programs to communicate with each other
  - A weather app might use an API provided by a weahter service in order to retrieve the current temperature and forcast for a specific location.
  - You make a request through a service, the service sends request to api of another service; secondary service sends response via api back to primary service, which outputs via the UI

`REST` determines how the API looks like, it is a set of rules that developers follow when they create their api. One of those rules state that you should be able to get a piece of data (called a resource) when you link to specific URL. 
  - Each URL is called a request while the data sent back to you is called a reponse. 

<h3>The Anatomy of a Request:</h3>
* The endpoint: or route is the url you request for 
  - the root-endpoint is the starting point of the API you are requesting (i.e. `https://api.github.com`)
  - the path determines the resource you are requesting for (i.e. https://www.smashingmagazine.com`/tag/javascript/`)
    * To understand what paths are available to you, read the api documentation
    * `:` denotes a variable (i.e. `/users/:username/repos`)
  - the query parameters give you an option to modify your request with key-value pairs and always begin with a `?`, each pair is separated with an `&`.
    * `?query1=value1&query2=value2`
    * ex: [github-repos-api](https://api.github.com/users/eoyebami/repos?sort=pushed) : this api requests all data partaining to my pushed repos
    * try using the curl command to make this api call from the terminal: `curl https://api.github.com/users/eoyebami/repos\?sort\=pushed`
   
* Note: the common format for sending and requesting data through a REST API is json, also when making an API call with curl, ensure you prepend all special charaters with `\` so that the cli will interpret them as normal characters

| Root endpoint                     | Path            |
| ---                               | ---             |
| https://www.smashingmagazine.com/ | /tag/javascript |

* The method:
  - GET
  - POST
  - PUT
  - PATCH
  - DELETE
* <h5>Curl Command Utilizing HTTP Methods:</h5>
  * `curl -X GET https://api.github.com/users/eoyebami/repos?sort=pushed` 
    - `-X` or `--request` indicates the type of request you'd like to make
    - If you try to `curl -X POST https://api.github.com/user/repos` to create a new repo it will fail with the below error
```
{
  "message": "Requires authentication",
  "documentation_url": "https://developer.github.com/v3"
}
``` 
* The headers:
  - Headers are used to provide information to both the client and server, it can be used for:
    * Authentication
    * Providing information about the body content
    * More info on headers at [2023-05-01-http-requests](posts/http/2023-05-01-http-requests.md)
* <h5>Curl Command Utilizing Header Flag:</h5>
  * Headers are property-value pairs that are separated with a colon
    - "Content-Type: application/json" : which tells the server to expect JSON content
  * `curl -X GET -H "Content-Type: application/json" https://api.github.com`
    - to view the headers you've sent, you can use `v` or `--verbose` option 
    - `curl -X GET -H "Content-Type: application/json" https://api.github.com -v`
* The data(or body):
  - The data contains information you want to send to the server
  - The data option is only used in `POST`, `PUT`, `PATCH`, `DELETE` requests
* <h5>Curl Command Utilizing Data Flag:</h5>
  * You used the `-d` or `--data` option to send data via curl
  * `curl -X POST <URL> -d property1=vale1 -d property2=value2`
    ```
    curl -X POST <URL> \
     -d property1=vale1 \
     -d property2=value2
    ```
  * If you wish to send you data in json format, you can do this:
    ```
    curl -X POST https://requestb.in/1ix963n1 \
     -H "Content-Type: application/json" \
     -d '{
      "property1":"value1",
      "property2":"value2"
    }'
    ```
  * If you wish to add a file as your source of data, you can do this:
   ```
    curl -X POST https://requestb.in/1ix963n1 \
     -H "Content-Type: application/json" \
     -d @file.txt
   ```
