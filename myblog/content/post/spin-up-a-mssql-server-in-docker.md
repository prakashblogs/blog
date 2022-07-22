---
title: "Spin Up a MsSQL Server in Docker"
date: 2022-07-17T23:36:54+05:30
---

You plan to build an application that talks to a database, but you're procrastinating the idea because you don't want to go through the hassle of doing all the database setup yourself.

Tadaa.., we have Docker to the rescue. Docker is an example of what we call _containerization tool_ in the technical lingo. Here we are gonna skip through the download and installation of Docker, you can find the detailed instructions depending on your machine type on their [getting started](https://www.docker.com/get-started/) page.

Now that we have docker running on our machine, let us go through these simple steps to run a sql server inside docker.

#### Creating a Docker Compose File

A docker compose file is a simple text file with `.yml` extension, where we write all the steps required to get our service(mssql server in our case) up and running inside docker.

1. Create a file and name it as _sqlserver.yml_
2. Open the file and paste the following

> ```
> version: "3"
> services:
>  sqlserver:
>    image: "mcr.microsoft.com/mssql/>server:2019-latest"
>    container_name: sql
>    environment:
>      SA_PASSWORD: "myPassword8"
>      ACCEPT_EULA: "Y"
>    ports:
>      - "1433:1433"
>    volumes:
>      - "sqlservervolume:/var/opt/mssql"
> volumes:
>  sqlservervolume:
> ```

3. Set your desired password in _SA_PASSWORD_

#### Running the compose file

We will run the `docker-compose` command and pass the `sqlserver.yml` file as argument.
But before that make sure you have opened a CLI and you are in the same directory as the compose file. Then run the following command

> `docker-compose -f sqlserver.yml up`

#### Port Mapping

> Here we understand the connectivity between host machine and the docker container.

The above command will start up a new container named _sql_ where the server would be running and listening on port `1433`. Now the question is how do we connect to the server running on the port `1433` of the container?

To answer that we will have to understand the concept of _port mapping_. Let us take a look at our _yml_ file, where we have a _ports_ section in which we have written `1433:1433` what does this do?
This is where port mapping happens. We have instructed docker that the container port `1433` on the right of the colon), should be mapped to `1433` port on the host machine(left hand side of the colon).

This enables us to talk to the database on `1433` port of our machine, as it _relays_ the messages to `1433` port inside the container.

To understand it better, we could have mapped any other free port of our host to `1433` port of the container, like `9034:1433` and it would still run the same, only difference is that now we would connect to the database from our application on port `9034`.

#### Stopping the server

This is all that you need to do to get started with a running database and get on with your project. Of course you would ask, how to stop the server when you don't want to keep the database running. Since our server is being run inside docker, we will let docker handle the shutdown of the server, by executing the command to stop the container itself.

> `docker stop sql`
