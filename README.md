# Gitlab ci runner with hetzner docker machine plugin

Run your ci jobs on Hetzner cloud with the help of gitlab ci and the [docker-machine-driver-hetzner](https://github.com/JonasProgrammer/docker-machine-driver-hetzner). You can follow the official [gitlab docs](https://docs.gitlab.com/runner/executors/docker_machine.html) for configuring the gitlab ci runner with docker machine, just use this image as a drop in replacement for `gitlab/gitlab-runner`. See the example config.toml below for the hetzner specific docker machine options.

## Usage

Use [this image](https://hub.docker.com/r/mawalu/hetzner-gitlab-runner) instead of the `gitlab/gitlab-runner` image and set `MachineDriver` to `hetzner` in your runner configuration.

Example config.toml:

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

## Versions

Currently this image is build using the `gitlab/gitlab-runner:latest` image and the latest docker-machine hetzner plugin. If you need builds for another version feel free to open an PR.

## License 

MIT

