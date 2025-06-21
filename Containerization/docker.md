The standard for containerization.

## 0. Installation
### 0.1. On Linux

1. **Recommended Way**
```shell
# Install dependencies
sudo apt-get install -y ca-certificates curl gnupg

# Add Docker’s official GPG key
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# Add Docker repo
echo \
  "deb [arch=$(dpkg --print-architecture) \
  signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Update and install
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Verify installation
sudo docker --version
sudo docker run hello-world

# Start and Enable on startup
sudo systemctl start docker
sudo systemctl enable docker
```

2. or Run this **Convenience Script**

```shell
curl -fsSL https://get.docker.com | sh
```

## 1. Commands

### 1.1. Information

| Command                                              | What it does                           |
| ---------------------------------------------------- | -------------------------------------- |
| `docker --version`                                   | Shows client version (and build hash). |
| `docker version`                                     | Shows detailed client and server info. |
| `docker info`                                        | Shows detailed system info.            |
| `docker help`<br />`docker --help`                     | Main help.                             |
| `docker help <command>`<br />`docker <command> --help` | Command help.                          |

### 1.2. Images

| Command                               | What it does                 |
| ------------------------------------- | ---------------------------- |
| `docker pull <image>`                 | Download image.              |
| `docker rmi <image>`                  | Remove image.                |
| `docker images`                       | List local images.           |
| `docker build -t <name>:<tag> <path>` | Build image from Dockerfile. |
| `docker save -o <file> <image>`       | Save image as file.          |
| `docker load -i <file>`               | Load image from file.        |
| `docker tag <image> <new-name>`       | Tag image.                   |
| `docker push <image>`                 | Push image to registry.      |
## 2. Dockerfile
A `Dockerfile` is a plain text script of instructions (essentially a recipe) that tells Docker how to build a custom image step-by-step. *Only the FROM command is mandatory.*

```dockerfile

ARG <argument> = <value>
FROM <image>
LABEL <field> = <string>
ENV <variable> = <value>
COPY <source-path> <destination-path>
```



| Instruction   | Purpose                                                              |
| ------------- | -------------------------------------------------------------------- |
| `FROM`        | Base image to build from. Always the first instruction (except ARG). |
| `LABEL`       | Adds metadata (author, version, description). Good for docs.         |
| `ARG`         | Defines build-time variables.                                        |
| `ENV`         | Sets environment variables inside the image.                         |
| `COPY`        | Copies files/folders from your local machine to the image.           |
| `ADD`         | Like `COPY` but can also uncompress files & fetch URLs.              |
| `RUN`         | Executes a command **at build time** (e.g., `apt-get install`).      |
| `WORKDIR`     | Sets the **working directory** for following instructions.           |
| `EXPOSE`      | Documents which port(s) the container listens on.                    |
| `VOLUME`      | Creates a mount point for persistent storage.                        |
| `USER`        | Sets which user to run as inside the image.                          |
| `CMD`         | Specifies the **default command** to run when the container starts.  |
| `ENTRYPOINT`  | Like `CMD` but more rigid: sets the executable that always runs.     |
| `HEALTHCHECK` | Defines how to test if the container is healthy.                     |
| `ONBUILD`     | Adds a trigger instruction for child images.                         |
| `SHELL`       | Changes the shell used for `RUN` commands (`sh` vs `bash`).          |


### 6️⃣ **ADD**

```Dockerfile
ADD https://example.com/file.tar.gz /tmp/
```

- Like `COPY` but can:
    
    - download URLs
        
    - extract `.tar.gz` automatically.
        

---

### 7️⃣ **RUN**

```Dockerfile
RUN apt-get update && apt-get install -y curl
```

- Runs commands **while building** the image.
    
- Creates new layers.
    

---

### 8️⃣ **WORKDIR**

```Dockerfile
WORKDIR /app
```

- Sets the working directory.
    
- Automatically creates it if it doesn’t exist.
    

---

### 9️⃣ **EXPOSE**

```Dockerfile
EXPOSE 80
```

