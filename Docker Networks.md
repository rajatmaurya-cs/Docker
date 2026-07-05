To allow containers on the same network to communicate with each other.

```js
Step 1: Create a Docker network

First, create a network.

docker network create my-network

Now Docker has a network called:

my-network

You can check it:

docker network ls

Output:

NETWORK ID      NAME
xxxxxx          bridge
yyyyyy          host
zzzzzz          my-network

Step 2: Build the frontend image
docker build -t my-frontend ./frontend

Now Docker has an image:

Image
┌───────────────┐
│ my-frontend   │
└───────────────┘
Step 3: Build the backend image
docker build -t my-backend ./backend

Now Docker has:

Images

my-frontend
my-backend
Step 4: Run the backend container
docker run -d \
  --name backend \
  --network my-network \
  my-backend

Docker does:

my-network

┌──────────────────────────┐
│                          │
│ backend                  │
│ IP: 172.18.0.2           │
│ Port: 3000               │
│                          │
└──────────────────────────┘

Notice something important:

The container has the name:

backend

Docker stores something like:

backend
↓

172.18.0.2

This is Docker's internal DNS.

Step 5: Run the frontend container
docker run -d \
  --name frontend \
  --network my-network \
  my-frontend

Now the network looks like

my-network

┌─────────────────────────────┐

frontend
IP: 172.18.0.3

backend
IP: 172.18.0.2

└─────────────────────────────┘

Both containers are on the same network.

```


```js

The IP address uniquely identifies a container on the Docker network. Docker uses the IP address to deliver network packets to the correct container.

Step 1: You create a network

docker network create my-network

Now Docker creates a virtual network.

my-network

At this point, the network is empty.

my-network

(No containers)

Step 2: Start the backend
docker run -d \
  --name backend \
  --network my-network \
  my-backend

When the backend joins the network, Docker gives it an IP address.

For example:

backend

IP = 172.18.0.2

Now the network looks like

my-network

backend
IP = 172.18.0.2
Step 3: Start the frontend
docker run -d \
  --name frontend \
  --network my-network \
  my-frontend

Docker gives it another IP.

frontend

IP = 172.18.0.3

Now

my-network

backend
IP = 172.18.0.2

frontend
IP = 172.18.0.3

```


```


Step 7: Frontend wants data

Suppose your backend has

app.get("/api", (req, res) => {
    res.json({ message: "Hello" });
});

Now inside the frontend code you write

fetch("http://backend:3000/api");

Notice you wrote

backend

Not

172.18.0.2

Why?

Because IP addresses can change whenever the container is recreated, but the container name stays the same.

Step 8: What happens internally?

The frontend executes

fetch("http://backend:3000/api");

It extracts:

Host = backend

Port = 3000

Path = /api

Now it asks:

What is the IP address of backend?

Docker DNS replies:

backend

↓

172.18.0.2

Now the request becomes

172.18.0.2:3000/api
Step 9: Request travels
Frontend Container

fetch()

        │

        ▼

Docker Network

        │

        ▼

Backend Container

Express Server

GET /api
Step 10: Backend sends response
Backend

↓

{
  "message":"Hello"
}

↓

Docker Network

↓

Frontend

Then

const res = await fetch("http://backend:3000/api");
const data = await res.json();

console.log(data.message);

prints

Hello


```