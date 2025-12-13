# Docker Compose Cheatsheet

This guide covers transitioning from manual `docker run` commands to a structured `compose.yaml` file for a Node.js development environment with a MySQL backend.

## 1. The Manual Way (Hard Mode)

Without Compose, you must manage networks, volumes, and environment variables manually for every single container.

### Step 1: Create the Network

```bash
docker network create todo-app
```

### Step 2: Start MySQL Container

```bash
docker run -d \
    --network todo-app --network-alias mysql \
    -v todo-mysql-data:/var/lib/mysql \
    -e MYSQL_ROOT_PASSWORD=secret \
    -e MYSQL_DATABASE=todos \
    mysql:8.0
```

- **`-d`**: **Detached mode**. Runs the container in the background so it doesn't lock up your terminal.
- **`--network`**: Connects the container to the `todo-app` network we created.
- **`--network-alias`**: Gives this container a simpler hostname (e.g., "mysql") so other containers can reach it.
- **`-v`**: **Volume**. Maps the named volume `todo-mysql-data` to the container's storage folder (`/var/lib/mysql`) so data persists after restart.
- **`-e`**: **Environment Variable**. Sets configuration values inside the container (like passwords).

### Step 3: Start Node App Container

```bash
docker run -dp 3000:3000 \
    -w /app -v "$(pwd):/app" \
    --network todo-app \
    -e MYSQL_HOST=mysql \
    -e MYSQL_USER=root \
    -e MYSQL_PASSWORD=secret \
    -e MYSQL_DB=todos \
    node:18-alpine \
    sh -c "yarn install && yarn run dev"
```

- **`-dp`**: Combines detached mode (`-d`) and port mapping (`-p`).
- **`3000:3000`**: Maps port 3000 on your Mac to port 3000 inside the container.
- **`-w /app`**: **Working Directory**. Sets the default folder where commands run inside the container.
- **`-v "$(pwd):/app"`**: **Bind Mount**. Maps your current folder (source code) into the container. This allows "Hot Reloading" (changes on Mac update the container instantly).
- **`sh -c "..."`**: Overrides the default command to install dependencies and start the dev server.

---

## 2. The Compose Way (Easy Mode)

We can replace all the commands above with a single file named `compose.yaml` in your project root.

### Create `compose.yaml`

```yaml
services:
  app:
    image: node:18-alpine
    command: sh -c "yarn install && yarn run dev"
    ports:
      - 3000:3000
    working_dir: /app
    volumes:
      - ./:/app
    environment:
      MYSQL_HOST: mysql
      MYSQL_USER: root
      MYSQL_PASSWORD: secret
      MYSQL_DB: todos
    depends_on:
      - mysql

  mysql:
    image: mysql:8.0
    volumes:
      - todo-mysql-data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: secret
      MYSQL_DATABASE: todos

volumes:
  todo-mysql-data:
```

**Key Changes:**

- **`depends_on`**: Tells Docker to start the `mysql` service before starting the `app` service.
- **Automatic Networking**: Compose automatically creates a shared network. The service name (`mysql`) becomes the hostname.
- **`volumes:`**: The named volume is defined at the bottom so Docker knows to manage it.

---

## 3. Workflow & Commands

### Cleanup Old Containers

Before starting Compose, ensure no conflicting "manual" containers are running.

```bash
# List all running containers
docker ps

# Force remove specific containers by ID
docker rm -f <container_id>
```

### Cleanup Old Networks

After removing containers, remove any leftover manual networks to prevent conflicts.

```bash
# List networks to find the name
docker network ls

# Remove a specific network
docker network rm todo-app

# OR: Remove all unused networks at once (Safe & Fast)
docker network prune -f
```

### Start the Stack

Spins up the app and DB in the background.

```bash
docker compose up -d
```

- **`-d`**: Detached mode (background). Without this, logs take over your terminal window.

### View Logs

Stream logs from all services to see if everything started correctly.

```bash
docker compose logs -f
```

- **`-f`**: **Follow**. Keeps the connection open and streams new logs in real-time (like `tail -f`).
- **Specific Service:** `docker compose logs -f app` (Useful to see just Node errors).

### Stop the Stack

Stops and removes the containers and networks, but **keeps** the database volume.

```bash
docker compose down
```

### Stop & Destroy Data

Stops containers and **deletes** the named volumes.

```bash
docker compose down --volumes
```

