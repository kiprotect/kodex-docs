# Blueprints

Blueprints are configurations that specify how Kodex should read, analyze transform and write data.

Privacy & security engineering is complex: In a typical scenario, we might want to read data from a variety of sources (e.g. a database, file or API) and process it for different purposes (e.g. analytics or anomaly detection), which often require different trasformations. We also might want to send the data to a variety of destinations (e.g. a database or message queue). Finally, we might want to do all this continously as data is produced.

To make this as easy as possible, we implemented a declarative and expressive config language based on YAML files. Other systems like Kubernetes or Ansible have shown that collections of YAML files can be powerful tools for configuration management. Also, files are easy to work with and can be versioned just like any other configuration file.

## Blueprint basics

<aside>
    The basic structure of a blueprint. This example would read data from a SQL database, pseudonymize it and forward the pseudonymized data to a HTTP API.

<!--translate:ignore-->
    <div class="highlight">
        {%filter highlight(strip=True, language='yaml')%}
            actions: # different actions we want to apply to our data items
              - name: pseudonymize-name
                type: pseudonymize
                ...
            sources: # sources we want to load data items from
              - name: production-db
                type: sql
                ...
            destinations: # destinations we want to send data items to
              - name: audit-api
                type: http
                ...
            streams: # streams of data items that we want to process
              - name: default
                description: |
                  Pseudonymize incoming data before sending it to the audit API.
                sources: # sources of data items for this stream
                  - production-db
                configs: # configurations for this stream
                  - name: default
                    actions: # actions in this configuration
                      - pseudonymize-name
                    destinations: # destinations that these items should be sent to
                      - name: audit-api
        {%endfilter%}
    </div>
<!--translate:ignore-->
</aside>

A blueprint essentially contains at least four different sections: Actions, sources, destinations and streams. Each stream refers to one or more sources and contains one or more stream configs. Each stream config refers to one or more actions as well as one or more destinations. Together these primitives tell Kodex how data should flow through it and how it should be analyzed and transformed.

## Running blueprints

You can run a given blueprint using the `run` command:

<!--translate:ignore-->
```
kodex run [blueprint name]
```
<!--translate:ignore-->

If you omit the <tr-hint v="blueprint name">`[blueprint name]`</tr-hint>, Kodex will look for a file named `.blueprint.yml` in the current directory to run. Otherwise, Kodex will first check if you entered a file location, and if so loads the blueprint from there. Otherwise, Kodex will go through all of its blueprint directories (by default `~/.kodex/blueprints`) and try to find the blueprint you specified. For example, if we run

<!--translate:ignore-->
```
kodex run pseudonymization/examples/data-types/pseudonymize
```
<!--translate:ignore-->

Kodex will try to find a blueprint named `pseudonymize.yml` in a subfolder `pseudonymization/examples/data-types` within any of the given blueprint paths. Sometimes you might have different versions of a blueprint installed. By default, Kodex will load the latest version it can find. If you don't want that, you can specify a version using the `--version` flag:

<!--translate:ignore-->
```
kodex run my-blueprint --version 0.4.1
```
<!--translate:ignore-->

Version numbers follow the [semantic version specification (2.0)](https://semver.org/lang/de/).

## Blueprint repositories

Kodex lets you download and install blueprints from so-called repositories. For example, to get our official blueprints repository, simply run

<!--translate:ignore-->
```
kodex blueprints download
```
<!--translate:ignore-->

This will download the latest snapshot of the master branch of our [Blueprints repository](https://github.com/kiprotect/blueprints) and install it into a local directory. If you want to download blueprints from a different URL simple provide it:

<!--translate:ignore-->
```
kodex blueprints download https://my.blueprints/repo.zip
```
<!--translate:ignore-->

Kodex will look for a directory with a `.blueprints.yml` file in the ZIP  archive and extract that directory to the main blueprints path. You can also create own local blueprint repositories of course, just make sure to put them in a subfolder of a blueprints path and create a `.blueprints.yml` file with the following content:

<!--translate:ignore-->
<div class="highlight">
    {%filter highlight(strip=True, language='yaml')%}
    package: [your-package-name] # e.g. my-blueprints
    version: [your version] # e.g. 1.5.2
    {%endfilter%}
</div>
<!--translate:ignore-->
