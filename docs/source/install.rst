==================
Install directions
==================

Build EPICS base
----------------

.. warning:: Make sure the disk partition hosting ~/epics is not larger than 2 TB. See `tech talk <https://epics.anl.gov/tech-talk/2017/msg00046.php>`_ and  `Diamond Data Storage <https://epics.anl.gov/meetings/2012-10/program/1023-A3_Diamond_Data_Storage.pdf>`_ document.

::

    $ mkdir ~/epics-sim
    $ cd epics-sim
    

- Download EPICS base latest release, i.e. 7.0.3.1., from https://github.com/epics-base/epics-base::

    $ git clone https://github.com/epics-base/epics-base.git
    $ cd epics-base
    $ git submodule init
    $ git submodule update
    $ make distclean (do this in case there was an OS update)
    $ make -sj
    
.. warning:: if you get a *configure/os/CONFIG.rhel9-x86_64.Common: No such file or directory* error issue this in your csh termimal: $ **setenv EPICS_HOST_ARCH linux-x86_64**

Build a minimal synApps
-----------------------

To build a minimal synApp::

    $ cd ~/epics-sim

- Download in ~/epics `assemble_synApps <https://github.com/EPICS-synApps/assemble_synApps/blob/18fff37055bb78bc40a87d3818777adda83c69f9/assemble_synApps>`_.sh
- Edit the assemble_synApps.sh script to include only::
    
    $modules{'ASYN'} = 'R4-44-2';
    $modules{'AUTOSAVE'} = 'R5-11';
    $modules{'BUSY'} = 'R1-7-4';
    $modules{'XXX'} = 'R6-3';

You can comment out all of the other modules (ALLENBRADLEY, ALIVE, etc.)

- Run::

    $ cd ~/epics-sim
    $ ./assemble_synApps.sh --dir=synApps --base=/home/beams/FAST/epics-sim/epics-base

- This will create a synApps/support directory::

    $ cd synApps/support/

- Clone the simepics module into synApps/support::
    
    $ git clone https://github.com/decarlof/simepics.git

- Edit configure/RELEASE add this line to the end::
    
    SIMEPICS=$(SUPPORT)/simepics

- Verify that synApps/support/simepics/configure/RELEASE::

    EPICS_BASE=/home/beams/FAST/epics-sim/epics-base
    SUPPORT=/home/beams/FAST/epics-sim/synApps/support

are set to the correct EPICS_BASE and SUPPORT directories and that::

    AUTOSAVE
    ASYN
    BUSY
    XXX

point to the version installed.

- Run the following commands::

    $ cd ~/epics-sim/synApps/support/
    $ make release
    $ make -sj

To run the simApp EPICS IOC server::

    $ cd ~/epics-sim/synApps/support/simepics/iocBoot/iocSimEpics
    $ ./start_medm
    $ ./start_IOC

Build the python server
-----------------------

To build the **simEpics** python server you need to have `Conda <https://docs.conda.io/en/latest/miniconda.html>`_
installed.

Next, create a dedicated conda environment for simEpics by running::

    (base) $ conda create --name simepics python=3.9

then::

    (base) $ conda activate simepics

and install the required python packages::

    (simepics) $ pip install pvapy
    (simepics) $ pip install pyepics

Finally you can build **simEpics** with::

    (simepics) $ cd ~/epics-sim/synApps/support/simpics/
    (simepics) $ pip install .

To run the python server::

    (simepics) $ cd ~/epics-sim/synApps/support/simpics/iocBoot/iocSimEpics
    (simepics) $ python -i start_simepics.py




