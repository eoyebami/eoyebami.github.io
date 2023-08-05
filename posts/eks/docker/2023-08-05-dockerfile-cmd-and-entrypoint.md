<h1>Dockerfile: CMD vs Entrypoint</h1>
These both set instructions set within a Dockerfile, but exactly are the difference between the two?
<h3>CMD</h3>
Values set within the `CMD` instructions, are commands that should be run with the container starts

```
FROM ubuntu

RUN apt-get update
RUN apt-get python-pip

CMD ["sleep", "5"]
```

- In the example above we have specified that `apt-get update` and `apt-get install python-pip` be run when the image is created
  - However, we have hardcoded that whenever the image is run in a container, it must always start with a `leep 5` command.
  * The format of using `CMD` instruction is `CMD ["command", "param1" ]` 
    - We can override this instruction on container start up: `docker run <image> sleep 10`, however this is not industry standard and a better alternative to this is to use `ENTRYPOINT` instruction
<h3>ENTRYPOINT</h3>
Values set within `ENTRYPOINT` instruction will also run when container starts

```
FROM ubuntu

RUN apt-get update
RUN apt-get python-pip

ENTRYPOINT ["sleep"]
```

- In the example above we have set the command to sleep, on start up we can give the parameter for the command
  * `docker run <image> 10`
    - which translates to `docker run <image> sleep 10` due to the `ENTRYPOINT` instruction
    - the parameters you provide will be appended to the `ENTRYPOINT` instruction coded in the Dockerfile
  * You can also override the `ENTRYPOINT` instructions by running a `--entrypoint` flag at container start up and still set the parameter as well
    - `docker run --entrypoint ls <image> -R`
  * However an issues arises if you do not specify a parameter at the container start up
    - The command could fail to run if it relies on a parameter
    - This is where both `ENTRYPOINT` and `CMD` will be used together to avoid this issue
<h3>CMD & ENTRYPOINT</h3>
Using `CMD` and `ENTRYPOINT` together will allow you to hardcode default parameters for the command

```
FROM ubuntu

RUN apt-get update
RUN apt-get python-pip

ENTRYPOINT ["sleep"]

CMD ["5"]
``` 

- In the example above we provided the command that should be run in the `ENTRYPOINT` instruction and the parameters for that command in the `CMD` instructions
  * This means by default, in this example, at container start up, `sleep 5` will be run
  * However, we can still override the parameter in `docker run <image> 10`, to override the `CMD` value

- `CMD` lets us hardcode command and parameter, but in order to override we must also provide command and parameter at container start up
- `ENTRPOINT` lets us hardcode a command and specifiy the parameter at container start up, but if a parameter is not set then the command could fail
- Utilizing both `CMD` and `ENTRYPOINT` will allows us to hardcode command and default parameter, and still override the parameter at container start up
