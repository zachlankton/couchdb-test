Apache CouchDB DEVELOPERS
=========================

Before you start here, read `INSTALL.Unix` (or `INSTALL.Windows`) and
follow the setup instructions including the installation of all the
listed dependencies for your system.

Only follow these instructions if you are building from a source checkout.

If you're unsure what this means, ignore this document.

Dependencies
------------

You need the following to run tests:

* `Python 3               <https://www.python.org/>`_
* `Elixir                 <https://elixir-lang.org/>`_

You need the following optionally to build documentation:

* `Sphinx                 <http://sphinx.pocoo.org/>`_
* `GNU help2man           <http://www.gnu.org/software/help2man/>`_
* `GnuPG                  <http://www.gnupg.org/>`_

You need the following optionally to build releases:

* `md5sum                 <http://www.microbrew.org/tools/md5sha1sum/>`_
* `sha1sum                <http://www.microbrew.org/tools/md5sha1sum/>`_

You need the following optionally to build Fauxton:

* `nodejs                 <http://nodejs.org/>`_
* `npm                    <https://www.npmjs.com/>`_               

You will need these optional dependencies installed if:

* You are working on the documentation, or
* You are preparing a distribution archive

However, you do not need them if:

* You are building from a distribution archive, or
* You don't care about building the documentation

If you intend to build Fauxton, you will also need to install its
dependencies. After running ``./configure`` to download all of the
dependent repositories, you can read about required dependencies in
`src/fauxton/readme.md`. Typically, installing npm and node.js are
sufficient to enable a Fauxton build.

Here is a list of *optional* dependencies for various operating systems.
Installation will be easiest, when you install them all.

Docker
~~~~~~

CouchDB maintains a ``Dockerfile`` based on Debian that includes all
the dependencies noted above in the `.devcontainer <https://github.com/apache/couchdb/tree/main/.devcontainer>`_
folder.

The ``Dockerfile`` can be used on its own, or together with the
associated ``devcontainer.json`` file to quickly provision a
development environment using `GitHub Codespaces <https://github.com/features/codespaces>`_
or `Visual Studio Code <https://code.visualstudio.com/docs/remote/containers>`_.


.. image:: https://img.shields.io/static/v1?label=Remote%20-%20Containers&message=Open&color=blue&logo=visualstudiocode
    :target: https://vscode.dev/redirect?url=vscode://ms-vscode-remote.remote-containers/cloneInVolume?url=https://github.com/zachlankton/couchdb-test

If you already have VS Code and Docker installed, you can click the badge above or 
`here <https://vscode.dev/redirect?url=vscode://ms-vscode-remote.remote-containers/cloneInVolume?url=https://github.com/zachlankton/couchdb-test>`_ 
to get started. Clicking these links will cause VS Code to automatically install the 
Remote - Containers extension if needed, clone the source code into a container volume, 
and spin up a dev container for use.

This ``devcontainer`` will automatically run ``./configure && make`` the first time it is created.  
While this may take some extra time to spin up, this tradeoff means you will be able to 
run things like ``./dev/run`` and ``make check`` straight away.  Subsequent startups should be quick.


Debian-based (inc. Ubuntu) Systems
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

    sudo apt-get install help2man python-sphinx gnupg nodejs npm \
         python3 python3-venv

Gentoo-based Systems
~~~~~~~~~~~~~~~~~~~~

::

    sudo emerge gnupg coreutils pkgconfig help2man sphinx python
    sudo pip install hypothesis requests nose

Centos 7 and RHEL 7
~~~~~~~~~~~~~~~~~~~

::

    sudo yum install help2man python-sphinx python-docutils \
        python-pygments gnupg nodejs npm


Mac OS X
~~~~~~~~

Install `Homebrew <https://github.com/mxcl/homebrew>`_, if you do not have 
it already.

Unless you want to install the optional dependencies, skip to the next section.

Install what else we can with Homebrew::

    brew install help2man gnupg md5sha1sum node python

If you don't already have pip installed, install it::

    sudo easy_install pip

Now, install the required Python packages::

    sudo pip install sphinx docutils pygments sphinx_rtd_theme

FreeBSD
~~~~~~~

::

    pkg install help2man gnupg py27-sphinx node
    pip install nose requests hypothesis

Windows
~~~~~~~

Follow the instructions in `INSTALL.Windows` and build all components from
source, using the same Visual C++ compiler and runtime.

Configuring
-----------

Configure the source by running::

    ./configure

If you intend to run the test suites::

    ./configure -c

If you don't want to build Fauxton or documentation specify
``--disable-fauxton`` and/or ``--disable-docs`` arguments for ``configure`` to
ignore their build and avoid any issues with their dependencies.

See ``./configure --help`` for more information.

Developing
----------

Formatting
~~~~~~~~~~

The ``erl`` files in ``src`` are formatted using erlfmt_. The checks are run
for every PR in the CI. To run the checks locally, run ``make erlfmt-check``.
To format the ``erl`` files in ``src``, run ``make erlfmt-format``.
To use ``erlfmt`` for specific files only, use the executable ``bin/erlfmt``
that is installed by ``configure``.

.. _erlfmt: https://github.com/WhatsApp/erlfmt

Testing
-------

To run all the tests use run::

    make check

You can also run each test suite individually via ``eunit`` and ``javascript``
targets::

    make eunit
    make javascript

If you need to run specific Erlang tests, you can pass special "options"
to make targets::

    # Run tests only for couch and chttpd apps
    make eunit apps=couch,chttpd

    # Run only tests from couch_btree_tests suite
    make eunit apps=couch suites=couch_btree

    # Run only only specific tests
    make eunit tests=btree_open_test,reductions_test

    # Ignore tests for specified apps
    make eunit skip_deps=couch_log,couch_epi

The ``apps``, ``suites``, ``tests`` and ``skip_deps`` could be combined in any 
way. These are mimics to ``rebar eunit`` arguments. If you're not satisfied by 
these, you can use EUNIT_OPT environment variable to specify exact `rebar eunit`
options::

    make eunit EUNIT_OPTS="apps=couch,chttpd"

JavaScript tests accepts only `suites` option, but in the same way::

    # Run all JavaScript tests
    make javascript

    # Run only basic and design_options tests
    make javascript suites="basic design_options"

    # Ignore specific test suites via command line
    make javascript ignore_js_suites="all_docs bulk_docs"

    # Ignore specific test suites in makefile
    ignore_js_suites=all_docs,bulk_docs

Note that tests on the command line are delimited here by whitespace,
not by comma.You can get list of all possible test targets with the
following command::

    make list-js-suites

Code analyzer could be run by::

    make dialyze

If you need to analyze only specific apps, you can specify them in familiar way
::

    make dialyze apps=couch,couch_epi

See ``make help`` for more info and useful commands.

Please report any problems to the developer's mailing list.

Releasing
---------

The release procedure is documented here::

    https://cwiki.apache.org/confluence/display/COUCHDB/Release+Procedure

Unix-like Systems
~~~~~~~~~~~~~~~~~

A release tarball can be built by running::

    make dist

An Erlang CouchDB release includes the full Erlang Run Time System and
all dependent applications necessary to run CouchDB, standalone. The
release created is completely relocatable on the file system, and is
the recommended way to distribute binaries of CouchDB. A release can be
built by running::

    make release

The release can then be found in the rel/couchdb directory.

Microsoft Windows
~~~~~~~~~~~~~~~~~

The release tarball and Erlang CouchDB release commands work on
Microsoft Windows the same as they do on Unix-like systems. To create
a full installer, the separate couchdb-glazier repository is required.
Full instructions are available in that repository's README file.

