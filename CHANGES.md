Change log
==========

1.1.0-rc2 (2015-01-29)
----------------------

On top of the changelog for 1.1.0-rc1 (see below), the following bugs have been fixed:

- When an environment variables file specified with `env_file` doesn't exist, Compose was showing a stack trace instead of a helpful error.

- Configuration files using the old name (`fig.yml`) were not being read unless explicitly specified with `docker-compose -f`. Compose now reads them and prints a deprecation warning.

- Bash tab completion now reads `fig.yml` if it's present.

1.1.0-rc1 (2015-01-20)
----------------------

Fig has been renamed to Docker Compose, or just Compose for short. This has several implications for you:

- The command you type is now `docker-compose`, not `fig`.
- You should rename your fig.yml to docker-compose.yml.
- If you’re installing via PyPi, the package is now `docker-compose`, so install it with `pip install docker-compose`.

Besides that, there’s a lot of new stuff in this release:

- We’ve made a few small changes to ensure that Compose will work with Swarm, Docker’s new clustering tool (https://github.com/docker/swarm). Eventually you'll be able to point Compose at a Swarm cluster instead of a standalone Docker host and it’ll run your containers on the cluster with no extra work from you. As Swarm is still developing, integration is rough and lots of Compose features don't work yet.

- `docker-compose run` now has a `--service-ports` flag for exposing ports on the given service. This is useful for e.g. running your webapp with an interactive debugger.

- You can now link to containers outside your app with the `external_links` option in docker-compose.yml.

- You can now prevent `docker-compose up` from automatically building images with the `--no-build` option. This will make fewer API calls and run faster.

- If you don’t specify a tag when using the `image` key, Compose will default to the `latest` tag, rather than pulling all tags.

- `docker-compose kill` now supports the `-s` flag, allowing you to specify the exact signal you want to send to a service’s containers.

- docker-compose.yml now has an `env_file` key, analogous to `docker run --env-file`, letting you specify multiple environment variables in a separate file. This is great if you have a lot of them, or if you want to keep sensitive information out of version control.

- docker-compose.yml now supports the `dns_search`, `cap_add`, `cap_drop` and `restart` options, analogous to `docker run`’s `--dns-search`, `--cap-add`, `--cap-drop` and `--restart` options.

- Compose now ships with Bash tab completion - see the installation and usage docs at https://github.com/docker/fig/blob/1.1.0-rc1/docs/completion.md

- A number of bugs have been fixed - see the milestone for details: https://github.com/docker/fig/issues?q=milestone%3A1.1.0+

1.0.1 (2014-11-04)
------------------

 - Added an `--allow-insecure-ssl` option to allow `fig up`, `fig run` and `fig pull` to pull from insecure registries.
 - Fixed `fig run` not showing output in Jenkins.
 - Fixed a bug where Fig couldn't build Dockerfiles with ADD statements pointing at URLs.

1.0.0 (2014-10-16)
------------------

The highlights:

 - [Fig has joined Docker.](https://www.orchardup.com/blog/orchard-is-joining-docker) Fig will continue to be maintained, but we'll also be incorporating the best bits of Fig into Docker itself.

   This means the GitHub repository has moved to [https://github.com/docker/fig](https://github.com/docker/fig) and our IRC channel is now #docker-fig on Freenode.

 - Fig can be used with the [official Docker OS X installer](https://docs.docker.com/installation/mac/). Boot2Docker will mount the home directory from your host machine so volumes work as expected.

 - Fig supports Docker 1.3.

 - It is now possible to connect to the Docker daemon using TLS by using the `DOCKER_CERT_PATH` and `DOCKER_TLS_VERIFY` environment variables.

 - There is a new `fig port` command which outputs the host port binding of a service, in a similar way to `docker port`.

 - There is a new `fig pull` command which pulls the latest images for a service.

 - There is a new `fig restart` command which restarts a service's containers.

 - Fig creates multiple containers in service by appending a number to the service name (e.g. `db_1`, `db_2`, etc). As a convenience, Fig will now give the first container an alias of the service name (e.g. `db`).
   
   This link alias is also a valid hostname and added to `/etc/hosts` so you can connect to linked services using their hostname. For example, instead of resolving the environment variables `DB_PORT_5432_TCP_ADDR` and `DB_PORT_5432_TCP_PORT`, you could just use the hostname `db` and port `5432` directly.

 - Volume definitions now support `ro` mode, expanding `~` and expanding environment variables.

 - `.dockerignore` is supported when building.

 - The project name can be set with the `FIG_PROJECT_NAME` environment variable.

 - The `--env` and `--entrypoint` options have been added to `fig run`.

 - The Fig binary for Linux is now linked against an older version of glibc so it works on CentOS 6 and Debian Wheezy.

Other things:

 - `fig ps` now works on Jenkins and makes fewer API calls to the Docker daemon.
 - `--verbose` displays more useful debugging output.
 - When starting a service where `volumes_from` points to a service without any containers running, that service will now be started.
 - Lots of docs improvements. Notably, environment variables are documented and official repositories are used throughout.

Thanks @dnephin, @d11wtq, @marksteve, @rubbish, @jbalonso, @timfreund, @alunduil, @mieciu, @shuron, @moss, @suzaku and @chmouel! Whew.

0.5.2 (2014-07-28)
------------------

 - Added a `--no-cache` option to `fig build`, which bypasses the cache just like `docker build --no-cache`.
 - Fixed the `dns:` fig.yml option, which was causing fig to error out.
 - Fixed a bug where fig couldn't start under Python 2.6.
 - Fixed a log-streaming bug that occasionally caused fig to exit.

Thanks @dnephin and @marksteve!


0.5.1 (2014-07-11)
------------------

 - If a service has a command defined, `fig run [service]` with no further arguments will run it.
 - The project name now defaults to the directory containing fig.yml, not the current working directory (if they're different)
 - `volumes_from` now works properly with containers as well as services
 - Fixed a race condition when recreating containers in `fig up`

Thanks @ryanbrainard and @d11wtq!


0.5.0 (2014-07-11)
------------------

 - Fig now starts links when you run `fig run` or `fig up`.
   
   For example, if you have a `web` service which depends on a `db` service, `fig run web ...` will start the `db` service.

 - Environment variables can now be resolved from the environment that Fig is running in. Just specify it as a blank variable in your `fig.yml` and, if set, it'll be resolved:
   ```
   environment:
     RACK_ENV: development
     SESSION_SECRET:
   ```

 - `volumes_from` is now supported in `fig.yml`. All of the volumes from the specified services and containers will be mounted:

   ```
   volumes_from:
    - service_name
    - container_name
   ```

 - A host address can now be specified in `ports`:

   ```
   ports:
    - "0.0.0.0:8000:8000"
    - "127.0.0.1:8001:8001"
   ```

 - The `net` and `workdir` options are now supported in `fig.yml`.
 - The `hostname` option now works in the same way as the Docker CLI, splitting out into a `domainname` option.
 - TTY behaviour is far more robust, and resizes are supported correctly.
 - Load YAML files safely.

Thanks to @d11wtq, @ryanbrainard, @rail44, @j0hnsmith, @binarin, @Elemecca, @mozz100 and @marksteve for their help with this release!


0.4.2 (2014-06-18)
------------------

 - Fix various encoding errors when using `fig run`, `fig up` and `fig build`.

0.4.1 (2014-05-08)
------------------

 - Add support for Docker 0.11.0. (Thanks @marksteve!)
 - Make project name configurable. (Thanks @jefmathiot!)
 - Return correct exit code from `fig run`.

0.4.0 (2014-04-29)
------------------

 - Support Docker 0.9 and 0.10
 - Display progress bars correctly when pulling images (no more ski slopes)
 - `fig up` now stops all services when any container exits
 - Added support for the `privileged` config option in fig.yml (thanks @kvz!)
 - Shortened and aligned log prefixes in `fig up` output
 - Only containers started with `fig run` link back to their own service
 - Handle UTF-8 correctly when streaming `fig build/run/up` output (thanks @mauvm and @shanejonas!)
 - Error message improvements

0.3.2 (2014-03-05)
------------------

 - Added an `--rm` option to `fig run`. (Thanks @marksteve!)
 - Added an `expose` option to `fig.yml`.

0.3.1 (2014-03-04)
------------------

 - Added contribution instructions. (Thanks @kvz!)
 - Fixed `fig rm` throwing an error.
 - Fixed a bug in `fig ps` on Docker 0.8.1 when there is a container with no command.

0.3.0 (2014-03-03)
------------------

 - We now ship binaries for OS X and Linux. No more having to install with Pip!
 - Add `-f` flag to specify alternate `fig.yml` files
 - Add support for custom link names
 - Fix a bug where recreating would sometimes hang
 - Update docker-py to support Docker 0.8.0.
 - Various documentation improvements
 - Various error message improvements

Thanks @marksteve, @Gazler and @teozkr!

0.2.2 (2014-02-17)
------------------

 - Resolve dependencies using Cormen/Tarjan topological sort
 - Fix `fig up` not printing log output
 - Stop containers in reverse order to starting
 - Fix scale command not binding ports

Thanks to @barnybug and @dustinlacewell for their work on this release.

0.2.1 (2014-02-04)
------------------

 - General improvements to error reporting (#77, #79)

0.2.0 (2014-01-31)
------------------

 - Link services to themselves so run commands can access the running service. (#67)
 - Much better documentation.
 - Make service dependency resolution more reliable. (#48)
 - Load Fig configurations with a `.yaml` extension. (#58)

Big thanks to @cameronmaske, @mrchrisadams and @damianmoore for their help with this release.

0.1.4 (2014-01-27)
------------------

 - Add a link alias without the project name. This makes the environment variables a little shorter: `REDIS_1_PORT_6379_TCP_ADDR`. (#54)

0.1.3 (2014-01-23)
------------------

 - Fix ports sometimes being configured incorrectly. (#46)
 - Fix log output sometimes not displaying. (#47)

0.1.2 (2014-01-22)
------------------

 - Add `-T` option to `fig run` to disable pseudo-TTY. (#34)
 - Fix `fig up` requiring the ubuntu image to be pulled to recreate containers. (#33) Thanks @cameronmaske!
 - Improve reliability, fix arrow keys and fix a race condition in `fig run`. (#34, #39, #40)

0.1.1 (2014-01-17)
------------------

 - Fix bug where ports were not exposed correctly (#29). Thanks @dustinlacewell!

0.1.0 (2014-01-16)
------------------

 - Containers are recreated on each `fig up`, ensuring config is up-to-date with `fig.yml` (#2)
 - Add `fig scale` command (#9)
 - Use `DOCKER_HOST` environment variable to find Docker daemon, for consistency with the official Docker client (was previously `DOCKER_URL`) (#19)
 - Truncate long commands in `fig ps` (#18)
 - Fill out CLI help banners for commands (#15, #16)
 - Show a friendlier error when `fig.yml` is missing (#4)
 - Fix bug with `fig build` logging (#3)
 - Fix bug where builds would time out if a step took a long time without generating output (#6)
 - Fix bug where streaming container output over the Unix socket raised an error (#7)

Big thanks to @tomstuart, @EnTeQuAk, @schickling, @aronasorman and @GeoffreyPlitt.

0.0.2 (2014-01-02)
------------------

 - Improve documentation
 - Try to connect to Docker on `tcp://localdocker:4243` and a UNIX socket in addition to `localhost`.
 - Improve `fig up` behaviour
 - Add confirmation prompt to `fig rm`
 - Add `fig build` command

0.0.1 (2013-12-20)
------------------

Initial release.


