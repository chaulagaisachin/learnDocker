
# Learn Docker :whale:

Docker is the most amazing and life-changing technology ever invented (said no one who has ever spent hours trying to debug a Dockerfile).

In reality, Docker is a tool that allows you to package applications and their dependencies into lightweight container images, which can then be run on any machine with a Docker runtime. It makes it easy to build, deploy, and run applications in a consistent and isolated environment, regardless of the underlying infrastructure.

But don't let that simplicity fool you - Docker can be a real pain to work with at times, especially when you're trying to get a multi-container application up and running. Between trying to figure out the right combination of volumes, networks, and dependencies, you'll probably want to pull your hair out more than once.

Despite its challenges, Docker is an extremely popular and widely-used tool that has revolutionized the way we deploy and run applications. So, if you're up for a bit of a challenge and want to spend hours debugging your Dockerfiles, go ahead and give it a try!

## What You Will Learn

By the end of this course, you should be able to:

- Install and configure Docker with modern workflows.
- Build, tag, and publish container images.
- Use volumes and storage patterns correctly.
- Design container networking for local and multi-node setups.
- Create clean Dockerfiles and compose multi-container apps.
- Understand Swarm orchestration concepts and operations.
- Apply container security and forensic practices.
- Measure, benchmark, and tune container performance.

## Course Modules

| Module | Focus | Outcome |
|---|---|---|
| [Module 01](https://github.com/chaulags/learnDocker/tree/main/Module01#module-01-docker-install--configure) | Docker Install and Configure | Working Docker setup and architecture fundamentals |
| [Module 02](https://github.com/chaulags/learnDocker/tree/main/Module02#module-02-image-creation--management--registry) | Images, Registry, Lifecycle | Build, run, tag, and push images confidently |
| [Module 03](https://github.com/chaulags/learnDocker/tree/main/Module03#module-03-docker-storage-and-volumes) | Storage and Volumes | Manage persistent data with correct mount strategy |
| [Module 04](https://github.com/chaulags/learnDocker/tree/main/Module04#module-04-docker-networking) | Docker Networking | Connect services using practical networking patterns |
| [Module 05](https://github.com/chaulags/learnDocker/tree/main/Module05#module-05-docker-image-creation--compose) | Dockerfile and Compose | Build efficient images and run multi-service stacks |
| [Module 06](https://github.com/chaulags/learnDocker/tree/main/Module06#module-06-container-orchestration-using-docker-swarm) | Orchestration with Swarm | Understand cluster model and service orchestration |
| [Module 06 Continued](https://github.com/chaulags/learnDocker/tree/main/Module06%20Continued#docker-swarm-continued) | Swarm Deployment Operations | Scale, update, and manage swarm workloads |
| [Module 07](https://github.com/chaulags/learnDocker/tree/main/Module07#module-07-docker-security--forensics) | Security and Forensics | Apply secure runtime and investigation practices |
| [Module 08](https://github.com/chaulags/learnDocker/tree/main/Module08#module-08-container-performance-monitoring-and-benchmarking) | Performance and Benchmarking | Monitor, benchmark, and optimize containers with evidence |

## How to Use This Repository

1. Start from Module 01 and continue in order.
2. Run commands yourself, do not just read outputs.
3. Keep notes for each module: command used, expected behavior, actual result.
4. Re-run selected labs with small variations to build intuition.
5. Use Module 08 to validate performance changes with baseline and comparison tables.

## Recommended Lab Setup

- Linux host or VM with recent Docker Engine.
- At least 8 GB RAM recommended for monitoring and benchmarking exercises.
- Reliable network access for pulling images and referencing docs.

## Documentation

- [Docker Official Documentation](https://docs.docker.com/)
- [Docker CLI Reference](https://docs.docker.com/reference/cli/docker/)
- [Compose File Reference](https://docs.docker.com/reference/compose-file/)

## Contributing

Suggestions and improvements are welcome.

Open an issue or submit a pull request if you want to:

- Fix outdated commands.
- Improve examples or explanations.
- Add labs, visuals, or troubleshooting notes.

## Author

- [@chaulagaisachin](https://www.github.com/chaulagaisachin)

