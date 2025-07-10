# Laravel

## Setup

### Containerized with Sail

1. Install PHP, Composer and Laravel

```shell
/bin/bash -c "$(curl -fsSL https://php.new/install/linux/8.4)"
```

2. Create a project and navigate inside it.

```shell
laravel new laravel-app
cd laravel-app
```

3. Set bash alias for **Sail** and restart terminal.

```shell
alias sail='sh $([ -f sail ] && echo sail || echo vendor/bin/sail)'
```

4. Run these commands they are equivalent to Docker commands.

```shell
# To start all laravel services
sail up -d

# To stop all laravel services
sail stop
```

5. Run migration while the app is running.

```shell
sail artisan migrate
# DONT USE THIS: php artisan migrate
```