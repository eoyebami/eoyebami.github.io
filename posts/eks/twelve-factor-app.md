<h1>Twelve Factor App Methodology</h1>

1. Codebase: Each service or application should have its own codebase where developers can modify and change code at the same time (ex. `Git/Github`)
2. Dependencies: Never relies on implicit existence of system-wide packages (ex: `explicitly declare and isolate the dependencies for the application`)
  ex:
    - using a requirements.txt in python code to hardcode the version of a dependencies you want installed
    - using a versions.tf in terraform 
  * Isolate? Similar to using virualt environmentst `(venv)` in python to isolate applications, which will in turn have their own dependecy versions specified in their own requirement.txt. Docker is also another great example of this `(spinning up containers that can host their own dependencies and environments isolated from the host machine)`.
    - Explictly declaring dependencies in code
    - Explictly defining a version so that there are no discrepencies
    - Dockerizing application so there you have an isolated environment to run different applications which may be using different versions of dependencies.
3. Config: Environment configuration should be kept separate from main application code. 
   * Defining and env file that will load environment variables to the app. So we can launch our application in different environments and the only thing that would need to change in each environment, would be the env file `(branching in version control would make this alot simplier)`
4. Backing Services: We should be able to point our application from one backing service to another and it should just work, application shouldn't be specific to one remote or local backing service
   * Redis: stores data
   * ECR: host images
   * Artifactory: store Artifacts
   * SMTP: send emails
5. Build, Release, and Run:
   * Build: conversion of code into an executable format `(./app, app.exe, dockerimage)`
   * Release: once built, the executable and the config files become the release object `(should have a unique release ID: flask-app-test:2023-02-25)`
   * Run: where you run the release object in different env `(Jenkins)`, makes it easier to rollback to previous release should there be an error or mistake  
6. Processes: 12-factor processes are stateless and share nothing. They should be stateless and all data should be stored in a databases, where all the process and applications can then pull the data from. This will ensure that data and session information are not dependent on the application, so if application and process crash, then the data will be prevalent. 
7. Port Binding: binding applications to different ports 
8. Concurrency: Using Horizontal Autoscaling, so we have multiple servers with multiple containers running the application concurrently, along with a loadbalancer to balance the traffic. 
9. Disposability: processes should be disposable whenever it is no longer necessary
10. Dev/Prod Parity: increasing speed between these stages, dev will be involved with operations
   * Dev: where code is developed
   * Staging: where code is tested in a prod like env
   * Prod: prod env
11. Logs: a twelve-factor app never concerns itself with routing or storage of its ouputs, logs are stored in a centralized location, which will then use an agent to transfer to a centralized location
   * ELK stack
   * Splunk
12. Admin Processes: Admin tasks should be run in a separate setup 
