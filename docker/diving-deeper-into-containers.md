### Running Containers in Different Modes

Two modes:
1. **Detached(d)**
2. **Interactive(i)**

First, let's run a container in detached mode:

```bash
docker run -d --name nginx-detached nginx
```

Now, let's run a container in interactive mode:

```bash
docker run -it --name ubuntu-interactive ubuntu /bin/bash
```

### Managing Container Lifecycle
Start, Stop, Restart and Remove containers

Let's start by stopping the nginx-detached container:

```bash
docker stop nginx-detached
docker ps -a
```

Let's start it again:

```bash
docker start nginx-detached
```

We can also restart a running container:

```bash
docker restart nginx-detached
```

Let's remove the stopped ubuntu-interactive container:

```bash
docker rm ubuntu-interactive
docker ps -a
```

### Inspecting Container Details

```bash
docker inspect nginx-detached
```

To get the IP address of the container:

```bash
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' nginx-detached
```

To see the current state of the container:

```bash
docker inspect --format '{{.State.Status}}' ubuntu-interactive
```

These commands use Go templates to filter the output. The **-f** flag allows us to specify a format template.

Let's also check the port mappings:

```bash
docker port nginx-detached
```

Port mapping allows containers to communicate with the host system or external networks. **It maps a port on the host system to a port inside the container**. This is crucial for accessing services running inside containers from outside the Docker environment.

To demonstrate port mapping, let's create a new Nginx container with a port mapping:

```bash
docker run -d --name nginx-with-port -p 8080:80 nginx
docker port nginx-with-port
```

### Working with Container Logs

Let's view the logs of our nginx container:

```bash
docker logs nginx-detached
```

This shows all logs from the container's start. To see only the most recent logs, you can use the **--tail** option:

```bash
docker logs --tail 10 nginx-detached
```

To follow the logs in real-time (like **tail -f**), use the **-f** option:

```bash
docker logs -f nginx-detached
```

You can also get timestamps for your log entries:

```bash
docker logs --timestamps nginx-detached
```

This can be particularly useful for debugging time-sensitive issues.

### Executing Commands in Running Containers

Let's start by executing a simple command in our nginx container:

```bash
docker exec nginx-detached echo "Hello from inside the container"
```

Now, let's get an interactive shell inside the container:

```bash
docker exec -it nginx-detached /bin/bash
```

### Copying Files To and From Containers

Docker provides a way to copy files between your host system and containers. This is useful for tasks like updating configuration files or retrieving logs.

First, let's create a simple HTML file on our host system:

```bash
echo "<html><body><h1>Hello from host</h1></body></html>" > hello.html
```

Now, let's copy this file into our nginx container:

```bash
docker cp hello.html nginx-detached:/usr/share/nginx/html/hello.html
```

We can verify the file was copied by executing a command in the container:

```bash
docker exec nginx-detached cat /usr/share/nginx/html/hello.html
```

Now, let's copy a file from the container to our host:

```bash
docker cp nginx-detached:/etc/nginx/nginx.conf ~/project/nginx.conf
```

### Setting Environment Variables in Containers

Environment variables are a key way to configure applications running in containers. Let's explore how to set and use them.

First, let's run a new container with an environment variable:

```bash
docker run --name env-test -e MY_VAR="Hello, Environment" -d ubuntu sleep infinity
```

Now, let's verify that our environment variable was set:

```bash
docker exec env-test env | grep MY_VAR
```

We can also set environment variables using a file. Create a file named **env_file** with the following content

```bash
ANOTHER_VAR=From a file
YET_ANOTHER_VAR=Also from the file
```

Now, let's start another container using this file:

```bash
docker run --name env-file-test --env-file env_file -d ubuntu sleep infinity
```

Verify the environment variables in this new container:

```bash
docker exec env-file-test env | grep -E "ANOTHER_VAR|YET_ANOTHER_VAR"
```

### Limiting Container Resources

Docker allows you to limit the resources (CPU and memory) that a container can use. This is crucial for managing resource allocation in multi-container environments.

Let's start a container with memory and CPU limits:

```bash
docker run --name limited-nginx -d --memory=512m --cpus=0.5 nginx
```

This command does the following:

- **--name limited-nginx:** Names the container "limited-nginx"
- **-d:** Runs the container in detached mode
- **--memory=512m:** Limits the container to 512 megabytes of memory
- **--cpus=0.5:** Limits the container to use at most half of a CPU core
- **nginx:** Uses the Nginx image

We can verify these limits using the **inspect** command:

```bash
docker inspect -f '{{.HostConfig.Memory}}' limited-nginx
docker inspect -f '{{.HostConfig.NanoCpus}}' limited-nginx
```

To see how these limits affect the container in real-time, we can use the **stats** command:

```bash
docker stats limited-nginx
```

#### List only container names

```bash
docker ps --format '{{.Names}}'
```