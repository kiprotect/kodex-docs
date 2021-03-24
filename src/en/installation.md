# Kodex Community Edition (CE)

To install the community edition of Kodex, simply run

<!--translate:ignore-->
<div class="highlight">
    {%filter highlight(strip=True, language='bash')%}
		git clone https://github.com/kiprotect/kodex
		cd kodex
		make
		make install
	{% endfilter %}
</div>
<!--translate:ignore-->

That's it! You can now process blueprints with the `kodex` CLI command.

# Kodex Enterprise Edition (EE)

To install the enterprise edition of Kodex (Kodex-EE), first download `kodex-ee` from our [web portal](https://app.kiprotect.com). The binary contains everything you need to get started. Notably, it provides an `install` command that (mostly) automates the installation of Kodex-EE for different environments. For example, to create a Docker compose configuration with all Kodex components and required external services, simply run

<!--translate:ignore-->
<div class="highlight">
    {%filter highlight(strip=True, language='bash')%}
		kodex-ee install --method docker-compose
		cd docker-compose
		docker-compose up
	{% endfilter %}
</div>
<!--translate:ignore-->

That should give you a fully functional test system with the Kodex frontend running on port 4242. The following sections explain in detail how to get Kodex-EE running on your infrastructure in a more permanent way.

## Deployment Options

Kodex-EE is deployed as a set of stateless Linux services that interact with external databases and systems, as described above. A full deployment consists of at least one **daemon** and one **API** instance. Both of these services are stateless and can be configured using a simple settings file. The API is designed to be used behind a reverse proxy that handles TLS and load balancing (example configurations for servers like **nginx** or **caddy** is available).

## Requirements

Kodex-EE requires several external services to function:

* A **PostgreSQL** database (preferrably version 9.6 or newer), which Kodex-EE uses to store meta-data about data streams, actions and parameters. Specific Kodex-EE plugins (e.g. Klaro, Konsens) use their own PostgreSQL schemas for data storage. For stability and performance reasons it is advisable to provide separate PostgreSQL instances to these.  
* A **RabbitMQ** server (preferrably version 3.8 or newer), or a **Kafka** server (preferrably version 2.7 or newer), which Kodex-EE uses to store intermediate data items during processing.
* An **authentication service based on SSO, SAML, OpenID-Connect or LDAP**, which Kodex-EE can use to authenticate users. The service must provide a mapping of users to organizations and roles within those organizations.
* A **Redis** database (preferrably version 5.0 or newer), which Kodex-EE uses to e.g. store API and data processing metrics as well as data necessary for rate limiting.
* *Optionally*, either a **persistent directory/volume** that is accessible from API and daemon nodes, or a **"blob" storage service** with an S3-compatible API. Kodex-EE uses these to store file data that is generated for certain background tasks.

The given minimum versions of services are what Kodex-EE is tested and deployed against, it might be possible to run it with older versions as well, though we cannot make any guarantees. The same is true when using newer versions of these services.

### PostgreSQL Database(s)

Kodex-EE uses a primary database schema to store meta-data about things like data transformations, sources and destinations. For regular usage, only a small number of table entries are generated, and the primary database schema rarely grows beyond a few GB. Specific Kodex-EE plugins (e.g. Klaro or Konsens) use secondard database schemas to store data results, which grow in proportion to the processed data. Kodex-EE can implement a data retention schedule and will delete old data, nevertheless the required storage will depend on the amount of data processed. Please note that performance may also degrade if very large amounts of data are being processed. All Kodex-EE data schemas are designed to handle billions of rows of data without significant problems though, provided adequate hardware has been provisioned for the databases.

### Redis Database(s)

Kodex-EE uses Redis for metrics collection and keeping of internal state during processing (e.g. for anonymization). Metrics are generated using a hierarchical time-spans (hours, days, weeks, months, years) and individual data items expire automatically. Internal state storage requirements will vary depending on the type of data processing and the number of items processed.

### Message Queue(s) (RabbitMQ or Kafka)

Kodex-EE stores processed data in a message queue for internal processing and buffering. The amount of data stored in those queues depends on many factors, notably the number of incoming data items, the number of daemon nodes and the read/write capacity of data sources and destinations. Kodex-EE will automatically apply backpressure when writes to the internal queue become impossible.

## Daemon & API Requirements

Kodex EE can run on systems with minimal resources like an embedded system or SoC.

### Operating System

Kodex-EE is delivered as a statically compiled X64/AMD64 binary that is compatible with all mayor Linux distributions. Running Kodex-EE on Windows, MacOS and OpenBSD should be possible but is currently untested and unsupported.

### Memory

We recommend at least 512 MB of available RAM for both the **API** and the **daemon**. Actual memory usage can vary depending on the workloads but Kodex EE will try to automatically limit data processing in order to not exhaust all available memory.

### CPU

Both the daemon and the API process data concurrently in parallel threads, hence they can effectively utilize multiple CPU cores / processing threads.

### Storage

Apart from task-specific storage mentioned above, neither the daemon nor the API require persistent on-disk data storage.

### Networking

Kodex-EE needs to have connectivity to data sources and destinations from which it should read or to which it should write data. Processing speed will be limited by available network bandwidth.

# Operations

The following sections describe operations-related aspects of Kodex-EE.

## Metrics

Kodex-EE collects various metrics that are exposed via the REST API and supports instrumentation for Prometheus.

## Logging

Kodex-EE has an internal, level-based logging system that is compatible with **syslog**.
