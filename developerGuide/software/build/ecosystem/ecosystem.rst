.. _ecosystem:

##########################
Build Red Pitaya ecosystem
##########################

Go to the Red Pitaya (git) directory.

.. note::
   
   | It is recommended that you set ``$LC_ALL`` variable.
   | To check whether it is set, type the following command into a terminal:
   
   .. code-block:: shell-session
      
       echo $LC_ALL

   If it returns an empty line, set it up by typing the following command into the terminal:

   .. code-block:: shell-session
      
       export LC_ALL=C
   
   This line can also be added to the end of .bashrc and will automatically set the ``$LC_ALL`` variable each time the 
   the terminal is started.

   
.. note::
    
    It is not possible to build an ecosystem on an encrypted home directory since schroot can not access that 
    directory. We recommend that you make a separate directory in the home directory that is not encrypted e.g. 
    ``/home/ecosystem_build``

       
=====================================
Red Pitaya ecosystem and applications
=====================================

Here you will find the sources of various software components of the
Red Pitaya system. The components are mainly contained in dedicated
directories, however, due to the nature of the Xilinx SoC "All 
Programmable" paradigm and the way several components are interrelated,
some components might be spread across many directories or found at
different places one would expect.


+--------------+-------------------------------------------------------------------------------------------------------------------------------------------------+
| directories  | contents                                                                                                                                        |
+==============+=================================================================================================================================================+
| rp-api       | API source code for ``librp.so`` , ``librp2.so`` , ``librp-gpio.so`` , ``librp-i2c.so`` , ``librp-spi.so``, etc ...                             |
+--------------+-------------------------------------------------------------------------------------------------------------------------------------------------+
| apps-free    | WEB application for the old environment (also with controller modules & GUI clients)                                                            |
+--------------+-------------------------------------------------------------------------------------------------------------------------------------------------+
| apps-tools   | WEB interface home page and some system management applications                                                                                 |
+--------------+-------------------------------------------------------------------------------------------------------------------------------------------------+
| Bazaar       | Nginx server with dependencies, Bazaar module & application controller module loader                                                            |
+--------------+-------------------------------------------------------------------------------------------------------------------------------------------------+
| fpga         | FPGA design (RTL, bench, simulation, and synthesis scripts) SystemVerilog based for newer applications                                          |
+--------------+-------------------------------------------------------------------------------------------------------------------------------------------------+
| OS           | GNU/Linux operating system components                                                                                                           |
+--------------+-------------------------------------------------------------------------------------------------------------------------------------------------+
| patches      | Directory containing patches                                                                                                                    |
+--------------+-------------------------------------------------------------------------------------------------------------------------------------------------+
| scpi-server  | SCPI server                                                                                                                                     |
+--------------+-------------------------------------------------------------------------------------------------------------------------------------------------+
| Test         | Command line utilities (acquire, generate, ...), tests                                                                                          |
+--------------+-------------------------------------------------------------------------------------------------------------------------------------------------+
| Examples     | Examples in different programming languages for working with peripherals                                                                        |
+--------------+-------------------------------------------------------------------------------------------------------------------------------------------------+
| build_scripts| Scripts for building an ecosystem and preparing an image for writing to a memory card                                                           |
+--------------+-------------------------------------------------------------------------------------------------------------------------------------------------+

|

-------------------
Supported platforms
-------------------

Red Pitaya is developed on Linux (64-bit Ubuntu 18.04),
so Linux is also the only platform we support.

.. note::

   Ecosystem version 2.0 or higher requires Ubuntu version 22.04


.. _sys-req-label:

---------------------
Software requirements
---------------------

You will need the following to build the Red Pitaya components:

1. Various development packages.

   .. code-block:: shell-session

      # generic dependencies
      sudo apt-get install make curl xz-utils
      # U-Boot build dependencies
      sudo apt-get install libssl-dev device-tree-compiler u-boot-tools
      # secure chroot
      sudo apt-get install schroot
      # QEMU
      sudo apt-get install qemu qemu-user qemu-user-static
      #32-bit libraries
      sudo apt-get install lib32z1 lib32ncurses5 libbz2-1.0:i386 lib32stdc++6

