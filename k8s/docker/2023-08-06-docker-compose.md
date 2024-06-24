<h1>Docker Compose</h1>
 
Using `Docker Compose` we can build a multi-container infrastructure with just a yaml file. We can build images, link containers, and launch applications with a simple `docker-compose up`
<h3>Docker Compose Yaml file</h3>
 
There are different versions of docker-compose that have additional support to help when building your dockerized infrastructure, for more information on this visit this [page](https://docs.docker.com/compose/compose-file/compose-versioning/).
  - This will have info on the changes and the updates of each version
Below is an example of a Docker Compose Yaml File:


* This is version 1 of a docker compose yaml file; it is in its most basic form and has many limitations
  - In version one docker compose attaches all the containers it runs in a default brigde network and uses links to enable communication between the containers

   ```yml
   redis: [name of container]
     image: redis [image to pull]
   db: 
     image: postgres:9.4
   vote:
     build: ./vote [which directory to build the image from]
     ports: [ports the container should map to on the host]
       - 5000:80
     links:
       - redis
   result:
     buiid: ./result
     ports:
       - 50001:80
     links:
       - db
   worker:
     build: ./worker
     links:
       - db
       - redis
   ```

* This is version 2 of a docker compose yaml file
  - Docker compose automatically creates a dedicated bridge network for the application, and then attaches all the containers to the network
  - All containers are then able to communicate with each other through their service names
    * This makes the use of links no longer necessary
    * We can also manage these networks, by defining them in the yaml file and specifying which of the defined networks, a container will have access to
  - This version also includes a `depends_on` feature to let docker compose know which order to spin up the containers
    * For more information on networks check out this [page](vms/vbox/2023-04-29-vbox-networking.md)

   ```yml
   version: "2"
   services:
     redis: #name of container
       image: redis # image to pull
       networks:
         - back-end
     db:
       image: postgres:9.4
       environment: # specifying environment variables for the container
         POSTGRES_USER: postgres
         POSTGRES_PASSWORD: postgres
       networks:
         - backend
     vote:
       build: ./vote #which directory to build the image from
       ports: # ports the container should map to on the host
         - 5000:80
       networks:
         - front-end
         - back-end
       depends_on:
         - redis
     result:
       buiid: ./result
       ports:
         - 50001:80
       networks:
         - front-end
         - back-end
       depends_on:
         - db
     worker:
       build: ./worker
       depends_on: 
         - redis
         - db
         - vote
         - result
       networks:
         - front-end
         - back-end

   networks:
     front-end:
     back-end:
   ```
