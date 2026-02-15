# System Design

### Table of Contents
1. [What is System Design?](#1-what-is-system-design)
2. [What is a Load Balancer?](#2-what-is-a-load-balancer)
3. [Horizontal vs Vertical Scaling](#3-horizontal-vs-vertical-scaling)
4. [Cache](#4-cache)
5. [API Gateway](#5-api-gateway)
6. [Flow Diagram](#6-flow-diagram)
7. [Web Servers](#7-web-servers)
8. [Reverse Proxy](#8-reverse-proxy)
9. [Forward Proxy](#9-forward-proxy)
10. [Forward Proxy vs VPN](#10-forward-proxy-vs-vpn)
11. [Kafka](#11-kafka)

<br>

## 1. What is System Design?

<img width="756" height="383" alt="image" src="https://github.com/user-attachments/assets/194108f7-cab8-4ee3-9ab5-968c113fadbc" />


*System Design means:*

- Planning how a system (app / website / backend) will work at large scale.
- Instead of writing code, we design:
- How users interact
- How servers handle requests
- How data is stored
- How system handles millions of users
- How system avoids crashing

### 1️⃣ Functional Requirements

*What should the system do?*

- Who are the main users/actions? (read-heavy or write-heavy?)
- Specific features needed? (post, like, comment, follow, search, notifications, stories?)
- Any real-time needs? (chat, live comments)

### 2️⃣ Non Functional Requirements

*How should the system behave?*

- Should support 10M users
- Should respond in < 200ms
- Should never lose data
- Should scale automatically

<br>

## 2. What is a Load Balancer?

Distributes incoming traffic across multiple servers so that no single server gets overloaded.

<img width="690" height="354" alt="image" src="https://github.com/user-attachments/assets/3f76f492-edb0-4190-8134-6f9dd4a2e612" />


💡 ***Real Life Example***
  *Imagine:*

- Bank has only 1 counter.
- 100 people come.
- Line becomes huge.
- Now bank opens 5 counters.
- Security guard sends each person to different counter.
- That security guard = Load Balancer.
- Counters = Servers.

***Basic small system:***
```jsx
User → Server → Database
```
***When traffic increases:***
```jsx
                → Server 1
User → Load Balancer → Server 2
                → Server 3
                       ↓
                    Database

```

👉 Load Balancer sits between Users and Servers

🚨 ***Why Do We Need It?***

*Imagine:*
- Your server can handle 1000 users
- Suddenly 10,000 users come

`Without Load Balancer:`
```jsx
All 10,000 → 1 server → CRASH 💥
```
`With Load Balancer:`
```jsx
10,000 users
   → 3 servers
   → each handles ~3,333 users
```
Now system survives.

🎯 ***When Should You Use It?***

*Use Load Balancer when:*

 1️⃣ You have multiple servers
- If you only have 1 server, no need.

 2️⃣ Traffic is high
- Thousands or millions of users.

3️⃣ High availability is required
- If one server dies, traffic goes to others.

4️⃣ You want scalability
- Add new server → Load Balancer automatically starts using it.

<br>

⚙️ ***How Does It Decide Where to Send Traffic?***

*Common strategies:*

1️⃣ Round Robin
- Server1 → Server2 → Server3 → repeat

2️⃣ Least Connections
- Send request to server with least active users.

3️⃣ IP Hash
- Same user always goes to same server.

<br>

🛡 ***Extra Benefit: Fault Tolerance***

If:
- Server2 crashes ❌
- Load Balancer automatically stops sending traffic to it.
- Users don’t even know server crashed.
- That is called ***High Availability.***

<br>

🌍 ***Real World Load Balancers***

`Examples:`
- Nginx
- AWS ELB
- HAProxy

`In cloud:`
- You don’t install manually.
- Cloud provides it.

<br>

## 3. Horizontal vs Vertical Scaling

<img width="698" height="332" alt="image" src="https://github.com/user-attachments/assets/483d2087-aa50-42c1-86d9-f4515184188b" />


1️⃣ ***Vertical Scaling***

👉 Meaning
- Increase power of one single server.
- You make one machine stronger.

Example
```jsx
1 server
8GB RAM
4 CPU

```
Traffic increases. So you upgrade to:
```jsx
1 server
32GB RAM
16 CPU
```
Same machine.
More power.
That is Vertical Scaling.

🏢 ***Real Life Example***
- You own a small shop.
- More customers come.
- Instead of opening new shops, you make the shop bigger.
- Same shop. Bigger size.

✅ ***Advantages***
- Easy to implement
- No code changes
- Simple architecture

❌ ***Problems***
- Hardware limit exists
- Very expensive
- If server crashes → whole system down

<br>

2️⃣ ***Horizontal Scaling***

👉 Meaning
- Increase number of servers.
- Instead of making one strong,
- add more machines.

`Example`
```jsx
1 server
```
`You do:`
```jsx
Server 1
Server 2
Server 3
```
`And add a Load Balancer in front.`

```jsx
User
  ↓
Load Balancer
  ↓
Server1
Server2
Server3
```
Traffic divided.

<br>

🏢 ***Real Life Example***
- Restaurant is full.
- Instead of making kitchen bigger, you open 3 more branches.
- Customers go to different branches.

<br>

✅ ***Advantages***
- Almost unlimited scaling
- Safer
- If one server dies, others work
- Better for high traffic apps

<br>

❌ Problems
- More complex
- Need Load Balancer
- Need stateless servers

| Situation                     | Use            |
| ----------------------------- | -------------- |
| Small app                     | Vertical       |
| Startup growing               | Vertical first |
| Large app (Instagram, Amazon) | Horizontal     |
| Need high availability        | Horizontal     |

<br>

## 🚀 How To Do It Practically?
***Vertical Scaling in Cloud***

`In AWS:`
- Stop EC2
- Increase instance size
- Start again
- Done.

***Horizontal Scaling in Cloud***
- Create multiple EC2 instances
- Put them in Auto Scaling Group
- Add Load Balancer
- Enable health checks
- Now traffic auto distributes.


## 4. Cache

Cache is a temporary, high-speed storage layer that stores frequently accessed data so your system doesn’t have to fetch it again from a slower source like a database.

👉 Goal: Make system faster + reduce load on database

<img width="345" height="108" alt="image" src="https://github.com/user-attachments/assets/d7675e1c-e9b8-41b2-8d9d-63e038613c0f" />


🔥 ***Simple Real-Life Example***

- Imagine you run a tea shop.
- Kitchen (Database) → Takes time to prepare tea.
- Counter shelf (Cache) → Ready-made tea kept for quick serving.
- If 10 customers order the same tea:
- Without cache → Kitchen makes 10 times.
- With cache → Make once, serve 9 times quickly.

That’s caching.

```jsx
User → Load Balancer → App Server → Cache → Database
```

***Flow:***
- User requests data.
- App checks cache first.
- If found → return instantly.
- If not → fetch from DB, store in cache, then return.

⚡ ***Why We Use Cache***
- Reduce database load
- Improve response time
- Handle high traffic
- Reduce server cost


### 🗂 ***Types of Caching***

1️⃣ Client-Side Cache

- Stored in browser
- Example: images, CSS

2️⃣ CDN Cache

- Stored in edge servers globally
- Example: static files
- Popular CDN:
- Cloudflare
- Akamai

## 5. API Gateway

An API Gateway is a single entry point for all client requests in a microservices architecture.

- Instead of client calling 10 different services directly
- Client → calls → API Gateway → Gateway routes to correct service.

<img width="691" height="400" alt="image" src="https://github.com/user-attachments/assets/dbc91c1f-68cd-4885-9bc3-2c17217b3848" />


🧠 Simple Real Life Example

***Think of a hotel reception.***
- Guests do not go directly to housekeeping, kitchen, or manager.
- They talk to reception.
- Reception forwards the request to correct department.
- 👉 API Gateway = Reception
- 👉 Microservices = Different departments


### 🏗 Where It Fits in Architecture
```jsx
Client (Web / Mobile)
        ↓
  Load Balancer
        ↓
   API Gateways
        ↓
---------------------------------
| Auth Service                  |
| User Service                  |
| Payment Service               |
| Order Service                 |
---------------------------------
```

<br>

```jsx
Client
   ↓
Load Balancer
   ↓
API Gateway (multiple instances)
   ↓
Microservices
```

### 🎯 Why We Use API Gateway

`Without API Gateway:`
- Client needs to know all service URLs
- Too many API calls
- Security becomes complex

`With API Gateway:`
- One URL
- Centralized security
- Cleaner architecture

### ⚡ Responsibilities of API Gateway

1️⃣ Routing
```jsx
/users → User Service
/orders → Order Service
```

2️⃣ Authentication & Authorization

Checks token before forwarding request.

Example:
- JWT validation
- OAuth

3️⃣ Rate Limiting

- Prevents abuse.

Example:
- 100 requests per minute per user.

4️⃣ Load Balancing

- Distributes traffic across service instances.

5️⃣ Logging & Monitoring

- Tracks requests and errors.


## 6. Flow Diagram

```jsx
Client
   ↓
Load Balancer
   ↓
Web Server / Reverse Proxy
   ↓
API Gateway
   ↓
Microservices (App Servers)
   ↓
Database
```

| Layer         | Meaning in One Line          |
| ------------- | ---------------------------- |
| Client        | Sends request                |
| Load Balancer | Chooses server               |
| Web Server    | Accepts and forwards request |
| API Gateway   | Secures and routes APIs      |
| Microservices | Executes business logic      |
| Database      | Stores data permanently      |


## 7. Web Servers

<img width="752" height="283" alt="image" src="https://github.com/user-attachments/assets/0f0f68e9-b550-4a4b-b1df-a3a08b831228" />

**A Web Server is software that:**

- Accepts HTTP or HTTPS requests
- Serves static content
- Forwards requests to backend applications
- Manages connections efficiently
- It is usually the first backend layer after a load balancer.

### 🔵 What Exactly Is a Web Server?

A web server is software running on a machine.

- NGINX
- Apache HTTP Server
- Internet Information Services

They run on Linux or Windows servers.

🔥 ***Responsibilities of Web Server***

1️⃣ Serve Static Content

`Fast delivery of:`
- HTML
- CSS
- JavaScript
- Images
Web servers are optimized for this.

2️⃣ Reverse Proxy

```jsx
Client → Web Server → Backend App
```

3️⃣ SSL Termination

- Handles HTTPS encryption.
- Client uses HTTPS
- Inside system may use HTTP
- Backend does not manage certificates

4️⃣ Load Distribution (Basic)

Some web servers can distribute traffic across app instances.

Example:
- NGINX


5️⃣ Security Layer

Can:
- Block bad IPs
- Limit request size
- Prevent DDoS at basic level

🎯 When Do You Need a Web Server?

`You need it when:`
- Hosting frontend on your server
- Want SSL termination
- Need reverse proxy
- Want performance optimization
- You may not need separate one if:
- Using CDN for frontend
- Using managed API Gateway that handles everything

## 8. Reverse Proxy

<img width="662" height="389" alt="image" src="https://github.com/user-attachments/assets/dd1a7e70-c88b-4d71-8f0a-a7d4a8dc7bd1" />


A Reverse Proxy is a server that sits between clients and backend servers and forwards client requests to the appropriate backend server.

- Client never talks directly to backend.
- It always talks to the reverse proxy first.

```jsx
Client
   ↓
Reverse Proxy
   ↓
Backend Server(s)
   ↓
Database
```


🧠 ***Simple Meaning***

- Reverse Proxy = Middleman that:
- Receives request
- Forwards to correct backend
- Returns response back to client

***Client does NOT know:***

- How many backend servers exist
- Where they are located
- What their IP addresses are

🔥 ***Why Do We Need Reverse Proxy?***

1️⃣ Security
- Backend servers are hidden.

`Instead of:`
```jsx
Client → App Server
```

`We do:`
```jsx
Client → Reverse Proxy → App Server
```


### 🧠 Reverse Proxy vs Load Balancer

- Load balancer distributes traffic
- Reverse proxy forwards and can also balance
- Many reverse proxies also act as load balancers


### Reverse Proxy vs API Gateway table

| Feature                | Reverse Proxy                       | API Gateway                              |
| ---------------------- | ----------------------------------- | ---------------------------------------- |
| Primary Purpose        | Forward and protect backend servers | Manage and control APIs in microservices |
| Works At               | Network / HTTP layer                | Application / API layer                  |
| Routing                | Basic URL-based routing             | Advanced service-based routing           |
| Authentication         | Basic (IP allow/deny)               | JWT, OAuth, token validation             |
| Rate Limiting          | Basic                               | Advanced per user / per API              |
| Response Aggregation   | ❌ No                                | ✅ Yes                                    |
| API Versioning         | ❌ No                                | ✅ Yes                                    |
| Logging & Monitoring   | Basic                               | Advanced analytics                       |
| Microservices Friendly | Limited                             | Designed for microservices               |
| Example Tools          | NGINX, HAProxy                      | Amazon API Gateway, Kong                 |


### 🧠 Simple Meaning

`Reverse Proxy`
→ Protects backend and forwards traffic.

`API Gateway`
→ Controls APIs and applies business-level policies.


### 🏗 How They Fit Together

```jsx
Client
   ↓
Reverse Proxy
   ↓
API Gateway
   ↓
Microservices
```

***In many systems:***

- Reverse proxy handles SSL + basic routing
- API Gateway handles auth + API rules
- Reverse Proxy = Traffic manager
- API Gateway = API manager


## 9. Forward Proxy

A Forward Proxy is a server that sits between a client and the internet and sends requests on behalf of the client.

👉 It protects or controls the client side.

`Instead of:`
```jsx
Client → Website
```

`It becomes:`
```jsx
Client → Forward Proxy → Website
```

🔥 ***Why Use Forward Proxy?***
1️⃣ Privacy / Anonymity

Website cannot see client’s real IP.

***Common example:***
- Tor

2️⃣ Corporate Control

In companies:
- Employees cannot access:
- Social media
- Specific websites
- Certain external services
- All traffic goes through a forward proxy.

3️⃣ Caching

If 100 employees download same file:
- Proxy caches it
- Next users get it faster.

4️⃣ Security Monitoring

`Company can:`
- Monitor traffic
- Block malicious websites
- Scan downloads

🔥 ***Forward Proxy vs Reverse Proxy***

| Feature  | Forward Proxy   | Reverse Proxy   |
| -------- | --------------- | --------------- |
| Protects | Clients         | Servers         |
| Position | Client side     | Server side     |
| Hides    | Client IP       | Server IP       |
| Used By  | Companies, ISPs | Backend systems |


🧠 ***Real-Life Example***

- Forward Proxy = Company security gate before employees go outside.
- Reverse Proxy = Security gate before visitors enter company.


## 10. Forward Proxy vs VPN

| Feature          | Forward Proxy                       | VPN                               |
| ---------------- | ----------------------------------- | --------------------------------- |
| What It Protects | Client identity for specific apps   | Entire device internet traffic    |
| Works At         | Application level                   | Network level                     |
| Traffic Coverage | Only configured apps (browser etc.) | All apps and system traffic       |
| Encryption       | Not always encrypted                | Fully encrypted tunnel            |
| IP Hiding        | Hides IP from destination server    | Hides IP + encrypts data from ISP |
| Setup Level      | App or browser configuration        | OS level or device level          |
| Use Case         | Corporate filtering, caching        | Privacy, security on public WiFi  |
| Performance      | Faster, lightweight                 | Slightly slower due to encryption |
| Control          | Granular control per request        | Full device routing               |