|

2. Meson Build system (depends on Python 3) is used for some new code.
   It is not required but can be used during development on an x86 PC.

   .. code-block:: shell-session

      sudo apt-get install python3 python3-pip
      sudo pip3 install --upgrade pip
      sudo pip3 install meson
      sudo apt-get install ninja-build

|

3. Xilinx `Vivado 2020.1 <http://www.xilinx.com/support/download.html>`_ FPGA development tools.
   The SDK (bare metal toolchain) must also be installed, be careful during the installation process to select it.
   Preferably use the default install location.

   If you want to run Vivado from a virtual machine and Vivado is installed on a host shared
   folder, then we suggest you use VirtualBox since VMware has a bug in VMware-tools
   for Ubuntu guests and can not mount vmhgfs shared file system type.

   Then install Ubuntu 18.04 in VirtualBox (NOTE: don't use encrypt installation, 
   since it blocks some Red Pitaya build procedures).

   After successfully installation, change the settings for Ubuntu virtual machine.
   Go to the Shared Folders menu and choose the Xilinx installation directory on the host machine
   (by default is under /opt/ directory). And choose the Auto-mount option (checkbox).

   Then you must install (on Ubuntu guest) a package dkms.

   .. code-block:: shell-session

      $ sudo apt-get install virtualbox.guest-dkms

   After rebooting Ubuntu guest, you can access (with superuser/root privileges) Xilinx shared
   folder under /media/sf_Xilinx subdirectory.

   Now you can manage this system to meet your requirements.

   .. note::

      Ecosystem version 2.0 requires Vivado version 2020.1 and SDK 2019.1

|

4. Missing ``gmake`` path

   Vivado requires a ``gmake`` executable that does not exist on Ubuntu. It is necessary to create a symbolic link to the regular ``make`` executable.

   .. code-block:: shell-session

      $ sudo ln -s /usr/bin/make /usr/bin/gmake


.. _build-proc-label:

=============
Build process
=============

.. note::

   To implement the build process, at least 8 GB of available space on the PC's local machine is required.


