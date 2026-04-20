# Module 07: Docker Security & Forensics
* [Docker Security Overview](https://github.com/chaulags/learnDocker/tree/main/Module07#docker-security-overview)
* [Namespaces and cgroups](https://github.com/chaulags/learnDocker/tree/main/Module07#namespaces-and-cgroups)
* [Running Containers as Non-Root](https://github.com/chaulags/learnDocker/tree/main/Module07#running-containers-as-non-root)
* [Read-Only Containers](https://github.com/chaulags/learnDocker/tree/main/Module07#read-only-containers)
* [Docker Content Trust](https://github.com/chaulags/learnDocker/tree/main/Module07#docker-content-trust)
* [Docker Secrets](https://github.com/chaulags/learnDocker/tree/main/Module07#docker-secrets)
* [AppArmor and Seccomp Profiles](https://github.com/chaulags/learnDocker/tree/main/Module07#apparmor-and-seccomp-profiles)
* [Image Vulnerability Scanning](https://github.com/chaulags/learnDocker/tree/main/Module07#image-vulnerability-scanning)
* [Docker Bench for Security](https://github.com/chaulags/learnDocker/tree/main/Module07#docker-bench-for-security)
* [Docker Forensics](https://github.com/chaulags/learnDocker/tree/main/Module07#docker-forensics)
* [Official Documentation Updates (2026)](#official-documentation-updates-2026)
* [Rootless Docker Update](#rootless-docker-update)
* [Docker Scout Expanded Update](#docker-scout-expanded-update)
* [Image Signing with cosign and Notation Update](#image-signing-with-cosign-and-notation-update)


## Docker Security Overview
* Security is a critical concern when running containerized workloads in production.
* Docker provides multiple layers of defense to protect containers and the host system.
* Key areas of concern:
  - Container isolation from the host and from each other.
  - Image integrity and trust.
  - Secrets and sensitive data management.
  - Least-privilege principles for running processes.
* Docker security is built on top of Linux kernel features such as **Namespaces**, **cgroups**, **Capabilities**, **Seccomp**, and **AppArmor/SELinux**.


## Namespaces and cgroups
* Docker uses Linux kernel primitives to provide isolation.

#### Namespaces
* Namespaces restrict what a container can **see**.
* Each container gets its own isolated view of:

| Namespace | Isolates |
|---|---|
| `pid` | Process IDs: containers cannot see host or other containers' processes |
| `net` | Network interfaces, IP addresses, routing tables |
| `mnt` | Filesystem mount points |
| `uts` | Hostname and domain name |
| `ipc` | Inter-process communication (shared memory, semaphores) |
| `user` | User and group IDs |

#### cgroups (Control Groups)
* cgroups restrict what a container can **use**.
* They limit and monitor resource consumption: CPU, memory, disk I/O, network bandwidth.
```
# Limit container to 512MB of RAM and 1 CPU
sudo docker run -it --memory="512m" --cpus="1.0" ubuntu:latest
```
```
# View cgroup details of a running container
sudo docker inspect <containerID> | grep -i cgroup
```

## Running Containers as Non-Root
* By default, processes inside a container run as **root (UID 0)**, which is a security risk.
* Best practice: always run containers as a non-root user.

#### Using the `--user` flag
```
sudo docker run -it --user 1000:1000 ubuntu:latest
```

#### Defining user in Dockerfile

> **Note (2026):** `ubuntu:latest` resolves to whatever is current at build time. For reproducible builds, pin a specific tag such as `ubuntu:24.04`.

```dockerfile
FROM ubuntu:24.04
RUN useradd -ms /bin/bash appuser
USER appuser
CMD ["bash"]
```

#### Checking the running user
```
sudo docker exec <containerID> whoami
```
* Dropping unnecessary Linux capabilities reduces the attack surface further.
```
sudo docker run --cap-drop ALL --cap-add NET_BIND_SERVICE nginx:latest
```


## Read-Only Containers
* Mounting the container's root filesystem as read-only prevents tampering.
* Useful for stateless services where no writes to the filesystem are expected.
```
sudo docker run -it --read-only ubuntu:latest bash
```
* To allow writes only to specific paths, combine with a `tmpfs` mount:
```
sudo docker run -it --read-only --tmpfs /tmp ubuntu:latest bash
```
* This ensures the base OS filesystem cannot be modified, limiting the impact of a compromised container.


## Docker Content Trust
* Docker Content Trust (DCT) ensures that images pulled and pushed are **cryptographically signed**.
* Prevents pulling tampered or unsigned images.

#### Enabling Docker Content Trust
```
export DOCKER_CONTENT_TRUST=1
```
* With DCT enabled, any `docker pull` or `docker run` on an unsigned image will fail.

#### Signing an Image
```
sudo docker trust sign <repository>/<image>:<tag>
```

#### Viewing signers for an image
```
sudo docker trust inspect --pretty <repository>/<image>:<tag>
```

#### Revoking trust
```
sudo docker trust revoke <repository>/<image>:<tag>
```


## Docker Secrets
* Docker Secrets are used to securely manage sensitive data such as passwords, API keys, and TLS certificates.
* Secrets are only available to **Swarm services** (not standalone containers by default).
* Secrets are stored encrypted in the Swarm Raft log.

#### Creating a Secret
```
echo "mypassword" | sudo docker secret create db_password -
```
```
sudo docker secret create db_password ./password.txt
```

#### Listing Secrets
```
sudo docker secret ls
```

#### Using a Secret in a Service
```
sudo docker service create \
  --name myapp \
  --secret db_password \
  myimage:latest
```
* The secret is mounted inside the container at `/run/secrets/<secret_name>`.

#### Removing a Secret
```
sudo docker secret rm db_password
```


## AppArmor and Seccomp Profiles

#### AppArmor
* AppArmor is a Linux Security Module (LSM) that restricts the capabilities of programs.
* Docker applies a default AppArmor profile (`docker-default`) to all containers.
* Custom profiles can be applied using:
```
sudo docker run --security-opt apparmor=<profile_name> ubuntu:latest
```
* To disable AppArmor for a container (not recommended in production):
```
sudo docker run --security-opt apparmor=unconfined ubuntu:latest
```

#### Seccomp (Secure Computing Mode)
* Seccomp filters which **system calls** a container process can make.
* Docker ships with a default Seccomp profile that blocks ~44 dangerous syscalls.
* To apply a custom Seccomp profile:
```
sudo docker run --security-opt seccomp=/path/to/seccomp-profile.json ubuntu:latest
```
* To disable Seccomp filtering (not recommended):
```
sudo docker run --security-opt seccomp=unconfined ubuntu:latest
```
* Disabling Seccomp significantly increases the attack surface.


## Image Vulnerability Scanning
* Container images can carry known vulnerabilities in their OS packages and dependencies.
* Scanning images before deployment is a critical security step.

#### Docker Scout (built-in)
```
sudo docker scout cves <image>:<tag>
```
```
sudo docker scout quickview <image>:<tag>
```

#### Trivy (popular open-source scanner)

> **Note (2026):** Trivy is not in the default Ubuntu/Debian apt repos. Use Trivy's official apt repository instead.

```bash
# Add Trivy's official apt repo and install
sudo apt-get install -y wget apt-transport-https gnupg
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
echo "deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main" \
  | sudo tee /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install -y trivy
```

```bash
# Scan a local image
trivy image <image>:<tag>

# Scan a remote image
trivy image nginx:latest
```

#### Best practices for image security
* Use **minimal base images** (e.g., `alpine`, `distroless`) to reduce attack surface.
* Regularly rebuild images to incorporate security patches.
* Never run `latest` tag in production. Pin to specific digests.
```
sudo docker pull nginx@sha256:<digest>
```


## Docker Bench for Security
* Docker Bench for Security is an open-source script that checks for dozens of common best practices in Docker deployments.
* Based on the **CIS Docker Benchmark**.

#### Running Docker Bench
```
sudo docker run --rm -it \
  --net host --pid host --userns host --cap-add audit_control \
  -e DOCKER_CONTENT_TRUST=$DOCKER_CONTENT_TRUST \
  -v /etc:/etc:ro \
  -v /usr/bin/containerd:/usr/bin/containerd:ro \
  -v /usr/bin/runc:/usr/bin/runc:ro \
  -v /usr/lib/systemd:/usr/lib/systemd:ro \
  -v /var/lib:/var/lib:ro \
  -v /var/run/docker.sock:/var/run/docker.sock:ro \
  --label docker_bench_security \
  docker/docker-bench-security
```
* The output categorises findings as:
  - **[PASS]**: Compliant.
  - **[WARN]**: Needs attention.
  - **[INFO]**: Informational.
  - **[NOTE]**: Manual review required.


## Docker Forensics
* Docker forensics involves investigating containers and images after a security incident.
* The goal is to reconstruct what happened and collect evidence without destroying artifacts.

#### Inspecting a Container
```
sudo docker inspect <containerID>
```
* Reveals mounts, environment variables, network config, restart policies, and more.

#### Viewing Container Logs
```
sudo docker logs <containerID>
sudo docker logs --timestamps <containerID>
```

#### Examining Filesystem Changes
```
sudo docker diff <containerID>
```
* Shows files Added (`A`), Changed (`C`), or Deleted (`D`) since the container started.

#### Exporting a Container's Filesystem
```
sudo docker export <containerID> -o container-snapshot.tar
```
* Useful for offline forensic analysis of the container filesystem.

#### Saving an Image for Analysis
```
sudo docker save <image>:<tag> -o image-snapshot.tar
```

#### Copying Files from a Container
```
sudo docker cp <containerID>:/path/to/file ./local-destination/
```

#### Checking Running Processes inside a Container
```
sudo docker top <containerID>
```

#### Network Forensics
```
# Inspect network connections of a container
sudo docker exec <containerID> ss -tulnp
sudo docker exec <containerID> netstat -tulnp
```

#### History of an Image (layer-by-layer commands)
```
sudo docker history --no-trunc <image>:<tag>
```
* Reveals every command run during image build; useful for detecting malicious layers.

\
**This is the End of Module 7**
[ * * Go to Top * * ](https://github.com/chaulags/learnDocker/tree/main/Module07#module-07-docker-security--forensics)

---

## Official Documentation Updates (2026)

The sections below reflect current Docker security guidance as of 2026. All references point to official Docker documentation at [docs.docker.com](https://docs.docker.com).

---

## Rootless Docker Update

> **Source:** [https://docs.docker.com/engine/security/rootless/](https://docs.docker.com/engine/security/rootless/)

**What it is**

Rootless mode runs the Docker daemon itself as an unprivileged user, not as root. This is separate from running a container process as a non-root user with `--user`. With rootless mode, even the daemon has no root access on the host, so a daemon compromise does not automatically give an attacker host root.

**Rootless mode vs. `--user` in containers**

| | `--user` flag | Rootless Docker |
|---|---|---|
| What changes | The UID inside the container | The UID the Docker daemon runs as |
| Daemon still runs as root | Yes | No |
| Requires host root to set up | No | No (after initial setup) |
| Isolation level | Container process | Daemon + all containers |

**Setting up rootless mode**

Prerequisites: `uidmap` package must be installed.

```bash
sudo apt-get install -y uidmap
```

Run the install script (as the target non-root user, not with sudo):

```bash
dockerd-rootless-setuptool.sh install
```

Start the rootless daemon for the current user session:

```bash
systemctl --user start docker
```

Set the socket path so `docker` commands use the rootless daemon:

```bash
export DOCKER_HOST=unix://$XDG_RUNTIME_DIR/docker.sock
```

**Key limitations**
* Some network features (e.g., binding to ports below 1024) require additional configuration.
* Overlay network driver support depends on kernel version and distribution.
* `docker stats` memory reporting may be less accurate without cgroups v2.

Full setup instructions and distribution-specific notes: [docs.docker.com/engine/security/rootless/](https://docs.docker.com/engine/security/rootless/)

---

## Docker Scout Expanded Update

> **Source:** [https://docs.docker.com/reference/cli/docker/scout/](https://docs.docker.com/reference/cli/docker/scout/)

Docker Scout (introduced in Docker Desktop 4.17, available in CLI since 2023) covers more than CVE scanning. The existing section in this module shows `scout cves` and `scout quickview`. The commands below are part of the current Scout CLI.

**Generate a Software Bill of Materials (SBOM)**

An SBOM lists every package and dependency in an image.

```bash
sudo docker scout sbom <image>:<tag>
```

Output formats: `list` (default), `json`, `spdx`, `cyclonedx`.

```bash
sudo docker scout sbom --format spdx <image>:<tag>
```

**Get remediation recommendations**

Shows suggested base image updates that would resolve known CVEs.

```bash
sudo docker scout recommendations <image>:<tag>
```

**Compare two images**

Useful for checking whether a rebuild actually reduced vulnerabilities.

```bash
sudo docker scout compare <image>:<tag> --to <image>:<other-tag>
```

**Check policy compliance**

Docker Scout policies define rules (e.g., no critical CVEs, base image must be current). This command checks an image against those policies.

```bash
sudo docker scout policy <image>:<tag>
```

Policies are configured in Docker Scout settings for an organization. See [docs.docker.com/scout/policy/](https://docs.docker.com/scout/policy/) for details.

---

## Image Signing with cosign and Notation Update

> **Source:** [https://docs.docker.com/engine/security/trust/](https://docs.docker.com/engine/security/trust/)

**Background**

Docker Content Trust (DCT), covered earlier in this module, is built on Notary v1. It works, but the OCI (Open Container Initiative) community has moved toward two newer signing tools: **cosign** (from the Sigstore project) and **Notation** (from the CNCF Notary project). Both produce OCI-compliant signatures that attach to the image manifest in the registry.

DCT is still functional as of 2026. cosign and Notation are the tools used in newer CI/CD pipelines.

**cosign basics**

cosign is a tool from the [Sigstore](https://www.sigstore.dev) project. It signs container images using either a key pair or keyless signing via OIDC identity.

Install cosign (Linux binary):

```bash
# Download the latest release binary
curl -LO https://github.com/sigstore/cosign/releases/latest/download/cosign-linux-amd64
chmod +x cosign-linux-amd64
sudo mv cosign-linux-amd64 /usr/local/bin/cosign
```

Generate a key pair:

```bash
cosign generate-key-pair
```

Sign an image (the image must already be pushed to a registry):

```bash
cosign sign --key cosign.key <registry>/<image>:<tag>
```

Verify a signed image:

```bash
cosign verify --key cosign.pub <registry>/<image>:<tag>
```

Keyless signing uses a short-lived certificate tied to your OIDC identity (GitHub Actions, Google, Microsoft). No key pair is stored:

```bash
cosign sign <registry>/<image>:<tag>
```

**Notation basics**

Notation is maintained by the CNCF and follows the OCI Image Signing specification. It is integrated into tools like Azure Container Registry and AWS Signer.

```bash
# Sign an image
notation sign <registry>/<image>:<tag>

# Verify a signed image
notation verify <registry>/<image>:<tag>
```

**Which to use**

| Tool | Standard | Common with |
|---|---|---|
| DCT (Notary v1) | Docker-specific | Older Docker workflows |
| cosign | Sigstore / OCI | GitHub Actions, open-source projects |
| Notation | OCI (CNCF Notary v2) | Azure, AWS, enterprise registries |

All three produce signatures that attest an image has not been tampered with since signing. Pick the one that matches your registry and CI/CD tooling.