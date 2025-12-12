# Docker

## Overview
Docker is a platform for containerizing applications, making them portable and easy to deploy. This module covers everything from Docker fundamentals to advanced container management, image building, and registry operations.

## Learning Objectives
By the end of this module, you will be able to:
- Understand Docker architecture and containerization concepts
- Create and manage Docker containers effectively
- Build optimized Docker images using Dockerfiles
- Use Docker CLI commands proficiently
- Work with Docker registries including Docker Hub and private registries
- Apply Docker best practices for security and performance

## Topics Covered

### 1. [Docker Introduction](./topics/01-docker-introduction.md)
- What is Docker?
- Docker Architecture
- Containerization concepts
- Containers vs Virtual Machines
- Docker use cases and ecosystem

### 2. [Docker Images](./topics/02-docker-images.md)
- Understanding Docker images
- Image vs Container concepts
- Image layers and caching
- Working with images (pull, build, tag, push)
- Image optimization techniques

### 3. [Dockerfile](./topics/03-dockerfile.md)
- Dockerfile syntax and structure
- Common Dockerfile instructions (FROM, RUN, COPY, CMD, etc.)
- Multi-stage builds
- Dockerfile best practices
- Complete application examples

### 4. [Docker Containers](./topics/04-docker-containers.md)
- Creating and running containers
- Container lifecycle management
- Managing container resources
- Container networking and storage
- Container troubleshooting

### 5. [Docker CLI](./topics/05-docker-cli.md)
- Essential Docker commands
- Image management commands (pull, build, push, rmi)
- Container management commands (run, ps, start, stop, rm)
- Network and volume commands
- System management and cleanup

### 6. [Docker Registry](./topics/06-docker-registry.md)
- Understanding Docker registries
- Working with Docker Hub
- Pushing and pulling images
- Setting up private registries
- Cloud-based registries (ECR, GCR, ACR, GHCR)

## Key Commands Reference

```bash
# Image Commands
docker pull IMAGE[:TAG]           # Download image
docker build -t NAME:TAG .        # Build image from Dockerfile
docker images                     # List images
docker rmi IMAGE                  # Remove image

# Container Commands
docker run [OPTIONS] IMAGE        # Create and start container
docker ps                         # List running containers
docker ps -a                      # List all containers
docker start CONTAINER            # Start stopped container
docker stop CONTAINER             # Stop running container
docker rm CONTAINER               # Remove container
docker logs CONTAINER             # View container logs
docker exec -it CONTAINER bash    # Execute command in container

# Registry Commands
docker login                      # Login to registry
docker push IMAGE:TAG             # Push image to registry
docker search TERM                # Search Docker Hub

# System Commands
docker system df                  # Show disk usage
docker system prune               # Remove unused data
docker info                       # Display system information
```

## Exercises

### Exercise 1: First Docker Container
1. Install Docker on your system
2. Run your first container: `docker run hello-world`
3. Pull and run an nginx container
4. Access the nginx welcome page in your browser
5. Stop and remove the container

### Exercise 2: Building Custom Images
1. Create a simple Node.js or Python application
2. Write a Dockerfile for your application
3. Build the Docker image
4. Run a container from your custom image
5. Verify the application is working

### Exercise 3: Multi-Container Application
1. Create a web application with a database
2. Write Dockerfiles for both components
3. Create a custom network
4. Run both containers on the same network
5. Verify they can communicate

### Exercise 4: Docker Hub Workflow
1. Create a Docker Hub account
2. Build and tag an image
3. Push the image to Docker Hub
4. Remove local image
5. Pull the image from Docker Hub

### Exercise 5: Volume Management
1. Create a container with persistent storage
2. Write data to the volume
3. Remove the container
4. Create a new container with the same volume
5. Verify data persists

**Detailed exercises with solutions can be found in the [exercises](./exercises/) directory.**

## Additional Resources

### Official Documentation
- [Docker Documentation](https://docs.docker.com/)
- [Docker Hub](https://hub.docker.com/)
- [Dockerfile Reference](https://docs.docker.com/engine/reference/builder/)
- [Docker CLI Reference](https://docs.docker.com/engine/reference/commandline/cli/)

### Learning Resources
- [Docker Get Started Tutorial](https://docs.docker.com/get-started/)
- [Play with Docker](https://labs.play-with-docker.com/) - Interactive playground
- [Docker Samples](https://github.com/docker/awesome-compose) - Example applications

### Best Practices
- [Docker Image Best Practices](https://docs.docker.com/develop/dev-best-practices/)
- [Dockerfile Best Practices](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)
- [Docker Security Best Practices](https://docs.docker.com/engine/security/)

### Tools
- [Docker Desktop](https://www.docker.com/products/docker-desktop) - GUI for Mac/Windows
- [Docker Compose](https://docs.docker.com/compose/) - Multi-container orchestration
- [Portainer](https://www.portainer.io/) - Container management UI

### Cheat Sheets
- See [resources/cheat-sheets/docker.md](../resources/cheat-sheets/) for quick reference

## Assessment

You should be comfortable with:
- [ ] Understanding Docker architecture and containerization
- [ ] Creating and managing Docker containers
- [ ] Writing efficient Dockerfiles
- [ ] Building and optimizing Docker images
- [ ] Using Docker CLI commands effectively
- [ ] Working with Docker volumes and networks
- [ ] Pushing and pulling images from registries
- [ ] Implementing Docker best practices
- [ ] Troubleshooting common Docker issues

## Next Steps

Once you've completed this module and feel confident with Docker, proceed to:
- **Docker Compose**: Multi-container application orchestration
- **Kubernetes**: Container orchestration at scale
- **CI/CD with Docker**: Automated build and deployment pipelines
- **Container Security**: Advanced security practices

Related modules in this curriculum:
- [Module 24: Kubernetes](../24-kubernetes/) (if available)
- [Module 25: DevOps & CI/CD](../25-devops/) (if available)

---

**Time Estimate:** 4 days
**Difficulty:** Intermediate
**Prerequisites:** Linux fundamentals, basic command-line knowledge
