# Gitlab ci runner with hetzner docker machine plugin

Run your tests on Hetzner cloud with the help of gitlab ci and the [docker-machine-driver-hetzner](https://github.com/JonasProgrammer/docker-machine-driver-hetzner).

## Usage

Use this image instead of the `gitlab/gitlab-runner` image and set `MachineDriver` to `hetzner` in your runner configuration.

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

## License 

MIT

