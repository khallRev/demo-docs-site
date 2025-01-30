---
title: "Docker Architecture"
layout: default
---

# Docker

Docker is our container platform, used to package applications with all dependencies into portable images.

## Why Docker?
- **Consistent Environment**: Avoids the "works on my machine" problem by bundling dependencies.  
- **Lightweight**: Containers share the host OS kernel, making them more efficient than traditional VMs.  
- **Portability**: Run containers on any host with Docker installed, or within Kubernetes.

## Docker File Structure

Below is an example of a simple Dockerfile for a web application:

```dockerfile
FROM node:16-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["npm", "start"]
