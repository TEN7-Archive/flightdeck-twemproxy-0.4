![Flight Deck](https://raw.githubusercontent.com/ten7/flight-deck/master/flightdeck-logo.png)

# Flight Deck twemproxy

Flight Deck twemproxy is a minimalist twemproxy container for Drupal sites on Kubernetes and Docker. You can use it both for local development and production.

Features:
* ConfigMap-friendly YAML configuration
## Tags and versions

There are several tags available for this container, each with different twemproxy and Drupal module support:

| Tags | twemproxy Version | Search API | Custom cores |
| ---- | ------------ | ---------- | ------------ |
| latest | 0.4 | Yes | Yes |
| x.y.z | 0.4 | Yes | Yes |


## Configuration

Instead of a large number of environment variables, this container relies on a file to perform all runtime configuration, `flightdeck-twemproxy.yml`. Inside the file, create following:

```yaml
---
flightdeck_twemproxy: {}
```

All configuration is done as items under the `flightdeck_twemproxy` variable. See the following sections for details as to particular configurations.

You can provide this file in one of three ways to the container:

* Mount the configuration file at path `/config/twemproxy/flightdeck-twemproxy.yml` inside the container using a bind mount, configmap, or secret.
* Mount the config file anywhere in the container, and set the `FLIGHTDECK_CONFIG_FILE` environment variable to the path of the file.
* Encode the contents of `flightdeck-twemproxy.yml` as base64 and assign the result to the `FLIGHTDECK_CONFIG` environment variable.

### Basic settings

```yaml
---
flightdeck_twemproxy:
  confFile: "/config/twemproxy/twemproxy.yml"
  statsInterval: "30000"
  mbufSize: "16384"
  statsPort: "22222"
```

Where:
* **flightdeck_twemproxy.confFile** is the location of the twemproxy configuration file. Optional, defailts to `/config/twemproxy/twemproxy.yml`.
* **flightdeck_twemproxy.statsInterval** is the interval at which to publish stats. Optional, defaults to `30000`.
* **flightdeck_twemproxy.mbufSize** is the mbuf size. Optional, defaults to `16384`.
* **flightdeck_twemproxy.statsPort** is the port on which to access stats. Optional, defaults to `22222`.

### Providing configuration inline

Twemproxy accepts a separate YAML file as configuration. In some circumstances, you may wish to include that configuration inline with the flightdeck-twemproxy.yml instead. One such use is if you need to pass the configuration via environment variables such as FLIGHTDECK_CONFIG. To do this, use the `conf` key:

```yaml
---
flightdeck_twemproxy:
  conf: |
    alpha:
      listen: 127.0.0.1:22121
      hash: fnv1a_64
      distribution: ketama
      auto_eject_hosts: true
      redis: false
      server_retry_timeout: 2000
      server_failure_limit: 1
      servers:
       - memcache-0:11211:1
  statsInterval: "30000"
  mbufSize: "16384"
  statsPort: "22222"
```

Where:
* **flightdeck_twemproxy.conf** if you twemproxy configuration YAML.

For a full list of twemproxy configuration values, see [their documentation](https://github.com/twitter/twemproxy).

## Part of Flight Deck

This container is part of the [Flight Deck library](https://github.com/ten7/flight-deck) of containers for Drupal local development and production workloads on Docker, Swarm, and Kubernetes.

Flight Deck is used and supported by [TEN7](https://ten7.com/).

## Debugging

If you need to get verbose output from the entrypoint, set `flightdeck_debug` to `true` or `yes` in the config file.

```yaml
---
flightdeck_debug: yes
```

This container uses [Ansible](https://www.ansible.com/) to perform start-up tasks. To get even more verbose output from the start up scripts, set the `ANSIBLE_VERBOSITY` environment variable to `4`.

If the container will not start due to a failure of the entrypoint, set the `FLIGHTDECK_SKIP_ENTRYPOINT` environment variable to `true` or `1`, then restart the container.

## License

Flight Deck is licensed under GPLv3. See [LICENSE](https://raw.githubusercontent.com/ten7/flight-deck/master/LICENSE) for the complete language.
