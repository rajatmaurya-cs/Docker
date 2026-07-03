# 1. Build your own image

Suppose you wrote an Express app.

You create a Dockerfile and run:

```bash
docker build -t my-app .
```

Now you have your own image:

```bash
my-app
```


# 2 Pulling Images

Suppose you want the official Node.js image.

Run:

```bash
docker pull node
```

Docker downloads the image from Docker Hub.

Internet
     │
     ▼
Docker Hub
     │
     ▼
Your Computer

After downloading, you can create containers from it.

Example
```bash
docker pull ubuntu
```
Downloads Ubuntu.

```bash
docker pull nginx
```

Downloads Nginx.

```bash
docker pull redis
```
Downloads Redis.

# Listing Images

To see all downloaded images:

docker images

Example output:

```bash
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
node          latest    a12bc345       3 days ago     1.2GB
ubuntu        latest    b98de765       2 weeks ago    78MB
redis         latest    c54fg321       5 days ago     117MB
my-app        latest    d65hj987       2 hours ago    320MB
```


# Removing Images

Suppose you no longer need Ubuntu.

Remove it:

```bash
docker rmi ubuntu
```

or by image ID:

```bash
docker rmi a12bc345
```

Docker deletes the image from your computer.