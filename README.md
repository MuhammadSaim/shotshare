<p align="center">

![ShotShare banner image](/.docs/assets/banner.png)

</p>

<div align="center">

<p>An open-source self hosted image sharing platform.</p>

<a href="">![ci](https://img.shields.io/github/actions/workflow/status/mdshack/shotshare/.github%2Fworkflows%2Fci.yaml?style=for-the-badge&label=CI)</a>
<a href="">![coverage report](https://img.shields.io/codecov/c/github/mdshack/shotshare?style=for-the-badge&token=S4MN02T3LN)</a>
<a href="">![latest release](https://img.shields.io/github/v/release/mdshack/shotshare?logo=github&style=for-the-badge)</a>

</div>

---

## About

ShotShare is an open source, self hosted, bare bones image posting/sharing platform, it was built to allow friends to upload screenshots and send links to their friends without the constant barrage of ads/extraneous features.

## Demo

A demo may be found [here](https://demo.shotshare.mdsh.dev/). This environment is _strictly_ for trialing the application; images added here are automatically cleaned up after a few minutes.

## Installation, Configuration, & Local Development

ShotShare is meant to be extremely easy to self host. Below is a sample deployment using Docker.

### Example Deployment with Docker

1. Create a directory for ShotShare: `sudo mkdir /shotshare`
2. Create a `.env` file that will manage ShotShare generated environment variables (ex. your application key): `sudo touch /shotshare/.env`
3. Ensure the user/group 82 (`www-data` user in docker container) own the `.env` file: `sudo chown 82:82 /shotshare/.env`
4. Start the ShotShare container 

_You may wish to customize environment variables (such as the `HOST`) before running this command, see below for a list of environment variables._

#### Run in HTTPS mode

This will use Caddy's [Automatic HTTPs](https://caddyserver.com/docs/automatic-https) setup generate SSL certificates, handle renewals, and automagically redirect your visitors to HTTPS. For most scenarios, this is the "preferred" installation method.

```sh
docker run \
  -p 80:80 \
  -p 443:443 \
  -e HOST=localhost \
  -v shotshare_caddy_data:/data/caddy \
  -v shotshare_caddy_config:/config/caddy \
  -v shotshare_database:/app/database \
  -v shotshare_data:/app/storage \
  --mount type=bind,source=/shotshare/.env,target=/app/.env \
  -d \
  --restart unless-stopped \
  --name shotshare \
  mdshack/shotshare:latest
```

#### Run in HTTP mode

This will _not_ issue any SSL and will just serve over HTTP. This may be useful if you plan to front ShotShare with your own reverse proxy.

If you plan to force HTTPS before it gets to ShotShare, you will also need to force ShotShare to utilize HTTPS links, you may do so by adding `-e FORCE_HTTPS=true`.

```sh
docker run \
  -p 80:80 \
  -e HOST=":80" \
  -v shotshare_database:/app/database \
  -v shotshare_data:/app/storage \
  --mount type=bind,source=/shotshare/.env,target=/app/.env \
  -d \
  --restart unless-stopped \
  --name shotshare \
  mdshack/shotshare:latest
```

### Configuration Options

_Note: These are not all of the configuration options, only the most used ones, feel free to open a PR if you see any missing. For a more in-depth look at all the available options check out [here](/config)._

| Environment Variable  | Default | Options | Description |
| ------------- | ------------- | ------------- | ------------- |
| `ALLOW_REGISTRATION`  | `true`  | `true`, `false` | Allows new users to register |
| `DB_CONNECTION`  | `sqlite`  | `sqlite`,`mysql`,`pgsql`,`sqlsrv` | Indicates what database connection will be used |
| `DB_HOST`  | `127.0.0.1` | _N/A_ | Not required if using `sqlite`, indicates the database host |
| `DB_PORT`  | `3306` | _N/A_ | Not required if using `sqlite`, indicates the database port |
| `DB_DATABASE`  | `shotshare` | _N/A_ | Not required if using `sqlite`, indicates the database database |
| `DB_USERNAME`  | `shotshare` | _N/A_ | Not required if using `sqlite`, indicates the database username |
| `DB_PASSWORD`  | _none_ | _N/A_ | Not required if using `sqlite`, indicates the database password |
| `HOST`  | localhost | _N/A_ | Public host used by Caddy, thanks to caddy, this host will automatically be issued a SSL certificate |

### Commands

_Note: these are all "artisan" commands, and thus must be prefixed with `php artisan` (ex. `php artisan shotshare:clean-images`). These also must be executed inside of your docker container, an easy way to do that is using `docker exec -it shotshare php artisan [command you wish to issue]`._

| Environment Variable | Description |
| ------------- | ------------- |
| `shotshare:clean-images` | Clears out all images (from storage and application) |
| `shotshare:create-user`  | Creates a new user (useful for `ALLOW_REGISTRATION=false`) |

### Local Development

Local development is done using `docker compose`

1. Pull the repo: `git clone git@github.com:mdshack/shotshare.git`
2. Change to the ShotShare directory: `cd shotshare`
3. Install dependencies: `composer install; npm install`
4. Build frontend: `npm run build`
5. Start docker compose: `docker compose up`

## Contributing

There is currently no established pattern for contributing, if you see something missing or feel like something could be better feel free to pop open an issue and/or PR.

## License

ShotShare is open-sourced and licensed under the [MIT License](/LICENSE).
