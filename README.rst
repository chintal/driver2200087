
driver2200087
=============

This is a Python Package to interface with Radio Shack's 2200087 Multimeter.

The 2200087 is an inexpensive DMM which supports logging and graphing data on
a computer, but the supplied code only supports Windows. This is a python package
to allow for connecting to the multimeter over USB on Linux or Mac OSX. Due to
radioshack not supplying any serial specifications, the protocol was reverse
engineered by David Dworken from simply observing the output of the DMM.

The ``serialDecoder`` module and the serial protocol documentation is essentially a
slightly tweaked version of the script written and maintained by David Dworken,
available at https://github.com/ddworken/2200087-Serial-Protocol.git

This package includes a version of the ``serialDecoder`` module, slightly refactored
to allow it to be imported into other python scripts. It also includes a ``runner``
module which contains a Twisted protocol, wrapped by Crochet. This module should
be relatively easier to include into other python scripts and applications.

Installation
------------

This package has been tested only with python 2.7.

This package can be installed from pypi using pip::

    $ pip install driver2200087

Or using easy_install (python 2.7 only)::

    $ easy_install driver2200087

Usage
-----

Standalone usage is listed `in the documentation <http://driver2200087.readthedocs.org/en/latest/basic.html#installation-and-usage>`_,
and should be fairly straightforward to follow.

The ``serialDecoder`` module can also be imported and used from within a python script,
and the `documentation <http://driver2200087.readthedocs.org/en/latest/driver2200087.html#module-driver2200087.serialDecoder>`_
can help you use it in that manner.

The recommended way for using the package, though, is through the ``runner`` module which
it provides. The simpler form of use is to get the latest available value whenever
necessary. A short example of how this can be done using this package in a typical python
application would be ::

    from driver2200087 import runner

    dmm = runner.InstInterface2200087()
    dmm.connect()

    # Other Application code
    # ...
    #
    # And when the measurement is required :

    if dmm.data_available() > 0:
        print dmm.latest_point()
    else:
        raise Exception # Or pass, or retry, as per application requirements

    # other application code
    # ...

If the application calls for continuous recording of the data, the following is likely a
better approach ::

    from driver2200087 import runner

    dmm = runner.InstInterface2200087()
    dmm.connect()
    while True:
        if dmm.data_available() > 0:
            print dmm.next_point()

Note that in this code snippet, the python interpreter is blocked by the
infinite while loop. This is not required by the module. As long as
dmm.next_point() is called often enough (10 Hz), you can use whatever mechanism
you like to actually make the call. Note that dmm.data_available() **must** be
checked by your application before making the call, or you should trap the
exception that results.

Making the call at less than this frequency will cause data points to be lost
when the point buffer fills up - if your application only calls for the
occasional measurement, you're probably better off with dmm.latest_point()
instead.

For an example of using the ``runner`` module from within a larger framework
by subclassing the provided twisted protocol, see `the corresponding Tendril module <https://github.com/chintal/tendril/blob/public/tendril/testing/instruments/RS2200087.py>`_.
The ``Tendril`` module, while WIP, also includes examples of parsing the obtained
datapoint strings into usable values.


Downloads and Documentation
---------------------------

The simplest way to obtain the source for this package is to clone the git repository::

    git clone https://github.com/chintal/driver2200087.git driver2200087

You can install it as usual, with::

    python setup.py install

The latest version of the documentation can be found at `ReadTheDocs <http://driver2200087.readthedocs.org/en/latest/index.html>`_.

License
-------

driver2200087 is distributed under the GPLv2 license.



