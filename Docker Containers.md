## Running Containers

Suppose you already have an image called:

my-app

Run it:

```bash
docker run my-app
```

Docker will:

Create a new container.
Start the container.
Run the command specified in the image (e.g., npm start).

## Give the Container a Name

Instead of Docker generating a random name:

```bash
docker run --name backend my-app
```

Now your container is named backend.

## Run in Background

Normally, your terminal gets attached to the application.

To run it in the background:

```bash
docker run -d my-app
```

-d = detached mode

Now the container keeps running, and you get your terminal back.

Without -d

When you run:

```bash
docker run my-app
```

Docker starts the container and attaches your terminal to it.

Suppose your application is:

```js
// app.js
setInterval(() => {
     console.log("Server is running...");
}, 1000);
```

When you run:

```bash
docker run my-app
```

Your terminal will look like:

Server is running...
Server is running...
Server is running...
Server is running...

Notice something?

You cannot type new commands in this terminal.
Your terminal is "busy" showing the container's output.
To stop it, press:
Ctrl + C

That stops the container too.

What does "attached" mean?

Imagine your terminal is connected to the container with a cable.

Terminal  <-------->  Docker Container
                  logs

Everything the application prints appears directly in your terminal.

With -d (Detached Mode)

Now run:

```bash
docker run -d my-app
```

Instead of attaching your terminal, Docker says:

"I'll run the container in the background."

Output:

8c0d5d0b9fa42e....

This long string is the Container ID.

Immediately your prompt comes back:

$

Now you can type:

ls

or

git status

or

docker ps

Your application is still running even though you don't see its output.

## Run on a Port

Step 1: Imagine your Express app

Suppose you wrote this server:

```js
const express = require("express");
const app = express();

app.get("/", (req, res) => {
     res.send("Hello World");
});

app.listen(3000, () => {
     console.log("Server started on port 3000");
});
```

Here,

app.listen(3000)

means:

Inside the container, the application is listening on port 3000.

Notice I said inside the container.

Step 2: What is a container?

Think of a Docker container as a small separate computer.

Your Computer
│
├── Chrome
├── VS Code
├── Docker
│
└── Container
        ├── Node.js
        ├── Express App
        └── Port 3000

The Express app is running inside that mini-computer.

Step 3: The problem

Suppose you run:

```bash
docker run my-app
```

Docker starts the container.

Inside it:

Container

Express App
Listening on 3000

But your laptop doesn't know how to reach that port.

If you open

http://localhost:3000

your browser tries to connect to your computer's port 3000, not the container's port 3000.

So you'll usually get an error like:

This site can't be reached

Step 4: We need a bridge

Docker lets us create a bridge between your computer and the container.

That's what -p does.

Your Computer                  Docker Container

Port 3000   ─────────────►   Port 3000

The command is:

```bash
docker run -p 3000:3000 my-app
```

Step 5: Meaning of 3000:3000

The syntax is:

Host Port : Container Port

So:

```bash
docker run -p 3000:3000 my-app
```

means:

Your Computer                Container

3000  ---------------------> 3000

Now when your browser goes to

http://localhost:3000

it reaches:

Browser
    │
    ▼
Computer Port 3000
    │
    ▼
Docker forwards request
    │
    ▼
Container Port 3000
    │
    ▼
Express App

Then the Express app sends back:

Hello World

Step 6: Why are there two port numbers?

Because the ports don't have to be the same.

Example:

```bash
docker run -p 8080:3000 my-app
```

means:

Computer                Container

8080  ----------------> 3000

The app still listens on 3000 inside the container.

But on your computer, you access it using 8080.

So you visit:

http://localhost:8080

not

http://localhost:3000

## Multiple Options Together

```bash
docker run -d --name backend -p 3000:3000 my-app
```

This command:

Runs in background
Names the container backend
Maps port 3000
Starts the app

## Viewing Running Containers

Show only running containers:

```bash
docker ps
```

Example:

CONTAINER ID   IMAGE    STATUS
ab123cd        my-app   Up 2 minutes

Show all containers (including stopped ones):

```bash
docker ps -a
```

Example:

CONTAINER ID   IMAGE    STATUS
ab123cd        my-app   Up 2 minutes
xy987mn        my-app   Exited

## Stopping Containers

Suppose your container is named:

backend

Stop it:

```bash
docker stop backend
```

Or use the container ID:

```bash
docker stop ab123cd
```

Status changes to:

Exited

The container still exists—it just isn't running.

## Restarting Containers

Start a stopped container:

```bash
docker start backend
```

Restart a running container:

```bash
docker restart backend
```

## Removing Containers

Delete a stopped container:

```bash
docker rm backend
```

Now it's completely gone.

## Remove a Running Container

```bash
docker rm -f backend
```

## Viewing Logs

Logs are the output your application prints to the terminal.

Example in Node.js:

```js
console.log("Server Started");
console.log("Database Connected");
```

When running in Docker:

```bash
docker logs backend
```

Output:

Server Started
Database Connected
Listening on port 3000
Listening on port 3000

```


```
Watch Logs Live

docker logs -f backend


What does -f mean?

Now suppose your application keeps printing messages every few seconds.

Example:

setInterval(() => {
    console.log("Server is running...");
}, 2000);

Every 2 seconds:

Server is running...
Server is running...
Server is running...

If you run:

docker logs backend

You might only see:

Server Started
Server is running...
Server is running...

and then the command exits.

But your application is still printing more logs.

Now use -f
docker logs -f backend

-f means follow.

It tells Docker:

"Keep showing me every new log as soon as it's printed."

Your terminal now looks like:

Server Started
Server is running...
Server is running...
Server is running...
Server is running...
Server is running...

Every new message appears automatically.