.. tabs::

   .. group-tab:: Ecosystem 1.04

      **1.** Go to your preferred development directory and clone the Red Pitaya repository from GitHub.
      The choice of specific branches or tags is up to the user.

      .. code-block:: shell-session

         git clone https://github.com/RedPitaya/RedPitaya.git
         cd RedPitaya


      .. note:: 

         You can run a script that builds the ecosystem from the build_scripts folder. |br|
         To build an ecosystem for boards 125-14:

         .. code-block:: shell-session

            cd ./RedPitaya/build_scripts
            sudo ./build_Z10.sh

         To build an ecosystem for board 125-14 (Z7020):

         .. code-block:: shell-session
         
            cd ./RedPitaya/build_scripts
            sudo ./build_Z20_125.sh

         To build an ecosystem for board 125-14 4-Input (Z7020):

         .. code-block:: shell-session
         
            cd ./RedPitaya/build_scripts
            sudo ./build_Z20_4CH.sh

         To build an ecosystem for boards 122-16:

         .. code-block:: shell-session
         
            cd ./RedPitaya/build_scripts
            sudo ./build_Z20.sh

         To build an ecosystem for board 250-12:
         
         .. code-block:: shell-session
         
            cd ./RedPitaya/build_scripts
            sudo ./build_Z250_12.sh   

         or follow the steps of the instructions and build yourself
         
      |

      **2.**  An example script ``settings.sh`` is provided for setting all necessary environment variables.
      The script assumes some default tool install paths, so it might need editing if install paths other than the ones described above were used.

      .. code-block:: shell-session

         settings.sh

      |

      **3.** Prepare a download cache for various source tarballs.
      This is an optional step that will speed up the build process by avoiding downloads for all but the first build.
      There is a default cache path defined in the ``settings.sh`` script, you can edit it and avoid a rebuild the next time.

      .. code-block:: shell-session

         mkdir -p dl
         export DL=$PWD/dl

      |

      **4.** Download the ARM Ubuntu root environment (usually the latest) from Red Pitaya download servers.
      You can also create your root environment following the instructions in :ref:`OS image build instructions <os>`.
      Correct file permissions are required for ``schroot`` to work properly.

      .. code-block:: shell-session

         wget https://downloads.redpitaya.com/downloads/LinuxOS/redpitaya_ubuntu_04-oct-2021.tar.gz
         sudo chown root:root redpitaya_ubuntu_04-oct-2021.tar.gz
         sudo chmod 664 redpitaya_ubuntu_04-oct-2021.tar.gz

      |

      **5.** Create schroot configuration file ``/etc/schroot/chroot.d/red-pitaya-ubuntu.conf``.
      Replace the tarball path stub with the absolute path of the previously downloaded image.
      Replace user names with a comma-separated list of users who should be able to compile Red Pitaya.

      .. code-block:: none

         [red-pitaya-ubuntu]
         description=Red Pitaya Debian/Ubuntu OS image
         type=file
         file=absolute-path-to-red-pitaya-ubuntu.tar.gz
         users=comma-separated-list-of-users-with-access-permissions
         root-users=comma-separated-list-of-users-with-root-access-permissions
         root-groups=root
         profile=desktop
         personality=linux
         preserve-environment=true

      .. note::

         Example of configuration file:

         .. code-block:: shell-session
         
            [red-pitaya-ubuntu]
            description= Red pitaya
            type=file
            file=/home/user/RedPitaya/redpitaya_ubuntu_04-oct-2021.tar.gz
            users=root
            root-users=root
            root-groups=root
            personality=linux
            preserve-environment=true

      |

      **6.** To build everything a few ``make`` steps are required.

      .. code-block:: shell-session

         make -f Makefile.x86
         schroot -c red-pitaya-ubuntu <<- EOL_CHROOT
         make
         EOL_CHROOT
         make -f Makefile.x86 zip

      |

      **7.** If you want to build for 122-16 based on Z7020 Xilinx, you must pass parameter FPGA MODEL=Z20 in the makefile
      This parameter defines how to create projects and should be transferred to all makefiles.

      .. code-block:: shell-session

         make -f Makefile.x86 MODEL=Z20
         schroot -c red-pitaya-ubuntu <<- EOL_CHROOT
         make MODEL=Z20
         EOL_CHROOT
         make -f Makefile.x86 zip MODEL=Z20

      |

      **8.** If you want to build for 125-14 4-Input based on Z7020 Xilinx, you must pass parameter FPGA MODEL=Z20_125_4CH in makefile
      This parameter defines how to create projects and should be transferred to all makefiles.

      .. code-block:: shell-session

         make -f Makefile.x86 MODEL=Z20_125_4CH
         schroot -c red-pitaya-ubuntu <<- EOL_CHROOT
         make MODEL=Z20_125_4CH
         EOL_CHROOT
         make -f Makefile.x86 zip MODEL=Z20_125_4CH

      |

      **9.** If you want to build for 250-12 based on Z7020 Xilinx, you must pass parameter FPGA MODEL=Z20_250_12 in the makefile
      This parameter defines how to create projects and should be transferred to all makefiles.

      .. code-block:: shell-session

         make -f Makefile.x86 MODEL=Z20_250_12
         schroot -c red-pitaya-ubuntu <<- EOL_CHROOT
         make MODEL=Z20_250_12
         EOL_CHROOT
         make -f Makefile.x86 zip MODEL=Z20_250_12

      |

      To get an interactive ARM shell do.

      .. code-block:: shell-session

         schroot -c red-pitaya-ubuntu
   
   .. group-tab:: Ecosystem 2.00 and up

      **1.** Go to your preferred development directory and clone the Red Pitaya repository from GitHub.
      The choice of specific branches or tags is up to the user.

      .. code-block:: shell-session

         git clone https://github.com/RedPitaya/RedPitaya.git
         cd RedPitaya

      .. note:: 

         You can run a script that builds the ecosystem from the build_scripts folder. |br|
         To build an ecosystem for all boards:

         .. code-block:: shell-session

            cd ./RedPitaya/build_scripts
            sudo ./build_OS.sh

         or follow the steps of the instructions and build yourself
         
      |

      **2.**  An example script ``settings.sh`` is provided for setting all necessary environment variables.
      The script assumes some default tool install paths, so it might need editing if install paths other than the ones described above were used.

      .. code-block:: shell-session

         settings.sh

      |

      **3.** Prepare a download cache for various source tarballs.
      This is an optional step that will speed up the build process by avoiding downloads for all but the first build.
      There is a default cache path defined in the ``settings.sh`` script, you can edit it and avoid a rebuild the next time.

      .. code-block:: shell-session

         mkdir -p dl
         export DL=$PWD/dl

      |

      **4.** Download the ARM Ubuntu root environment (usually the latest) from Red Pitaya download servers.
      You can also create your root environment following the instructions in :ref:`OS image build instructions <os>`.
      Correct file permissions are required for ``schroot`` to work properly.

      .. code-block:: shell-session

         wget https://downloads.redpitaya.com/downloads/LinuxOS/redpitaya_OS_16-03-48_03-Nov-2022.tar.gz
         sudo chown root:root redpitaya_OS_16-03-48_03-Nov-2022.tar.gz
         sudo chmod 664 redpitaya_OS_16-03-48_03-Nov-2022.tar.gz

      |

      **5.** Create schroot configuration file ``/etc/schroot/chroot.d/red-pitaya-ubuntu.conf``.
      Replace the tarball path stub with the absolute path of the previously downloaded image.
      Replace user names with a comma-separated list of users who should be able to compile Red Pitaya.

      .. code-block:: none

         [red-pitaya-ubuntu]
         description=Red Pitaya Debian/Ubuntu OS image
         type=file
         file=absolute-path-to-red-pitaya-ubuntu.tar.gz
         users=comma-separated-list-of-users-with-access-permissions
         root-users=comma-separated-list-of-users-with-root-access-permissions
         root-groups=root
         profile=desktop
         personality=linux
         preserve-environment=true

      .. note::

         Example of configuration file:

         .. code-block:: shell-session
         
            [red-pitaya-ubuntu]
            description= Red pitaya
            type=file
            file=/home/user/RedPitaya/redpitaya_OS_16-03-48_03-Nov-2022.tar.gz
            users=root
            root-users=root
            root-groups=root
            personality=linux
            preserve-environment=true
       
      |

      **6.** To build everything a few ``make`` steps are required.

      .. code-block:: shell-session

         make -f Makefile.x86
         schroot -c red-pitaya-ubuntu <<- EOL_CHROOT
         make
         EOL_CHROOT
         make -f Makefile.x86 zip

      |

      To get an interactive ARM shell do.

      .. code-block:: shell-session

         schroot -c red-pitaya-ubuntu

      .. note::

         Ecosystem Build 2.0 cannot build for a specific board model as it was in version 1.04. Differences only in the assembly of FPGA for specific models.


