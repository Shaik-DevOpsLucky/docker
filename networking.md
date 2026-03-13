# Docker Networking (Interview Notes)

Docker networking allows **containers to communicate with:**

* other containers
* the Docker host
* external networks (internet)

Docker provides **network isolation + connectivity** using Linux networking features.

### Technologies used internally

* **Network namespaces**
* **veth (virtual ethernet pairs)**
* **Linux bridges**
* **iptables (NAT rules)**

---

# How Docker Networking Works (Architecture Flow)

When a container starts:

1. Docker creates a **network namespace**
2. Assigns:

   * IP address
   * network interface (eth0)
3. Creates **veth pair**
4. Connects container to **docker bridge**
5. Uses **iptables for NAT & routing**

### Flow

```
Container
   │
   │ eth0
   │
veth pair
   │
docker0 bridge
   │
Host Network Interface
   │
Internet
```

---

# Check Docker Networks

```bash
docker network ls
```

Example output

```
NETWORK ID     NAME      DRIVER
xxxxx          bridge    bridge
xxxxx          host      host
xxxxx          none      null
```

These are **default networks**.

---

# 1. Bridge Network (Default)

Bridge network creates an **internal private network on a single host**.

Containers connected to the same bridge can communicate with each other.

### Architecture

```
Container A
      │
      │
Container B
      │
      │
   docker0 bridge
      │
      │
      Host
      │
   Internet
```

### Run container

```bash
docker run -d nginx
```

Automatically connected to **bridge network**.

---

### Create custom bridge network (Best Practice)

```bash
docker network create my-bridge
```

Run containers in this network

```bash
docker run -d --name app1 --network my-bridge nginx
docker run -d --name app2 --network my-bridge busybox
```

Test communication

```bash
docker exec -it app2 ping app1
```

Docker provides **automatic DNS resolution**.

---

### Inspect bridge network

```bash
docker network inspect my-bridge
```

Shows:

* subnet
* gateway
* connected containers

---

# 2. Host Network

In **host networking**, container uses **host's network directly**.

No bridge, no NAT.

### Architecture

```
Container
   │
   │
Host Network Stack
   │
Internet
```

### Run container

```bash
docker run -d --network host nginx
```

### Characteristics

✔ No port mapping required
✔ High performance
❌ No network isolation

### Use Case

* monitoring agents
* networking tools

---

# 3. None Network

Container gets **no external network access**.

Only loopback interface exists.

### Architecture

```
Container
   │
   │
Loopback (lo)
```

### Command

```bash
docker run -d --network none nginx
```

### Use Case

* security isolation
* batch jobs

---

# 4. Overlay Network (Multi-host Networking)

Overlay network connects containers across **multiple Docker hosts**.

Used in **Docker Swarm**.

### Architecture

```
Host 1                    Host 2
Container A  ---- Overlay Network ----  Container B
```

### Enable swarm

```bash
docker swarm init
```

### Create overlay network

```bash
docker network create -d overlay my-overlay
```

### Run container

```bash
docker service create --name web --network my-overlay nginx
```

---

# 5. Macvlan Network

Macvlan assigns **real MAC address and IP** to containers.

Container appears as **physical device in network**.

### Architecture

```
Container
   │
Macvlan Interface
   │
Physical Network
   │
Router / Switch
```

### Create macvlan network

```bash
docker network create -d macvlan \
--subnet=192.168.1.0/24 \
--gateway=192.168.1.1 \
-o parent=eth0 my-macvlan
```

Run container

```bash
docker run -d --network my-macvlan nginx
```

### Use Case

* legacy applications
* direct LAN access

---

# Port Publishing (Very Important)

Expose container port to host.

### Command

```bash
docker run -d -p 8080:80 nginx
```

Meaning

```
Host Port 8080 → Container Port 80
```

Access application

```
http://localhost:8080
```

Docker creates **iptables NAT rule** internally.

---

# Important Docker Network Commands

### List networks

```bash
docker network ls
```

---

### Create network

```bash
docker network create mynet
```

---

### Inspect network

```bash
docker network inspect mynet
```

---

### Connect container to network

```bash
docker network connect mynet container1
```

---

### Disconnect container

```bash
docker network disconnect mynet container1
```

---

### Remove network

```bash
docker network rm mynet
```

---

### Remove unused networks

```bash
docker network prune
```

---

# Real DevOps Example (Microservices)

Example application:

```
Frontend
Backend
Database
```

Create network

```bash
docker network create app-network
```

Run services

```bash
docker run -d --name db --network app-network mysql
docker run -d --name backend --network app-network backend-image
docker run -d --name frontend --network app-network frontend-image
```

Connection inside backend

```
db:3306
```

Because Docker provides **internal DNS resolution**.

---

# One Line Interview Answer

You can say:

> Docker networking allows containers to communicate with each other, the host, and external networks using Linux networking features like namespaces, veth pairs, bridges, and iptables. Docker provides multiple network drivers such as bridge, host, none, overlay, and macvlan depending on the use case.

---

# Prepared by:
*Shaik Moulali*
# DevOps Engineer
