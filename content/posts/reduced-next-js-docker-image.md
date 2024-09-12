+++
title = 'Reduced Next Js Docker Image'
date = 2024-09-10T12:00:00+09:00
draft = false
+++

Recently, I noticed that the Docker image size for my Next.js project was quite large. So I decided to do some research and found ways to reduce the image size.

# Baseline

Here's what my initial Dockerfile looked like:

```Dockerfile
FROM node:18
WORKDIR /app
COPY . .
RUN yarn install
RUN yarn build
EXPOSE 3000
CMD ["yarn", "start"]
```

It ended up being around 3GB.

# Step 1: Use a Smaller Base Image

Before making any changes, my Docker image was using the standard Node.js base image. To reduce the image size, I switched to the Alpine Linux version. Here's the updated Dockerfile with a smaller base image:

```Dockerfile
FROM node:18-alpine
WORKDIR /app
COPY . .
RUN yarn install
RUN yarn build
EXPOSE 3000
CMD ["yarn", "start"]
```

This switch reduced the image size by 1GB, a significant improvement.

# Step 2: Use Multi-Stage Build

The next optimization was using a multi-stage build. This allowed me to separate the build process and include only the necessary files in the final image.

```Dockerfile
FROM node:18-alpine AS builder
WORKDIR /app
COPY ./package.json ./
RUN yarn install
COPY . .
RUN yarn build

FROM node:18-alpine AS runner
WORKDIR /app
COPY --from=builder /app/.next ./.next
COPY --from=builder /app/public ./public
COPY --from=builder /app/package.json ./
COPY --from=builder /app/node_modules ./node_modules
EXPOSE 3000
CMD ["node_modules/.bin/next", "start"]
```

Using multi-stage builds reduced the image size from 2GB to 520MB.

# Step 3: Next.js Output Configuration

To optimize the size even further, I updated the `next.config.js` file to generate a standalone build.

```ts
// next.config.js
/** @type {import('next').NextConfig} */
module.exports = {
  output: "standalone",
};
```

To use standalone build, I updated my Dockerfile:

```Dockerfile
FROM node:18-alpine AS builder
WORKDIR /app
COPY ./package.json ./
RUN yarn install
COPY . .
RUN yarn build

FROM node:18-alpine AS runner
WORKDIR /app
COPY --from=builder /app/.next/standalone ./
COPY --from=builder /app/.next/static ./.next/static
EXPOSE 3000
ENV PORT=3000
ENV HOSTNAME="0.0.0.0"
CMD ["node", "server.js"]
```

This configuration, helped keep the Dockerfile image size around 146MB.

# Conclusion

Through these optimizations, I was able to reduce the Docker image size from 3GB to 146MB. This not only makes the image faster to build and deploy but also saves storage.

| **Step**                           | **Image Size** |
| ---------------------------------- | -------------- |
| Initial image                      | 3GB            |
| After using a smaller base image   | 2.1GB          |
| After using multi-stage build      | 520MB          |
| After Next.js output configuration | 146MB          |

# References

1. [Official Docker Documentation](https://docs.docker.com/develop/develop-images/multistage-build/)
2. [Next.js Output Configuration](https://nextjs.org/docs/advanced-features/output-file-tracing)
3. [Optimizing Docker Image Sizes](https://www.docker.com/blog/intro-guide-to-dockerfile-best-practices/)
