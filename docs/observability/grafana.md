# Grafana

## Configuration

It uses the `/etc/grafana/grafana.ini` file by default (for Docker) - [Grafana Docker](https://grafana.com/docs/grafana/latest/setup-grafana/configure-docker/) 

When using containers it can be overridden with `GF_` env vars - [Grafana Environment Variables](https://grafana.com/docs/grafana/latest/setup-grafana/configure-grafana/#configure-with-environment-variables)

The general pattern is that an env var `GF_<SECTION>_<SETTING>=<VALUE>` corresponds to `[<SECTION>] <SETTING> = <VALUE>` in grafana.ini

**Useful Environment Variables**:

1. `GF_SECURITY_ADMIN_USER` and `GF_SECURITY_ADMIN_PASSWORD` - setting admin user and password.
2. `GF_SERVER_ROOT_URL` - setting what external address grafana is accessed on
3. `GF_SERVER_HTTP_PORT` - setting what port grafana actually listens on.
4. `GF_SERVER_SERVE_FROM_SUB_PATH` - set to true if you want grafana to be accessed from a subpath/route.
5. `GF_LOG_LEVEL` - set to "error", "warn", "info" or "debug".
6. `GF_USERS_DEFAULT_THEME` - set to "dark" or "light".

If environment variables changes are not taking effect (for containers) then remove the volumes by `docker volume ls` to find the grafana volume and `docker rm <grafana_volume>`.
