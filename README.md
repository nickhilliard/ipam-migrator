ipam-migrator
=============

IPAM data migration tool for between phpIPAM and NetBox. Currently supports reading from phpIPAM and writing to NetBox.


Prerequisites
-------------

The following software packages are required to use the `Makefile` features:

* **make**
* **pylint**
* **pip**

The following Python modules are required to use `setup.py` to install ipam-migrator:

* **setuptools**

The following software packages are required to run ipam-migrator:

* **Python**, version **3.4** or later
* **requests**


Installation
------------

ipam-migrator can be installed to the default location by simply using:

    sudo make install

By default, this will install the executables into `/usr/local/sbin`.

See the `Makefile` for more details on how to change the installation locations.

phpIPAM Configuration
---------------------

1. Enable the phpIPAM API: Administration | phpIPAM settings | API = ON
2. Enable pretty links: Administration | phpIPAM settings | Prettify links = Yes
3. Ensure that Apache mod_rewrite is set up so that the pretty links work (`AllowOverride all`)
4. Add a phpIPAM API Key: Administration | API | Create API key
  * The App ID is used as a reference in the input endpoint URL, so set it
    to something short and relevant, e.g. `ipamexport`
  * Set App security to be `SSL with App code token`
5. Ensure that http basic auth is disabled entirely for phpIPAM. If basic auth is configured in Apache, then ipam-migrator will not work.
6. Create a new username for API which has read access to all objects

Running
-------

Once ipam-migrator is installed and configured, it can be executed by simply running the `ipam-migrator` command if it is located in the `PATH` environment variable, or by running the executable directly if it is not.

The command `ipam-migrator --help` documents the optional arguments which can be used.

```
usage: ipam-migrator [-h] [-l FILE] [-ll LEVEL] [-iasv | -naisv]
                     [-oasv | -noasv]
                     INPUT-API-ENDPOINT,TYPE,AUTH-METHOD,KEY|TOKEN|USER,PASSWORD
                     [OUTPUT-API-ENDPOINT,TYPE,AUTH-METHOD,KEY|TOKEN|USER,PASSWORD]

Transfer IPAM information between two (possibly differing) systems

positional arguments:
  INPUT-API-ENDPOINT,TYPE,AUTH-METHOD,KEY|TOKEN|(USER,PASSWORD)
                        input database API endpoint, type, authentication
                        method and required information
  OUTPUT-API-ENDPOINT,TYPE,AUTH-METHOD,KEY|TOKEN|(USER,PASSWORD)
                        output database API endpoint, type, authentication
                        method and required information

optional arguments:
  -h, --help            show this help message and exit
  -l FILE, --log FILE   log output to FILE
  -ll LEVEL, --log-level LEVEL
                        use LEVEL as the logging level parameter
  -iasv, --input-api-ssl-verify
                        verify the input API endpoint SSL certificate
                        (default)
  -naisv, --no-input-api-ssl-verify
                        do NOT verify the input API endpoint SSL certificate
  -oasv, --output-api-ssl-verify
                        verify the output API endpoint SSL certificate
                        (default)
  -noasv, --no-output-api-ssl-verify
                        do NOT verify the output API endpoint SSL certificate
```

Specifying both an input and output API endpoint will migrate all data from the input to the output. Specifying just an input API endpoint will make ipam-migrator read all data from the input and output it to the logger, which is useful for verifying that the information being migrated to an output is correct before actually sending it.

For input from phpIPAM, assuming that phpIPAM is installed at https://www.example.com/phpipam/, and the App ID configured in phpIPAM is set to be `ipamexport`, use the following values:
  * INPUT-API-ENDPOINT: https://www.example.com/phpipam/api/ipamexport/
  * TYPE: `phpipam`
  * AUTH-METHOD: `login`
  * USER: the username that you created for this application
  * PASSWORD: the password for this username

A sample command line to test this configuration with username `phpipamapi` might look like this:

```
ipam-migrator --level DEBUG https://www.example.com/phpipam/api/ipamexport,phpipam,login,phpipamapi,mysecur3passw0rd
```