=======================
Partial rebuild process
=======================

.. tabs::

   .. group-tab:: Ecosystem 1.04

      The next components can be built separately.
      By default, the project is built for RedPitaya 125-14 (Z7010), if necessary build for the (RedPitaya 122-16) Z7020, use the parameter MODEL=Z20 and parameter MODEL=Z20_250_12 for RedPitaya (250-12) Z7020.

      * FPGA + device tree
      * u-Boot
      * Linux kernel
      * Debian/Ubuntu OS
      * API
      * SCPI server
      * free applications

      |

      **Base system**

      Here the *base system* represents everything before Linux user space.

      To be able to compile FPGA and cross-compile *base system* software, it is necessary to set up the Vivado FPGA tools and ARM SDK.


      .. code-block:: shell-session

         $ . settings.sh

      On some systems (including Ubuntu 18.04) the library setup provided by Vivado conflicts with default system libraries.
      To avoid this, disable library overrides specified by Vivado.

      .. code-block:: shell-session

         $ export LD_LIBRARY_PATH=""

      Once an ecosystem is built, it can be packaged into an archive for ease of use.


      .. code-block:: shell-session

         $ make -f Makefile.x86 zip

      |

      ***FPGA and device tree sources***


      .. code-block:: shell-session

         $ make -f Makefile.x86 fpga

      Detailed instructions are provided for :ref:`building the FPGA <buildprocess>`
      including some :ref:`device tree details <devicetree>`.

      **Device Tree compiler + overlay patches**

      Download the Device Tree compiler with overlay patches from Pantelis Antoniou.
      Compile and install it.
      Otherwise, a binary is available in ``tools/dtc``.

      .. code-block:: shell-session

         $ sudo apt-get install flex bison
         $ git clone git@github.com:pantoniou/dtc.git
         $ cd dtc
         $ git checkout overlays
         $ make
         $ sudo make install PREFIX=/usr

      |

      ***U-boot***

      To build the U-Boot binary and boot scripts (used to select between booting into Buildroot or Debian/Ubuntu):

      .. code-block:: shell-session

         make -f Makefile.x86 u-boot

      The build process downloads the Xilinx version of U-Boot sources from Github, applies patches, and starts the build process.
      Patches are available in the ``patches/`` directory.

      |

      ***Linux kernel and device tree binaries***

      To build a Linux image:

      .. code-block:: shell-session

         make -f Makefile.x86 linux
         make -f Makefile.x86 linux-install
         make -f Makefile.x86 devicetree
         make -f Makefile.x86 devicetree-install

      The build process downloads the Xilinx version of Linux sources from Github, applies patches, and starts the build process.
      Patches are available in the ``patches/`` directory.

      |

      ***Boot file***

      The created boot file contains FSBL, FPGA bitstream, and U-Boot binary.

      .. code-block:: shell-session

         make -f Makefile.x86 boot
   
   .. group-tab:: Ecosystem 2.00 and up

      The next components can be built separately.

      * FPGA + overlays     
      * u-Boot
      * Linux kernel
      * API
      * SCPI server
      * Console tools and web app

      |

      **Base system**

      Here the *base system* represents everything before Linux user space.

      To be able to compile FPGA and cross-compile *base system* software, it is necessary to set up the Vivado FPGA tools and ARM SDK.


      .. code-block:: shell-session

         ./settings.sh
         export CROSS_COMPILE=arm-linux-gnueabihf-
         export ARCH=arm
         export PATH=$PATH:/opt/Xilinx/Xilinx/Vivado/2020.1/bin
         export PATH=$PATH:/opt/Xilinx/SDK/2019.1/bin
         export PATH=$PATH:/opt/Xilinx/SDK/2019.1/gnu/aarch32/lin/gcc-arm-linux-gnueabi/bin/


      Once an ecosystem is built, it can be packaged into an archive for ease of use.


      .. code-block:: shell-session

         $ make -f Makefile.x86 zip

      |

      ***FPGA and overlays***

      Each FPGA version uses its overlay with the devices necessary to work with FPGA. Previously, the device tree was fixed for a specific FPGA version and board. |br|
      For each board, you need to call the assembly with the board version parameters. But to speed up the build, you can skip the unnecessary version.

      .. code-block:: shell-session

         make -f Makefile.x86 fpga MODEL=Z10
         make -f Makefile.x86 fpga MODEL=Z20
         make -f Makefile.x86 fpga MODEL=Z20_125
         make -f Makefile.x86 fpga MODEL=Z20_125_4CH
         make -f Makefile.x86 fpga MODEL=Z20_250_12

      Detailed instructions are provided for :ref:`building the FPGA <buildprocess>`

      |

      ***U-boot*** 

      To build the U-Boot binary and boot scripts:

      .. code-block:: shell-session

         make -f Makefile.x86 boot

      The build process downloads the Xilinx version of U-Boot sources from Github, applies patches, and starts the build process.
      Patches are available in the ``patches/`` directory.

      .. note::

         The script builds two versions of boot.bin files. One version is for boards with 512 MB RAM, the second version is for boards with 1 GB of RAM. There are also two versions of the Linux kernel boot scripts.

      .. note::
         
         The device tree for ``uboot`` is built using prepared files located in the `dts_uboot` folder. The device tree defines the minimum requirements for peripherals in order for the board to start.

      |

      ***Linux kernel and device tree binaries***

      To build a Linux image:

      .. code-block:: shell-session

         make -f Makefile.x86 linux
         make -f Makefile.x86 devicetree

      The build process downloads the Xilinx version of Linux sources from Github, applies patches, and starts the build process.
      Patches are available in the ``patches/`` directory.

      .. note:: 

         To build device trees, you must first build the necessary FPGA projects for the required boards. Since dtb and dts files are built based on FPGA `barebone` projects.

      |

      ***API + SCPI server + Web Applications***

      You can build separately each of the projects. The build requires a Linux image, see :ref:`Build process <build-proc-label>`.
      Use cases are shown below.:

      .. code-block:: shell-session

         schroot -c red-pitaya-ubuntu <<- EOL_CHROOT
         make api
         make nginx
         make scpi
         make sdr
         make bode
         make monitor
         make generator
         make acquire
         make calib
         make daisy_tool
         make spectrum
         make led_control
         make ecosystem
         make updater
         make main_menu
         make scpi_manager
         make streaming_manager
         make calib_app
         make network_manager
         make jupyter_manager
         EOL_CHROOT

      .. note::

         Possible options for individual assemblies are listed. Some of them depend on each other. You can build everything at once if you start the build with `make all`.


