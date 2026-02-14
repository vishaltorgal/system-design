# System Design

 ✅ ***What is System Design?***

*System Design means:*

- Planning how a system (app / website / backend) will work at large scale.
- Instead of writing code, we design:
- How users interact
- How servers handle requests
- How data is stored
- How system handles millions of users
- How system avoids crashing

## 1️⃣ Functional Requirements

*What should the system do?*

- Who are the main users/actions? (read-heavy or write-heavy?)
- Specific features needed? (post, like, comment, follow, search, notifications, stories?)
- Any real-time needs? (chat, live comments)

## 2️⃣ Non Functional Requirements

*How should the system behave?*

- Should support 10M users
- Should respond in < 200ms
- Should never lose data
- Should scale automatically


## 🧠 What is a Load Balancer?

Distributes incoming traffic across multiple servers so that no single server gets overloaded.

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

⚙️ ***How Does It Decide Where to Send Traffic?***

*Common strategies:*

1️⃣ Round Robin
- Server1 → Server2 → Server3 → repeat

2️⃣ Least Connections
- Send request to server with least active users.

3️⃣ IP Hash
- Same user always goes to same server.

<br><br>

🛡 ***Extra Benefit: Fault Tolerance***

If:
- Server2 crashes ❌
- Load Balancer automatically stops sending traffic to it.
- Users don’t even know server crashed.
- That is called ***High Availability.***

<br><br>

🌍 ***Real World Load Balancers***

`Examples:`
- Nginx
- AWS ELB
- HAProxy

`In cloud:`
- You don’t install manually.
- Cloud provides it.