- **`--volumes`**: **Destructive**. Wipes the persistent `todo-mysql-data` volume. Use this if you want to reset the database completely.

### ⚠️ The MySQL "Gotcha"

MySQL initialization scripts (database creation, password setting) run **only once** on the very first startup when the volume is empty.

- **Problem:** If you change `MYSQL_ROOT_PASSWORD` in your `compose.yaml` _after_ the volume is created, MySQL will ignore the change because it sees existing data.
- **Fix:** You must destroy the volume to force a re-initialization.
  ```bash
  docker compose down --volumes
  docker compose up -d
  ```

### 🔧 How to Change Password (WITHOUT Data Loss)

If you need to change the root password _after_ the database has been initialized, do **not** destroy the volume. Use SQL instead.

1. **Change the password inside the running database:**

   ```bash
   # 1. Enter the database container
   docker exec -it <container_name> mysql -u root -p

   # 2. Type your OLD password when prompted.

   # 3. Run this SQL command (MySQL 8.0+):
   ALTER USER 'root'@'%' IDENTIFIED BY 'newpassword';
   FLUSH PRIVILEGES;
   EXIT;
   ```

2. **Update your `compose.yaml`:**
   - Update `MYSQL_ROOT_PASSWORD` (for documentation/consistency).
   - **Crucial:** Update `MYSQL_PASSWORD` in the `app` service section so your Node app knows the new password.

3. **Restart the App:**
   ```bash
   # Re-creates the app container with the new environment variable
   docker compose up -d
   ```

## "Best Practice" for managing secrets

### 🔒 Using an `.env` File (Variable Substitution)

Instead of hardcoding passwords in `compose.yaml` and accidentally commiting them to Git, use variables.

1. **Create a file named `.env`:**

   ```bash
   MYSQL_PASS=mysecretpassword
   DB_NAME=todos
   ```

2. **Update `compose.yaml` to use variables:**
   Use the `${VARIABLE_NAME}` syntax.

   ```yaml
   services:
     app:
       environment:
         MYSQL_PASSWORD: ${MYSQL_PASS}
         MYSQL_DB: ${DB_NAME}
         # ...

     mysql:
       environment:
         MYSQL_ROOT_PASSWORD: ${MYSQL_PASS}
         MYSQL_DATABASE: ${DB_NAME}
   ```

### 📂 Using `env_file` (Bulk Injection)

Injects every variable from a file directly into the container.

1. **Create a config file (e.g. `mysql.env`):**

   ```bash
   MYSQL_ROOT_PASSWORD=secret
   MYSQL_DATABASE=todos
   MYSQL_USER=root
   ```

2. **Update `compose.yaml`:**
   Remove the `environment` section and replace it with `env_file`.
   ```yaml
   services:
     mysql:
       image: mysql:8.0
       env_file:
         - ./mysql.env
       volumes:
         - todo-mysql-data:/var/lib/mysql
   ```

### 🧠 How `.env` Works (The Hand-off)

The `.env` file is **not** read by your container. It is read by Docker Compose on your host machine to fill in the blanks in your `compose.yaml`.

**The Flow:**

1. **`.env`** defines the secret: `PASSWORD=secret123`
2. **`compose.yaml`** grabs it: `MYSQL_ROOT_PASSWORD: ${PASSWORD}`
3. **Container** receives it: The MySQL process starts with `MYSQL_ROOT_PASSWORD=secret123`

**Hierarchy of Priority:**
If the same variable exists in multiple places, Docker follows this order (highest priority first):

1. **Shell Environment:** `export PASSWORD=override` (in your terminal)
2. **`.env` file:** `PASSWORD=default` (in your folder)
3. **Default value:** `${PASSWORD:-fallback}` (in yaml)

### 🛡️ Security Best Practices

**Rule #1: Never Commit Secrets**
Any file containing passwords or keys must be added to your `.gitignore` file immediately.

1. **Add config files to `.gitignore`:**

   ```bash
   .env
   *.env
   mysql.env
   ```

2. **Commit a Template Instead:**
   Create a file named `.env.example` with dummy values so teammates know what variables are needed.

   ```bash
   # .env.example
   MYSQL_PASSWORD=placeholder_password
   ```

   A new developer runs

   ```bash
   cp mysql.env.example mysql.env
   ```

   to get started.

3. **Check before you push:**
   Run `git status` to ensure your secret files are not being tracked.
