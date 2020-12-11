# Auto-scale CI jobs with Gitlab Runner and the Hetzner Docker Machine driver

With this patched GitLab Runner image, you can auto-scale your CI jobs on Hetzner Cloud. The patch installs the [Hetzner Docker Machine driver](https://github.com/JonasProgrammer/docker-machine-driver-hetzner) and is available on [Docker Hub](https://hub.docker.com/r/mawalu/hetzner-gitlab-runner).

You can follow the official [GitLab docs](https://docs.gitlab.com/runner/executors/docker_machine.html) for configuring the GitLab Runner with docker machine, just use this image as a drop in replacement for `gitlab/gitlab-runner`.

See the example `config.toml` and `docker-compose.yaml` below for the hetzner specific docker machine options.

## Usage

Use [this image](https://hub.docker.com/r/mawalu/hetzner-gitlab-runner) instead of the `gitlab/gitlab-runner` image and set `MachineDriver` to `hetzner` in your runner configuration.

Example `config.toml`:

```toml
concurrent = 1
check_interval = 0

[session_server]
  session_timeout = 1800

[[runners]]
  name = "docker-machine"
  url = "https://gitlab.com"
  token = "your-token"
  executor = "docker+machine"
  [runners.docker]
    tls_verify = false
    image = "docker:latest"
    privileged = true
    disable_cache = false
    volumes = ["/cache"]
    shm_size = 536870912
  [runners.cache]
  [runners.machine]
    IdleCount = 0
    IdleTime = 1800
    MaxBuilds = 10
    MachineDriver = "hetzner"
    MachineName = "runner-%s"
    MachineOptions = [
      "hetzner-api-token=hetzner-api-token",
      "hetzner-image=ubuntu-18.04",
      "hetzner-server-type=cx31",
    ]
```

Example `docker-compose.yaml`:

```yaml
version: "3.8"

services:
  hetzner-runner:
    image: mawalu/hetzner-gitlab-runner:latest
    volumes:
      - "./hetzner_config:/etc/gitlab-runner"
```

### Docker Errors

If you face docker problems at runtime (such as the one below), you can specify a Docker version in the `MachineOptions`.

```toml
 MachineOptions = [
      "engine-install-url=https://releases.rancher.com/install-docker/19.03.9.sh",
    ]
```

Possible error that is caused by Docker:

```bash
ERROR: Error creating machine: Error running provisioning: Unable to verify the Docker daemon is listening: Maximum number of retries (10) exceeded  driver=hetzner name=runner-xxx-xxx operation=create
```

## Versions

Currently this image is build using the `gitlab/gitlab-runner:latest` image and the latest docker-machine hetzner plugin. If you need builds for another version feel free to open an PR.

## License

MIT
