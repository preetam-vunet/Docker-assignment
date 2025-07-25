# Docker Assignment

## 1. What is the relation between an image and a container in Docker?

In Docker, **images** and **containers** are fundamental concepts that work together to enable containerization.

- **Docker Image**: A read-only template containing the application, dependencies, and configuration. Think of it as a blueprint.
- **Docker Container**: A running instance of an image. It is mutable and isolated, with its own filesystem and processes.

**Relationship**: An image is the static blueprint, and a container is the running environment created from that blueprint. Multiple containers can be created from a single image.

---

## 2. List all the images and the containers in the system.

```bash
# List all Docker images
docker images

# List all containers (including stopped ones)
docker ps -a
```

---

## 3. Pull the latest image of `nginx` and run it:

a. By naming the container `super-nginx`  
b. By exposing the port 80 on the host  
c. In detached mode  
d. With the environment variable `NGINX_HOST` set to `vunet.local`

```bash
docker pull nginx:latest
docker run --name super-nginx -p 80:80 -d -e NGINX_HOST=vunet.local nginx:latest
```

---

## 4. Get the list of all running containers and stop and remove the `nginx` container

```bash
# List all running containers
docker ps

# Stop the nginx container
docker stop super-nginx

# Remove the nginx container
docker rm super-nginx
```

---

## 5. Create a docker volume named `vunet` and run `nginx` again, attaching the volume to `/etc/` in the container

```bash
docker volume create vunet
docker run --name super-nginx -p 80:80 -d -e NGINX_HOST=vunet.local -v vunet:/etc/ nginx:latest
```

---

## 6. Stop and remove the `nginx` container and remove the volume `vunet`

```bash
docker stop super-nginx
docker rm super-nginx
docker volume rm vunet
```

---

## 7. Create a `Dockerfile` that:

a. Uses the latest `ubuntu` image  
b. Installs `python3.10`  
c. Copies the below Python script into the image  
d. Runs the Python file as the entrypoint

**Dockerfile:**
```Dockerfile
FROM ubuntu:latest

RUN apt-get update && \
    apt-get install -y python3.10

COPY server.py /server.py

ENTRYPOINT ["python3.10", "/server.py"]
```

**server.py:**
```python
import http.server
import socketserver
from http import HTTPStatus

class Handler(http.server.SimpleHTTPRequestHandler):
    def do_GET(self):
        self.send_response(HTTPStatus.OK)
        self.end_headers()
        self.wfile.write(b'Hello world')

httpd = socketserver.TCPServer(('', 8000), Handler)
print("Serving Http Requests on 8000...")
httpd.serve_forever()
```

---

## 8. Run a container using the image created

```bash
docker build -t python-http-server .
docker run --name my-python-server -p 8000:8000 -d python-http-server
```

---

## 9. Check if the container is running and has the python process running

```bash
docker ps
docker exec my-python-server ps aux | grep python
```

---

## 10. Why are docker networks used? Create a docker network named `vunet`

**Why use Docker networks?**
- To allow containers to communicate with each other securely and efficiently.
- To isolate groups of containers.
- To control how containers connect to external resources.

**Create a network:**
```bash
docker network create vunet
```

---

## 11. Create an `nginx` cluster by attaching the created network, `vunet`, to it

```bash
docker run --name super-nginx --network vunet -p 7001:80 -d -e NGINX_HOST=vunet.local nginx:latest
```

---
