#Docker Cheatsheet

## Docker Basics

### List Running Containers

Shows active containers, IDs, names, and port mappings.

```bash
docker ps
```

- **Flag:** `-a` to see stopped containers.

### Start Services (Compose)

Builds, creates, and starts all services defined in `compose.yaml`.

```bash
docker compose up -d
```

- **Flag:** `-d` Detached mode (runs in background).

### List Networks

Shows all available networks. Use to find the network name (e.g., `todo-app_default`).

```bash
docker network ls
```

### Inspect Container Config

Returns JSON dump of configuration (IPs, volumes, networks).

```bash
docker inspect <container_name_or_id>
```

### Force Remove Container

Instantly stops and deletes a container.

```bash
docker rm -f <container_name_or_id>
```

- **Flag:** `-f` Force kill.

---

## Launching Netshoot (The Debugger)

### Mode A: Network Inspector (Neighbor Mode)

_Best for testing connections **between** containers (e.g., "Can Node reach MySQL?")._

```bash
docker run -it --rm --network <network_name> nicolaka/netshoot
```

- `--network`: Connects to a specific bridge network.
- `--rm`: Auto-delete container on exit.

### Mode B: Internal Inspector (Sidecar Mode)

_Best for inspecting **one** container's internals (e.g., "Is localhost:3000 open?")._

```bash
docker run -it --rm --net container:<target_container_name> nicolaka/netshoot
```

- `--net container:...`: Shares target's network stack (IP, localhost, ports).

---

## Diagnostics (Inside Netshoot)

### Test Port Access (Netcat)

Definitive test for firewall/listening status.

```bash
nc -zv <host> <port>
```

- **Example:** `nc -zv 101-app-mysql-1 3306`
- `-z`: Scan mode (no data).
- `-v`: Verbose.

### Test DNS Resolution

Checks if internal DNS resolves the container name to an IP.

```bash
dig <host_name>
```

- **Example:** `dig mysql`
- Look for the **ANSWER SECTION**.

### List Open Ports (Socket Stats)

_Run in **Mode B (Sidecar)**._ Verifies if app is listening on `0.0.0.0` vs `127.0.0.1`.

```bash
ss -tulpn
```

- `-t` TCP, `-u` UDP, `-l` Listening, `-p` Process, `-n` Numeric.

### Network Scanner (Nmap)

Finds any open ports you might have missed.

```bash
nmap -p- <host>
```

- `-p-`: Scan ALL ports (1-65535).

### Real-time Traceroute (MTR)

Interactive packet loss and latency viewer.

```bash
mtr <host>
```

### Process Viewer (Htop)

_Run in **Mode B (Sidecar)**._ Checks CPU/RAM usage.

```bash
htop
```

### Check IP Address

Shows current IP and interfaces.

```bash
ip addr
```
