===========
typo3-build
===========

Encapsulates build targets in order to build a TYPO3.CMS project.

**Note**: these steps are very custom but necessary in order to have a
clean composer structure within a TYPO3.CMS project.

But this is another story... For now, this is just a container in order
to clean up my projects a bit.

Installation
------------

Composer
~~~~~~~~

Edit your composer.json and add the following line to your `require-dev`
section:

.. code:: json

   {
     "require-dev": {
       "dreadlabs/typo3-build": "dev-master"
     }
   }

Buildfile
~~~~~~~~~

Now fire up your favourite text editor and add a `build.xml` file to your
projects root directory and add the following content:

.. code:: xml

   <?xml version="1.0" encoding="UTF-8"?>
   <project name="example" default="run">

     <import file="./vendor/dreadlabs/typo3-build/build.xml" />

     <target name="run">
       <phingcall target="build.main:run" />
     </target>
   </project>

Please note, the `name` attribute of the `project` root element MUST NOT be set
to ''build'' as this namespace is allocated by this package already.

Properties
~~~~~~~~~~

In your project directory, you have to create a `build.[stage].properties` file. The
**stage** value can be later addressed by specifying the `environment` property while
executing the build targets.

As a minimum, the following properties must be set:

.. code:: ini

   distDir=${application.startdir}/www

Secret properties
.................

To protect some sensitive data of the public eye a build properties file must be
created in the project's root directory: ''build.[stage].secret.properties''. This is
the template if you want to use the shipped build scripts:

.. code:: ini

   ssh.host=
   ssh.user=
   ssh.port=
   remote.path=

   database.name=
   database.host=
   database.password=
   database.port=
   database.username=

   basic_auth.user=
   basic_auth.password=

Usage
-----

Build
~~~~~

.. code:: sh

   ~ $ cd /vagrant
   ~ $ php vendor/bin/phing -Denvironment=[dev|prod]

Syncing
~~~~~~~

Currently the sync process supports downloading the database (without re-import
on the local machine) and downloading files out of `fileadmin/`.

**To fetch the database from the `prod` remote host**:

.. code:: sh

   ~ $ cd /vagrant
   ~ $ php vendor/bin/phing sync:db -Denvironment=prod


**To fetch the files (fileadmin/) from the `test` remote host**:

.. code:: sh

   ~ $ cd /vagrant
   ~ $ php vendor/bin/phing sync:files -Denvironment=test

QA
~~

The QA toolchain contains the following tools:

- PHP Mess Detector
- PHP_Depend
- PHPLOC
- PHP Copy/Paste Detector
- PHP_CodeSniffer
- PHPUnit

.. code:: sh

   ~ $ cd /vagrant
   ~ $ php vendor/bin/phing qa:run

Hooks
~~~~~

Currently, the following hooks are implemented.

typo3.copy-extensions.project.preCopyDist
.........................................

This hook is executed right before copying the project extensions form src/ to
${distDir}/typo3conf/ext/.

Use this, to run `grunt` or other things which should treat your project
extension before distribution.

Usage example
.............

In your project `build.xml` you have to define the hook-target mappings as properties
and add your custom task:

.. code:: xml

   <property name="typo3.copy-extensions.project.preCopyDist" value="runGrunt" />

   <target name="runGrunt">
      <exec
            command="/usr/bin/env grunt build"
            passthru="true"
            checkreturn="true" />
   </target>

License
-------

GPL-2.0