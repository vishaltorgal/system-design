# System Design

### Table of Contents
1. [What is System Design?](#1-what-is-system-design)
2. [What is a Load Balancer?](#2-what-is-a-load-balancer)
3. [Horizontal vs Vertical Scaling](#3-horizontal-vs-vertical-scaling)


## 1. ***What is System Design?***

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


## 2. ***What is a Load Balancer?***

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

<br>

## 3. Horizontal vs Vertical Scaling

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

## 🔁 2️⃣ Horizontal Scaling

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


🏢 ***Real Life Example**
- Restaurant is full.
- Instead of making kitchen bigger, you open 3 more branches.
- Customers go to different branches.


✅ ***Advantages***
- Almost unlimited scaling
- Safer
- If one server dies, others work
- Better for high traffic apps

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