- Documents a port — doesn’t actually **publish** it. You must use `-p` or `-P` when running.
    

---

### 🔟 **USER**

```Dockerfile
USER appuser
```

- Runs subsequent commands as this user.
    

---

### 1️⃣1️⃣ **CMD**

```Dockerfile
CMD ["python", "app.py"]
```

- Default command **if none is provided by `docker run`**.
    
- Only one `CMD` allowed — if multiple, only the last one applies.
    

---

### 1️⃣2️⃣ **ENTRYPOINT**

```Dockerfile
ENTRYPOINT ["python", "app.py"]
```

- Like `CMD` but always runs, even if you pass arguments in `docker run`.
    
- Good for making an image behave like an executable.
    

---

### 1️⃣3️⃣ **HEALTHCHECK**

```Dockerfile
HEALTHCHECK CMD curl --fail http://localhost:80/ || exit 1
```

- Optional.
    
- Tells Docker how to test if the container is healthy.
    

---

### 1️⃣4️⃣ **ONBUILD**

```Dockerfile
ONBUILD COPY . /app
```

- Triggered **when a child image is built from this one**.
    
- Handy for base images for frameworks.
    

---

## ✅ **General syntax tips**

- Instructions are **case-insensitive**, but uppercase is standard (`FROM`, `COPY`).
    
- Order **matters**: Docker caches layers step by step.
    
    - Changing an earlier step invalidates cache for later ones.
        
- Keep your `Dockerfile` tidy: put frequently changing parts (like `COPY . .`) **near the end** for better caching.
    

---

## 🎓 **Which lines are optional?**

|Required|Instruction|Note|
|---|---|---|
|✅|`FROM`|Always required (except for `scratch` base).|
|⚪|Everything else|Optional — depends on your app’s needs.|
|⚪|`CMD` or `ENTRYPOINT`|Highly recommended to define default behavior.|

---

## 📌 **Example: Clean, typical `Dockerfile`**

```Dockerfile
# Base image
FROM node:20-alpine

# Metadata
LABEL maintainer="jaish@example.com"

# Build-time variable
ARG PORT=3000

# Environment variable
ENV NODE_ENV=production

# Working directory
WORKDIR /app

# Copy files
COPY package*.json ./
RUN npm install
COPY . .

# Expose port
EXPOSE $PORT

# Default command
CMD ["npm", "start"]
```

---

## ✅ **In short:**

- **Only `FROM` is strictly required.**
    
- `CMD` or `ENTRYPOINT` defines what runs — recommended.
    
- Other instructions help set up, configure, and optimize your image.
    

---

If you want, I can share:

- **Best practices** (e.g., multi-stage builds)
    
- **Real-world examples** (e.g., Python, Node.js, Go)
    
- A **cheat sheet** version!
    

Just say _“Show me more!”_ 🚀🐳



1. `docker pull <image>` --> Download image.
2. `docker images` --> List images.
3. `docker ps` --> Running containers.
	1. `docker ps -a` (All containers).
4. `docker start <container>` --> Start
5. `docker stop <container>` --> Stop.
6. `docker rm <container>` --> Remove.
7. `docker rmi <image>` --> Remove image.
8. `docker exec -it <container> bash` --> Run command in running container.
9. `docker logs <container>` --> View logs.
10. `docker run -it ubuntu bash` --> Run a container interactively

Build Your Own Image

1. Create a Dockerfile:

FROM ubuntu:latest
RUN apt-get update && apt-get install -y python3
COPY . /app
WORKDIR /app
CMD ["python3", "app.py"]


2. Build the image:

docker build -t myapp .


3. Run it:

docker run -p 5000:5000 myapp

### Docker Compose

Define multi-container apps using YAML.

Example docker-compose.yml:

```yaml
version: '3'
services:
  web:
    image: nginx
    ports:
      - "8080:80"
  db:
    image: mysql
    environment:
      MYSQL_ROOT_PASSWORD: example
```

Commands:

docker compose up         # Start all
docker compose up -d      # Start in background
docker compose down       # Stop and remove
docker compose logs       # View logs