----------------
Linux user space
----------------

Debian/Ubuntu OS
~~~~~~~~~~~~~~~~

`Debian/Ubuntu OS instructions <https://github.com/RedPitaya/RedPitaya/tree/master/OS/debian>`_ are detailed elsewhere.


API
~~~

To compile the API run:

.. code-block:: shell-session

   schroot -c red-pitaya-ubuntu <<- EOL_CHROOT
   make api
   EOL_CHROOT

The output of this process is the Red Pitaya ``librp.so`` library in ``api/lib`` directory.
The header file for the API is ``redpitaya/rp.h`` and can be found in ``api/includes``.
You can install it on Red Pitaya by copying it there:

.. code-block:: shell-session

   scp build/api/lib/*.so root@192.168.0.100:/opt/redpitaya/lib/


SCPI server
~~~~~~~~~~~

Scpi server README can be found :download:`here <https://github.com/RedPitaya/RedPitaya/blob/master/scpi-server/README.md>`.

To compile the server run:

.. code-block:: shell-session

   schroot -c red-pitaya-ubuntu <<- EOL_CHROOT
   make scpi
   EOL_CHROOT

The compiled executable is ``scpi-server/scpi-server``.
You can install it on Red Pitaya by copying it there:

.. code-block:: shell-session

   scp scpi-server/scpi-server root@192.168.0.100:/opt/redpitaya/bin/

.. note::

   To build the scpi server for RP, a special `version <https://github.com/RedPitaya/scpi-parser/tree/redpitaya>`_  of scpi-parser is used. It added and optimized some functions.


Free applications
~~~~~~~~~~~~~~~~~

To build free applications, follow the instructions given :download:`here <https://github.com/RedPitaya/RedPitaya/blob/master/apps-free/README.md>`.



