

## `kodex-ee` Commands

Kodex-EE consists is distributed as a single binary `kodex-ee` with several subcommands that run the different services. For example, `kodex-ee api run` will run the API server, `kodex-ee daemon run` will run the daemon and `kodex-ee app run` will serve the frontend files (which the `api` command is also capable of btw). `kodex init` will perform necessary initializations like the creation of database tables.

### `settings`

The `settings` command helps you to manage Kodex settings. By default, Kodex will look for the environment variable `KODEX_SETTINGS`, which should contain a list of directories separated by `:`. It will then load all `.yml` files in these directories in the specified order (and sorted alphanumerically) to construct the global settings.

If you want to generate a minimal, annotated settings file simply run

<!--translate:ignore-->
```
kodex-ee settings generate [--filename [settings.yml]]
```
<!--translate:ignore-->

This will generate a bare settings file you can modify.

### `install`

The `install` command helps you to install Kodex by automating specific installation steps like the generation of Docker images or service configuration files.

### `api`

The `api` command executes the REST API of Kodex-EE. You can run multiple instances of the API on the same machine (not recommended) or a fleet of machines to scale the processing capabilities of Kodex.

### `daemon`

The `daemon` command executes the daemon of Kodex-EE, which performs all asynchronous data processing tasks as well as background tasks for all system components. You can run multiple instances of the daemon on the same machine (not recommended) or a fleet of machines to scale the processing capabilities of Kodex.

### `init`

The `init` command performs necessary initialization actions like the migration of database tables. You should run it whenever you upgrade Kodex to the latest version.

### `upgrade`

The `upgrade` command will download the latest version of Kodex and replace the binary with it.

## Plugins

Kodex-EE contains several plugins that provide additional functionality beyond the core components. For example, the Klaro plugin provides a privacy & security management platform for websites. Using these plugins might require additional services, which we describe in the following sections.

### Klaro

Klaro provides a website scanning functionality that is based on a headless Chrome browser running inside a Docker container. Running Klaro on-premises requires a working Docker daemon on the nodes that run these tasks.