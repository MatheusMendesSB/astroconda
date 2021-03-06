*********************
Compatibility Notices
*********************

As the Conda package ecosystem evolves and third-party software updates are released by Continuum and other providers, this may interfere with the stability of other codebases, such as STScI's software. This page will proactively chronicle such events as they occur as well as provide workarounds to these issues.

Problematic Conda Versions
==========================

These versions of the conda package and environment management tool itself are known to cause problems when installing Astroconda packages, including pipeline environments. They are to be avoided if at all possible. ``conda --version`` will display the version you have installed.

+----------------------------+
| Avoid use of Conda Version |
+============================+
| 4.1.6                      |
+----------------------------+

If you have an indicated version of conda installed you may want to upgrade to a newer version with

``conda update conda``

Once completed, check that the newly installed version is not indicated above as a problematic version. If it is indicated, you may have to downgrade to a previous version of conda in order to obtain one that has not been identified problematic.

``conda search conda``

will display a list of all versions of conda available. Select a version from the search list that does not appear in the table above and run

``conda install conda=<version>``.

-----------

If you spot a compatibility problem not listed here please let us know by sending an email to `help[at]stsci.edu`.

.. note::

  **You may be affected by an issue if you have updated your AstroConda environment on or after the dates listed in each section below.**


2018-01-24
==========

If you have installed or updated ``stsci``, ``stsci-hst``, or ``stwcs`` into a Python 2.7 environment, be aware, the release of ``stwcs`` (v1.4.0) is not compatible with Python 2.7.

``stwcs`` v1.4.0 is known to negatively impact the following packages:

* ``drizzlepac``
* ``stsci.skypac``

For anyone that has already installed ``stwcs`` (v1.4.0), the latest release of ``stsci-hst`` (v3.0.1) will automatically downgrade ``stwcs`` to v1.3.2. This downgrade behavior will not be observed in environments where the installed version of ``stwcs`` is less than v1.4.0.

As of 2018-02-01, the Python 2.7 release of ``stwcs`` v1.4.0 was removed from the AstroConda channel.

Am I affected?
--------------

Using ``find`` you can scan your Conda environments for ``stwcs`` v1.4.0. Be sure to replace ``/home/example/miniconda3`` with the path to your Anaconda or Miniconda installation. Depending on the number of environments installed and the seek time of your storage medium, this operation may take up to several minutes to complete.

.. code-block:: sh

    $ find /home/example/miniconda3/envs \
    -wholename '*/lib/python2.7/site-packages/stwcs/version.py' \
    | xargs grep --color -H "__version__.*'1.4.0'"

**If no matches are returned, then you are not affected by this release, and no action required.**

Otherwise, you may see results such as:

.. code-block:: sh

    ~/miniconda3/envs/astroconda27/lib/python2.7/site-packages/stwcs/version.py:__version__ = '1.4.0'
    ~/miniconda3/envs/iraf27/lib/python2.7/site-packages/stwcs/version.py:__version__ = '1.4.0'

In this example output, the ``astroconda27`` and ``iraf27`` Conda environments require your attention.

Repairing your Environment(s)
-----------------------------

Conda keeps a local cache of packages to help speed up installations and upgrades. In order to safeguard against reinstalling the incorrect version of ``stwcs`` it is recommended that you purge this cache.

This operation will not adversely affect your Conda installation. Packages will be redownloaded during the next installation or upgrade operation.

.. code-block:: sh

    $ conda clean --yes --index-cache --tarballs --packages

Update the ``stsci-hst`` package for each affected environment:

.. code-block:: sh

    $ conda update -n ENV_NAME_HERE stsci-hst

Or if you prefer to manually downgrade ``stwcs``, do this instead:

.. code-block:: sh

    $ conda install -n ENV_NAME_HERE stwcs=1.3.2


2017-05-24
==========

The method used to install IRAF via AstroConda has changed!

Before today, AstroConda's ``iraf`` package provided a full IRAF installation in a single tarball. Not only did this take a long time to install, it has proven to be problematic, because updating even one line of code required us to repackage >700MBs of data. As of now, IRAF has been split into several smaller packages (``iraf.[pkg]``) and controlled via a single meta-package: ``iraf-all``.  The new ``pyraf-all`` meta-package installs ``pyraf`` along with packages identified to require ``pyraf`` and ``iraf``. The installation section, :ref:`iraf_install`, has been updated to reflect the changes detailed below.

To install a new IRAF environment:

.. code-block:: sh

    $ conda create -n iraf27 python=2.7 iraf-all pyraf-all stsci
    $ source activate iraf27

If you plan to update an existing "iraf27" environment, do the following:

.. code-block:: sh

    $ conda install -n iraf27 iraf-all pyraf-all
    $ source activate iraf27

