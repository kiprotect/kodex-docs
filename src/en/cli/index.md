# Command Line Interface (CLI)

The Kodex command line interface (CLI) is the easiest way to get started with privacy and security engineering. It enables you to run a wide range of privacy- & security-enhancing transformations as well as analyses on your structured data.

## Installing Kodex CLI

First, you need to download or build the Kodex CLI tool. You can download pre-built binaries for various platforms on our [website]({{'urls.download'|translate}}). Alternatively, you can build the tool from source by following the instructions on our [Github page](https://github.com/kiprotect/kodex).

## Getting started

By default, we control the CLI tool using so-called **blueprints**. A blueprint is a config file (or a collection of them) that describes how Kodex should read, analyze, transform and write structured data.

To run a blueprint, you simply execute `kodex run [blueprint name]`. Kodex comes with a free, public repository of example blueprints that help us to get started. We can download and install them via the command line as well:

<!--translate:ignore-->
```
kodex blueprints download
```
<!--translate:ignore-->

This will download our public [blueprints repository](https://github.com/kiprotect/blueprints) and store it in a local directory (by default `~/.kodex/blueprints`). You can then run any blueprint by simply specifying its path relative to the blueprints directory. So let's run a simple example that shows how Kodex can pseudonymize different data types:

<!--translate:ignore-->
```
kodex run pseudonymization/examples/data-types/pseudonymize
```
<!--translate:ignore-->

This will load the configuration from the blueprint file (`pseudonymize.yml`). This file specifies from where data should be read (a JSON file in this case), how the data should be transformed (using a pseudonymization in this case) and where the resulting output data should be sent to (a JSON file again).

<aside>
    The items in the <code>input.json</code> file that we want to pseudonymize.

<!--translate:ignore-->
    <div class="highlight">
        {%filter highlight(strip=True, language='json')%}
            {
            "name": "test",
            "date": "2020-06-04",
            "ip": "42.34.122.112/32",
            "count": 4354
            }
            {
            "name": "another test",
            "date": "2019-07-02",
            "ip": "42.34.122.114/32",
            "count": 214
            }
        {%endfilter%}
    </div>
<!--translate:ignore-->
</aside>

The example blueprint that we picked will read data items from an `input.json` file located in the same directory as the blueprint, pseudonymize all attributes of each item using different applicable pseudonymization methods, and write the pseudonymized data to a JSON file (`pseudonymized.json`) in the current directory. Here's what the output looks like:

<div class="highlight">
<!--translate:ignore-->
    {%filter highlight(strip=True, language='json')%}
    {
    "_kip": "278ba5f7db26ca661b4e64b1eb6abb3d4e7d1aa15e55155b3a1f7626424f679c",
    "count": 7643,
    "date": "2021-09-11",
    "ip": "150.39.196.226/32",
    "name": "3YdcJQ=="
    }
    {
    "_kip": "278ba5f7db26ca661b4e64b1eb6abb3d4e7d1aa15e55155b3a1f7626424f679c",
    "count": 46,
    "date": "2015-07-19",
    "ip": "150.39.196.8/32",
    "name": "gKGhSKShnHg/vny6"
    }
    {%endfilter%}
<!--translate:ignore-->
</div>

As you can see, Kodex pseudonymized every attribute in every data item and also added a new attribute, `_kip`, to the items. The value of that attribute refers to a parameter set that contains the cryptographic keys that were used to transform the data. The actual keys are stored in a so-called parameter store. If you don't want that, you can also manage keys yourself as well: The `pseudonymize-with-key` blueprint in the same directory does that by first asking you to enter a key and then using that key to derive further encryption keys for the individual pseudonymization operations. Key & parameter management is a complex topic in itself, for now just rest assured that Kodex takes care of the messy details for you.

### Depseudonymizing data

At some point you might actually want to depseudonymize your data again. Kodex makes this easy by providing an `undo` action that can be applied to reversible transformations like the cryptographic pseudonymization above. So, to depseudonymize the data above, we can simply run a blueprint that contains such an `undo` action:

<!--translate:ignore-->
```
kodex run pseudonymization/examples/data-types/depseudonymize
```
<!--translate:ignore-->

which will print the depseudonymized data (which should exactly match the input data). If you provided a pseudonymization key yourself by using the `pseudonymize-with-key`, you can run `depseudonymize-with-key` blueprint instead, which will ask you to enter the pseudonymization key. Isn't that easy?

## Next steps

Now that you know the basics of the Kodex CLI tool, you can dive more into the details. Here's what you can do:

* Browse the [action catalogue]({{'kodex.actions.index'|href}}) to learn about all the different privacy- & security-enhancing actions that Kodex offers.
* Look through the [blueprints repository](https://github.com/kiprotect/blueprints) to see how to tackle a specific problem with Kodex.
* Check out the [blueprints documentation]({{'kodex.blueprints'|href}}) to learn how to write your own privacy & security blueprints with Kodex.

## Questions?

Do you have questions regarding Kodex? You can do the following:

* [Open a Github <tr-ignore>Issue</tr-ignore>](https://github.com/kiprotect/kodex) to ask our awesome open-source community for help.
* [Write us an e-mail](mailto:cli@kiprotect.com) with your question.