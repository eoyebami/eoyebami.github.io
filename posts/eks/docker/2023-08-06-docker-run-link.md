
## NOTE: Using `--link` flag is deprecated and will be removed from docker in the future
## This is only to understand the concept before moving this to using docker compose or other networking methods
<h1>Docker Run: Link flag</h1>
We will be going over how separate docker containers are able to resolve one another. If we sping up 2 containers (a webapp and a db tha the webapp writes to), how does the webapp container locate the db container. 
- Within the webapp code, we see that its looking for a db with the hostname `redis` for example, using the `--link` flag in `docker run`, we can point the webapp container to the redis db container.
<h3>Using the Link flag</h3>
- First you'll need to spin up the container you are going to link, before spinning up the container that relies on the link
  * `docker run -d --name redis redis`
- Then link the db container to the webapp
  * `docker run -d --name webapp -p 5000:80 --link redis:redis webapp`
    - the format for using the `--link` flag is `[container-name]:[host]`, so we've linked the redis container to the hostname redis within the webapp container
<h3>How does this work?</h3>
- When we use the `--link` flag docker knows to modify the /etc/hosts file in the container to include the container we are trying to link
- It creates this entry in the hosts file, with the following:
  * IP of the container
  * Name of the container
  * Hostname of the container

