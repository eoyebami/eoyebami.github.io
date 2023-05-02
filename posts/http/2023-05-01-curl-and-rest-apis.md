<h3>API calls</h3>

<h2>REST Architectural Style:</h2>
REST stands for `Representational state transfer`, it is a style that promotes the transfer, accessing, and manipulation of textual data representations in a stateless manner. In laymen's terms, the request (via a url) from the client to the server must contain all the information necesary for the server to understnad and complete the request. A RESTful API service is exposed through a Uniform Resource Locator (URL). [I[O
  * You make a request to the API service through its URL (its locator, a request for a resource) in order to communicate with the web server. 
In a RESTful architecture, the web services are able to be accessed and manipulated using HTTP methods. 
  * The specific resources we will be accessing are identified by unique URLs. 
  * It is a client-server architecture: client and server are separate entities that communicate with each other over a network
  * Stateless: Each request has all the necessary infor required to carry out request, without server needing to store any session information about the client 
  * Cacheable: requires that a response be labled as cacheable or non-cacheable
    - if it is cacheable then the client application gets the right to reuse the response data for later requests anf a specified period

<h2>What is an API</h2>
An API is an `Application Programming Interface`, which is a set of rules that allow programs to talk to each other. An API is a set of protocols, routines,, and tools that enable different software programs to communicate with each other
  - A weather app might use an API provided by a weahter service in order to retrieve the current temperature and forcast for a specific location.
  - You make a request through a service, the service sends request to api of another service; secondary service sends response via api back to primary service, which outputs via the UI

`REST` determines how the API looks like, it is a set of rules that developers follow when they create their api. One of those rules state that you should be able to get a piece of data (called a resource) when you link to specific URL. 
  - Each URL is called a request while the data sent back to you is called a reponse. 

<h2>The Anatomy of a Request:</h2>
* The endpoint: or route is the url you request for 
  - the root-endpoint is the starting point of the API you are requesting (i.e. `https://api.github.com`)
  - the path determines the resource you are requesting for (i.e. https://www.smashingmagazine.com`/tag/javascript/`)
    * To understand what paths are available to you, read the api documentation
    * `:` denotes a variable (i.e. `/users/:username/repos`)
  - the query parameters give you an option to modify your request with key-value pairs and always begin with a `?`, each pair is separated with an `&`.
    * `?query1=value1&query2=value2`
    * ex: [github-repos-api](https://api.github.com/users/eoyebami/repos?sort=pushed) : this api requests all data partaining to my pushed repos
    * try using the curl command to make this api call from the terminal: `curl https://api.github.com/users/eoyebami/repos\?sort\=pushed`
   
* [Note]: the common format for sending and requesting data through a REST API is json, also when making an API call with curl, ensure you prepend all special charaters with `\` so that the cli will interpret them as normal characters

| Root endpoint                     | Path            |
| ---                               | ---             |
| https://www.smashingmagazine.com/ | /tag/javascript |

* The method:
* The headers:
* The data(or body):
