```js
FROM node:22

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 3000

CMD ["npm", "run", "dev"]   

```

```

1. It is extremely slow and uses too much power
Your Dockerfile runs npm run dev (Development Mode).

In Development Mode, the computer is constantly looking at the code to see if you made any changes so it can update live. This uses a lot of computer power (CPU and memory).
When a user visits your site, the page has to be built right then and there. This makes the website load much slower for them.
In a real launch, you want to build the pages beforehand so they load instantly.

2. The file size is way too big
It uses a heavy computer setup (node:22).

Because it copies everything (even your helper tools, testing tools, and design files), the package becomes huge (usually over 1 GB).
A huge file size means it will take a long time to upload to the internet, and a long time to start up.


3. It exposes your secret code (Security Risk)
Because you copied your entire project folder inside, anyone who gets access to your running container can see your original source code.

In a good setup, you only copy the finished product (the compiled, unreadable code), not your design plans.
4. It installs useless things
It runs npm install which downloads every tool you use for writing code (like code checkers and testing tools).

The live website doesn't need these tools to run, so they just waste space.
9:42 AM


```


```js

# ---------- Stage 1 : Install Dependencies ----------
FROM node:22 AS deps

WORKDIR /app

COPY package*.json ./

RUN npm ci



// /app
// ├── package.json
// ├── package-lock.json
// └── node_modules/



# ---------- Stage 2 : Build ----------
FROM node:22 AS builder

WORKDIR /app

COPY --from=deps /app/node_modules ./node_modules

COPY . .

RUN npm run build



// /app
// ├── app/
// ├── public/
// ├── .next/
// ├── node_modules/
// ├── package.json
// └── package-lock.json


# ---------- Stage 3 : Production ----------

FROM node:22 AS runner

WORKDIR /app

ENV NODE_ENV=production

COPY --from=builder /app/package*.json ./

COPY --from=builder /app/.next ./.next

COPY --from=builder /app/public ./public

COPY --from=builder /app/node_modules ./node_modules

EXPOSE 3000

CMD ["npm","start"]


```