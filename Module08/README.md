# Module 08: Container Performance, Monitoring, and Benchmarking

* [Performance Engineering Overview](https://github.com/chaulags/learnDocker/tree/main/Module08#performance-engineering-overview)
* [Performance Goals and Metrics](https://github.com/chaulags/learnDocker/tree/main/Module08#performance-goals-and-metrics)
* [Create a Reliable Baseline](https://github.com/chaulags/learnDocker/tree/main/Module08#create-a-reliable-baseline)
* [Monitor Container Performance](https://github.com/chaulags/learnDocker/tree/main/Module08#monitor-container-performance)
* [Build a Lightweight Metrics Stack](https://github.com/chaulags/learnDocker/tree/main/Module08#build-a-lightweight-metrics-stack)
* [Benchmarking Workflows](https://github.com/chaulags/learnDocker/tree/main/Module08#benchmarking-workflows)
* [Bottlenecks and How to Detect Them](https://github.com/chaulags/learnDocker/tree/main/Module08#bottlenecks-and-how-to-detect-them)
* [Optimization Playbook](https://github.com/chaulags/learnDocker/tree/main/Module08#optimization-playbook)
* [Field-Tested Operator Tricks](https://github.com/chaulags/learnDocker/tree/main/Module08#field-tested-operator-tricks)
* [Hands-On Labs](https://github.com/chaulags/learnDocker/tree/main/Module08#hands-on-labs)
* [Official Documentation Updates (2026)](#official-documentation-updates-2026)
* [Compose Resource Controls Update](#compose-resource-controls-update)
* [Build Performance Update](#build-performance-update)
* [Monitoring and Debugging Update](#monitoring-and-debugging-update)


## Performance Engineering Overview
* Performance tuning without measurement is guessing.
* A good workflow always follows this loop:
  - Define target.
  - Capture baseline.
  - Apply one change.
  - Re-run benchmark.
  - Keep or rollback.
* Container performance depends on application code, runtime flags, host kernel, filesystem, and network path.
* Your objective is not to make one metric perfect; it is to improve service behavior under realistic load.


## Performance Goals and Metrics
* Choose a clear service objective before tuning.
* Common objectives:
  - Lower response time.
  - Increase throughput.
  - Reduce tail latency.
  - Lower compute cost per request.

| Metric | Why it matters | Typical unit |
|---|---|---|
| Latency (p50/p95/p99) | User experience and tail risk | ms |
| Throughput | Work completed over time | req/s |
| Error rate | Reliability under load | % |
| Saturation | How close resource is to limit | % |
| Startup time | Scale-out speed and recovery | s |

* Tail latency matters because users feel the slowest requests.
* A practical rule: optimize for p95/p99 once p50 is already acceptable.


## Create a Reliable Baseline
* Baseline first, optimize later.
* Freeze these variables for fair comparison:
  - Input dataset size.
  - Request profile and concurrency.
  - Host machine and power mode.
  - Container image tag and runtime arguments.
* Run warm-up requests before measurements to avoid cold-start bias.

```bash
# Example app under test
sudo docker run -d --name perf-api -p 8080:8080 myorg/perf-api:1.0

# Quick health check before benchmark
curl -s -o /dev/null -w "%{http_code}\n" http://localhost:8080/health
```

```bash
# Capture baseline resource behavior during a run
sudo docker stats perf-api --no-stream
sudo docker inspect perf-api --format '{{json .HostConfig}}' | jq '.'
```

* Record results in a comparison table.

| Run | Image | CPU limit | Memory limit | p95 latency | Throughput | Errors |
|---|---|---|---|---|---|---|
| Baseline | 1.0 | none | none | 180 ms | 750 req/s | 0.4% |
| Change A | 1.1 | 1.5 | 768m | 130 ms | 940 req/s | 0.2% |


## Monitor Container Performance
* Start with built-in Docker tools before introducing a full observability stack.

#### 1) `docker stats`
```bash
sudo docker stats
sudo docker stats perf-api worker-1 worker-2 --no-stream
```
* Watch CPU %, memory usage, network IO, block IO, and PIDs.

#### 2) `docker events`
```bash
sudo docker events --since 30m
```
* Correlate latency spikes with restarts, OOM kills, or healthcheck failures.

#### 3) `docker inspect`
```bash
sudo docker inspect perf-api --format '{{.State.OOMKilled}}'
sudo docker inspect perf-api --format '{{.RestartCount}}'
```
* Confirm whether failures are resource pressure related.

#### 4) Host-level visibility
```bash
# Linux host pressure signals
uptime
vmstat 1 5
iostat -xz 1 5
```
* Containers share host resources, so host contention is often the real bottleneck.


## Build a Lightweight Metrics Stack
* For time-series dashboards, use cAdvisor + Prometheus + Grafana.
* This gives historical trends and better before/after comparison.

```yaml
# compose.monitoring.yml
services:
  cadvisor:
    image: gcr.io/cadvisor/cadvisor:v0.49.1
    container_name: cadvisor
    ports:
      - "8081:8080"
    volumes:
      - /:/rootfs:ro
      - /var/run:/var/run:ro
      - /sys:/sys:ro
      - /var/lib/docker/:/var/lib/docker:ro

  prometheus:
    image: prom/prometheus:v2.54.1
    container_name: prometheus
    ports:
      - "9090:9090"
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml:ro

  grafana:
    image: grafana/grafana:11.1.4
    container_name: grafana
    ports:
      - "3000:3000"
```

```yaml
# prometheus.yml
global:
  scrape_interval: 5s

scrape_configs:
  - job_name: cadvisor
    static_configs:
      - targets: ["cadvisor:8080"]
```

```bash
sudo docker compose -f compose.monitoring.yml up -d
```

* Minimum useful dashboards:
  - Container CPU throttling time.
  - Working set memory and page faults.
  - Disk read and write latency.
  - Network retransmits and dropped packets.


## Benchmarking Workflows
* Use at least one tool per resource dimension.

| Dimension | Tools | Example |
|---|---|---|
| HTTP latency and throughput | `hey`, `wrk` | API endpoints |
| CPU and memory behavior | `sysbench` | Runtime stress |
| Disk IOPS and latency | `fio` | Data service paths |
| Network throughput and jitter | `iperf3` | Service-to-service link |

#### HTTP benchmark with `hey`
```bash
hey -n 20000 -c 100 http://localhost:8080/api/products
```

#### CPU benchmark with `sysbench`
```bash
sysbench cpu --cpu-max-prime=20000 --threads=4 run
```

#### Disk benchmark with `fio`
```bash
fio --name=randrw --rw=randrw --bs=4k --size=512M --numjobs=4 --runtime=60 --time_based --group_reporting
```

#### Network benchmark with `iperf3`
```bash
# Server
iperf3 -s

# Client
iperf3 -c <server-ip> -t 30 -P 4
```

* Benchmark discipline:
  - Run 3 to 5 iterations and compare median plus p95.
  - Change one variable at a time.
  - Keep benchmark window long enough to reach steady state.


## Bottlenecks and How to Detect Them

| Bottleneck | Common signal | Fast check | Typical fix |
|---|---|---|---|
| CPU throttling | High latency with moderate CPU usage | cgroup throttled periods | Raise CPU quota or tune concurrency |
| Memory pressure | OOMKilled, restarts, tail spikes | `docker inspect` state + host swap activity | Increase memory, reduce heap, tune GC |
| Disk contention | High IO wait, slow DB writes | `iostat -xz` and Block IO growth | Move hot paths to volume class with lower latency |
| Overlay network overhead | Throughput drops between services | `iperf3` between container hops | Use host networking when acceptable |
| DNS lookup slowness | Random latency spikes | App traces around resolution time | Add local cache, tune resolver config |
| Log backpressure | App stalls during bursts | Container logs lag and disk spikes | Async logging, limit log verbosity, rotate logs |
| Noisy neighbors | Unpredictable latency variance | Correlate with host-wide CPU/IO peaks | Resource pinning or isolate workloads |


## Optimization Playbook

#### CPU tuning
```bash
# Limit to 2 CPUs worth of compute
sudo docker run -d --cpus="2.0" myorg/perf-api:1.1

# Pin to specific cores to reduce scheduler movement
sudo docker run -d --cpuset-cpus="2,3" myorg/perf-api:1.1
```

#### Memory tuning
```bash
sudo docker run -d --memory="1g" --memory-swap="1g" myorg/perf-api:1.1
```
* Keep `--memory-swap` explicit so behavior is predictable under pressure.

#### Storage tuning
```bash
# Prefer named volumes for persistent write-heavy paths
sudo docker volume create api-data
sudo docker run -d -v api-data:/var/lib/app/data myorg/perf-api:1.1
```
* Avoid write-heavy workloads on slow bind mounts from network filesystems.

#### Network tuning
```bash
# Host mode can remove NAT overhead for selected workloads
sudo docker run -d --network host myorg/edge-proxy:1.1
```
* Use host mode only when isolation trade-offs are acceptable.

#### Startup optimization
* Multi-stage builds to reduce image size and startup pull time.
* Remove unnecessary shell tools and package managers from runtime layers.
* Pre-compile assets during build instead of at runtime.


## Field-Tested Operator Tricks
* These are practical habits used in production teams, with context and caution.

1. Keep one synthetic canary benchmark running in CI for every major image change.
2. Pin base image digests during benchmarking so nightly drift does not invalidate comparisons.
3. Use a dedicated benchmark host profile to reduce variance from background jobs.
4. Benchmark with production-like payload size, not toy payloads.
5. Export benchmark results as machine-readable logs (JSON/CSV) and track trend lines.
6. Separate control-plane metrics from app metrics when diagnosing spikes.
7. Tune healthcheck intervals carefully; aggressive checks can create load amplification.
8. Reduce unnecessary `stdout` chatter in high-QPS services to prevent log-driver bottlenecks.
9. Validate any performance gain against error budget, not only latency.
10. Keep rollback commands ready before applying runtime flag changes in production.

* Important: if a trick cannot be measured, treat it as a hypothesis, not a best practice.


## Hands-On Labs

### Lab 1: Baseline and Observe
* Goal: measure baseline p95 latency, throughput, and resource profile.
* Steps:
  - Run service without CPU and memory limits.
  - Run HTTP benchmark for fixed duration.
  - Capture `docker stats`, restart count, and host IO metrics.
* Output: baseline table row with metrics and environment notes.

### Lab 2: Reproduce a Bottleneck
* Goal: intentionally create pressure and identify root cause.
* Steps:
  - Apply strict memory limit.
  - Increase concurrency until latency rises and errors appear.
  - Verify OOM or throttling signals.
* Output: bottleneck diagnosis note with direct evidence.

### Lab 3: Apply Optimization
* Goal: fix the bottleneck using one targeted change.
* Steps:
  - Adjust only one variable (CPU quota, memory cap, or logging config).
  - Re-run identical benchmark settings.
* Output: before/after comparison and decision (keep or rollback).

### Lab 4: Produce a Performance Report
* Goal: convert raw test outputs into a short engineering report.
* Include:
  - Test setup and tool versions.
  - Baseline vs tuned chart.
  - p50, p95, p99, throughput, error rate.
  - Known limitations and next test ideas.


## Official Documentation Updates (2026)
* These updates reflect current official Docker guidance as of 2026.


## Compose Resource Controls Update

> **Sources:**
> - [https://docs.docker.com/compose/compose-file/05-services/#deploy](https://docs.docker.com/compose/compose-file/05-services/#deploy)
> - [https://docs.docker.com/reference/compose-file/services/#mem_limit](https://docs.docker.com/reference/compose-file/services/#mem_limit)

Compose now has clearer guidance on local vs swarm semantics for resource constraints.

* For local `docker compose up` workflows, prefer service-level resource keys supported by your Compose implementation.
* For swarm deployments, use `deploy.resources.limits` and `deploy.resources.reservations`.
* Always verify effective limits with:

```bash
sudo docker inspect <container-id> --format '{{json .HostConfig}}' | jq '.'
```


## Build Performance Update

> **Sources:**
> - [https://docs.docker.com/build/buildkit/](https://docs.docker.com/build/buildkit/)
> - [https://docs.docker.com/build/cache/](https://docs.docker.com/build/cache/)

BuildKit remains the default build engine and offers major performance improvements when cache is used correctly.

* Place stable Dockerfile instructions first.
* Keep dependency install layers above frequently changing source copy steps.
* Use cache mounts for package managers when possible.

```dockerfile
# syntax=docker/dockerfile:1.7
FROM node:22-alpine AS deps
WORKDIR /app
COPY package.json package-lock.json ./
RUN --mount=type=cache,target=/root/.npm npm ci

FROM node:22-alpine
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
CMD ["npm", "start"]
```


## Monitoring and Debugging Update

> **Sources:**
> - [https://docs.docker.com/reference/cli/docker/container/stats/](https://docs.docker.com/reference/cli/docker/container/stats/)
> - [https://docs.docker.com/reference/cli/docker/events/](https://docs.docker.com/reference/cli/docker/events/)

Docker CLI remains a strong first-line option for incident response.

* `docker stats` for immediate resource anomalies.
* `docker events` for timeline correlation.
* `docker logs --since` for scoped incident windows.

```bash
sudo docker logs --since 10m perf-api
sudo docker events --since 10m --until 0m
```

* For recurring performance incidents, move from ad-hoc CLI checks to persistent dashboards and alerts.