``conda update`` will not work as expected in this case, due to the fact that the ``iraf`` package no longer serves the same purpose. After ``iraf-all`` has been installed you may continue to use ``conda update --all`` to perform general updates.


2017-05-19
==========

Astropy removed yet another deprecated function in v2.0; namely ``io.fits.NumCode``.
This function was called by ``stpyfits``, which gets used by in the HST pipeline
as called by the ``drizzlepac`` to transparently handle ``_raw.fits`` HST data.
The deprecation is addressed in order to allow the next release to work in the
operational HST calibration pipeline build, HSTDP 2017.2
(see https://github.com/spacetelescope/stsci.tools/pull/36).

.. code-block:: python

    >>> sdq = stpyfits.getdata("j9ot10icq_raw.fits", extname="DQ", extver=1)
    WARNING: AstropyDeprecationWarning: The NumCode class attribute is deprecated and may be removed in a future version.
        Use the module level constant BITPIX2DTYPE instead. [astropy.utils.decorators]

2017-05-02
==========

A collection of errant release candidate packages were published to the AstroConda public channel (http://ssb.stsci.edu/astroconda) on Friday, Apr 28, 2017 around 3:45pm and remained available for download until 10:30am on Tuesday, May 2, 2017. If you updated/upgraded any of the following packages during that window, you may have retrieved and installed software which is unsuitable for use due to untested behavior.

The affected packages:

+--------------------+----------------------------+
|  Errant package    | <package>=<goodversion>    |
+====================+============================+
| calcos-3.2.1       | calcos=3.1.8               |
+--------------------+----------------------------+
| crds-7.1.1         | crds=7.1.0                 |
+--------------------+----------------------------+
| drizzlepac-2.1.14  | drizzlepac=2.1.13          |
+--------------------+----------------------------+
| hstcal-1.2.0rc1    | hstcal=1.1.1               |
+--------------------+----------------------------+
| stsci.tools-3.4.9  | stsci.tools=3.4.7          |
+--------------------+----------------------------+
| stwcs-1.3.2rc1     | stwcs=1.2.5                |
+--------------------+----------------------------+
| wfc3tools-1.3.5rc1 | wfc3tools=1.3.4            |
+--------------------+----------------------------+

If any of these errant packages appear in a ``conda list`` of your environment, please revert to the last known-good release version by issuing a ``conda install <package>=<goodversion>`` for each package.

We apologize for any inconvenience introduced by this unintended sofware release.


2017-02-11
==========

NumPy v1.12.0 modified the way array slicing is handled and triggered
a regression in the ``acstools`` and ``pysynphot`` packages:

 * ``acstools <= 2.0.6`` - 2.0.7 released (Feb 16, 2017)
 * ``pysynphot <= 0.9.8.5`` - 0.9.8.6 released (Feb 21, 2017)

One of the traceback messages to be aware of is as follows
(traceback may be worded differently but usually complains about
index not being an integer):

.. code-block:: python

    TypeError('slice indices must be integers or None or have an __index__ method',)

Recommended user actions:

  * Upgrade ``acstools`` to version 2.0.7 (i.e., ``conda update acstools``)
  * Upgrade ``pysynphot`` to version 0.9.8.6 (i.e., ``conda update pysynphot``)

Alternative user action:

  * Downgrade ``numpy`` to version 1.11 (i.e., ``conda install numpy=1.11``)


2016-12-23
==========

AstroPy v1.3 fully deprecated calls to ``astropy.io.fits.new_table``.
The following packages are known to be incompatible with this release:

  * ``calcos <= 3.1.8`` - 3.2.1 released (Jul 06, 2017)
  * ``costools <= 1.2.1`` - Bugfix pending
  * ``fitsblender <= 0.2.6`` - 0.3.0 released (Jan 17, 2017)

Recommended user actions:

  * Upgrade ``fitsblender`` to version 0.3.0 (i.e., ``conda update fitsblender``)

Alternative user actions:

  * Downgrade ``astropy`` to version 1.2.1 (i.e., ``conda install astropy=1.2.1``)


Future
======

A list of known deprecation warnings detected in regression tests managed by
STScI Science Software Branch is available
`here <http://ssb.stsci.edu/creature_report/>`_.
This list is refreshed daily from "dev" and "public" test results.

Drizzlepac
----------

These deprecation warnings have been fixed in ``drizzlepac`` 2.1.8,
which is now available in AstroConda:

* https://github.com/spacetelescope/drizzlepac/issues/14
* https://github.com/spacetelescope/drizzlepac/issues/15
* https://github.com/spacetelescope/drizzlepac/issues/16
* https://github.com/spacetelescope/drizzlepac/issues/17
* https://github.com/spacetelescope/drizzlepac/issues/21
* https://github.com/spacetelescope/drizzlepac/issues/27
