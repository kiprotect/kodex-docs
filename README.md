# KIProtect Documentation

These files are used to build the documentation at https://kiprotect.com/docs.

## Setting up the environment

To build the docs, you will need a few tools installed on your machine:

- Node.js
- Python 3, pip3 and virtualenv.
- `inotify-tools` or `fswatch` if you're on Mac (only required for the watch command).

You can then install all website dependencies by running

    make setup

This will set up a virtual Python environment, require the Python dependencies
into it and also install all required node modules.

## Building the docs

To build the docs simply run Make:

```bash
make
```

## Serving the docs

To serve the docs, simply run

```bash
make serve
```

This will start a HTTP server on port 8000 serving a hot-reloading version of
the docs.

## Watching out for changes

To continuously update the build when things change, simply run

```bash
make watch
```

The watch command will automatically serve the docs as well using a
hot-reloading capable server.
