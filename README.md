# Popeye

[Docker](https://www.docker.com) is one of today’s most popular containerization software.

It allows the packaging of applications, and the runtime environments (down to the operating systems) they need, which in return allows them to work wherever Docker is installed.

Like the brave sailor that Popeye is, containers can also confidently sail across the vast ocean of operating systems and configurations, being sure of working wherever they might end. As such, containers can be used on any host OS where Docker is installed.

During this project, you are going to master the basics of containerizing applications and describing multi-containers infrastructures with Docker and [Docker Compose](https://docs.docker.com/compose/).

## GENERAL DESCRIPTION

You will have to containerize and define the deployment of a simple web poll application.

There are five elements constituting the application:

- **_Poll_**, a Flask Python web application that gathers votes and push them into a Redis queue.
- A **Redis queue**, which holds the votes sent by the Poll application, awaiting for them to be consumed by the Worker.
- The **_Worker_**, a Java application which consumes the votes being in the Redis queue, and stores them into a PostgreSQL database.
- A **PostgreSQL database**, which (persistently) stores the votes stored by the Worker.
- **_Result_**,a Node.js web application that fetches the votes from the database and displays the... well,result. ;)

Do not worry, you do not have to code them! Popeye is generous, the code of these applications is given to you on the intranet’s project page.

```text
In DevOps, it is especially important that you take the time to research and understand
the technologies you are asked to work with, as you will need to understand how and by
which way you can configure them as needed.
```

## DOCKER IMAGES

You have to create 3 images.

The specifications for each image are as described below.

```text
Popeye does not like the ENTRYPOINT instruction, so you must not use it in this project.
```

```text
Some applications might not be compatible with the latest versions of the official
Docker Hub images they have to be based on. You must take the time to find the
compatible version if needed.
```

```text
Furthermore, latest versions of other elements (like databases) might also not be compatible
with the applications; it is part of your duties to find what versions the different
elements of your infrastructure need.
```

### POLL

The image must be based on a Python official image.
The dependencies of the application can be installed using the following command:
`pip3 install -r requirements.txt`
The application must expose and run on the port 80 and can be started with:
`flask run --host=0.0.0.0 --port=80`

### RESULT

The image must be based on an official Node.js version 12 Alpine image.
The application must expose and run on the port 80.
The dependencies of the application can be installed using the following command:
`npm install`

```text
Be careful about the location where this command has to be run.
```

```text
The node_modules folder must be excluded from the build context.
```

### WORKER

The image will be built using a multi-stage build.

### FIRST STAGE - COMPILATION

The first stage must be based on `maven:3.8.4-jdk-11-slim` and be named `builder`.
It must be used to build (natuurlijk) and package the Worker application using the following commands:

- `mvn dependency:resolve`, from within the folder containing `pom.xml`;
- then, `mvn package`, from within the folder containing the `src` folder.
It generates a file in the `target` folder named `worker-jar-with-dependencies.jar` (relative to your `WORKDIR` ).

### SECOND STAGE - RUN

The second stage must be based on `openjdk:11-jre-slim`.
This is the one really running the worker using the command:
`java -jar worker-jar-with-dependencies.jar`

```text
Your Docker images must be simple, lightweight and not bring too much things.
```

```text
Connection information (e.g.: addresses, ports, or credentials) is hard-coded into the
applications. However, in order to make them ready for true deployment, you must use
environment variables. Update the applications’ code if necessary.
```

## DOCKER COMPOSE

You now have 3 Dockerfiles that create 3 isolated images. It is now time to make them all work together using Docker Compose!

Create a `docker-compose.yml` file that will be responsible for running and linking your containers. You must use version 3 of Docker Compose’s syntax.

Your Docker Compose file should contain:
**5 services** :

- `poll`:
  - builds your `poll` image;
  - redirects port 5000 of the host to the port 80 of the container.
- `redis`:
  - uses an existing official image of Redis;
  - opens port 6379.
- `worker`:
  - builds your `worker` image.
- `db`:
  - represents the database that will be used by the apps;
  - uses an existing official image of PostgreSQL;
  - has its database schema created during container first start.
- `result`:
  - builds your `result` image;
  - redirects port 5001 of the host to the port 80 of the container.

```text
Databases must be launched before the services that use them,
because these services are depending on them.
```

**3 networks** :

- `poll-tier` to allow `poll` to communicate with `redis`.
- `result-tier` to allow `result` to communicate with `db`.
- `back-tier` to allow `worker` to communicate with `redis` and `db`.

**1 named volume** :

- `db-data` which allows the database’s data to be persistent, if the container dies.

```text
The path of data in the official PostgreSQL image is documented on Docker Hub.
```

```text
Do not add unnecessary data, such as extra volumes, extra networks, unnecessary inter
container dependencies, or unnecessary container commands/entrypoints.
```

Once your `docker-compose.yml` is complete, you should be able to run all the services and observe the votes
you submitted to the `poll`’s webpage onresult’s webpage.

Your containers must restart automatically when they stop unexpectedly.

```text
The environment variables or environment variables files (depending on what you
choose to use) have to be defined in the docker-compose.yml file.
```

```text
The environment variables defined by ENV instructions in Dockerfiles, or the environment
variables files not specified in the docker-compose.yml file will not be taken into account.
```

## TECHNICAL FORMALITIES

Your project will be entirely evaluated with Automated Tests, by analyzing your configuration files (the different Dockerfiles and `docker-compose.yml`).

In order to be correctly evaluated, your repository must at least contain the following files:

```text
.
├── docker-compose.yml
├── schema.sql
├── poll
│   └── Dockerfile
├── result
│   └── Dockerfile
└── worker
    └── Dockerfile
```

```text
poll, result and worker are the directories containing the applications,
given to you on the intranet.
```

## FINAL MARK

### MARK: 17 / 17 (100%)

- Image Poll (3 / 3)
- Image Result (4 / 4)
- Image Worker (3 / 3)
- Docker Compose (7 / 7)
