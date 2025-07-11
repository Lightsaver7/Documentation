.. _multiboard_sync:

Multiboard Synchronisation
############################

Multiboard synchronisation is a feature that allows you to synchronise clock and trigger signals across multiple Red Pitaya boards to achieve a higher number of fast analog channels for acquisition or generation.

There are two ways to achieve clock and trigger synchronisation across multiple Red Pitaya boards:

1. **Red Pitaya Click Shield Synchronisation** - multiple *External clock* Red Pitaya boards synchronised with Click Shields. Minimal clock and trigger delay and jitter.
#. **Red Pitaya X-Channel system** - one primary and multiple secondary Red Pitaya boards connected into a daisy chain with SATA (Gen 1) or USB-C (Gen 2) cables. Clock and trigger edges degrade slightly with each additional board in the chain.

The synchronized boards can be controlled as any normal Red Pitaya board. However, remote control from a computer is usually preferred to as it allows for more flexibility and easier control of multiple boards from the same program.

For direct comparison of the two systems, please see the :ref:`Q&A section <faq_multiboard>`.

.. note::

    Multichannel generation is currently still in development and is not yet fully supported.



How can I control synchronised boards?
======================================

* :ref:`SCPI commands <scpi_commands>`.
* :ref:`C & Python API commands <API_commands>` (harder to configure as a program must be exectured on each board separately).
* :ref:`Streaming application <streaming_top>` (either :ref:`Desktop client application <stream_desktop_app>` or :ref:`command line client <stream_command_client>`).

.. note::

    Any restrictions or limitations of applications and control methods also apply to multiboard synchronisation. However, these should be interpreted **per board** and not for the full system. For example, the Streaming application has an input data limitation of 20 MB/s per board. Therefore, if you have three boards in the system, the total input data rate is 60 MB/s (20 MB/s per board).



.. _click_shield_sync:

Click Shield Synchronisation
=============================

Red Pitaya Click Shields enable high-performance clock and trigger synchronisation between multiple Red Pitaya units or other devices.

.. note::

    The clock and trigger synchronisation with Click Shields is available only with Red Pitaya board models that can accept an external clock (External clock models). Please see the :ref:`Click Shield compatibility section <click_shield_compatibility>` for more information.


Setup
-------

The :ref:`Red Pitaya Click Shields <click_shield>` can synchronise multiple external clock Red Pitaya units together. As U.FL cables are used for clock and trigger synchronisation, other external clock devices can also be included in the chain.
The connection provides minimal clock signal delay between multiple Red Pitaya units, as there is only a single ZL40213 LVDS clock fanout buffer between two units.

To synchronise two or more external clock Red Pitaya units, establish the following connections with U.FL cables between the primary board (transmitting clock and trigger signals) and the secondary board (receiving the clock and trigger signals). Use one of the two schemes depending on whether you want to connect an external clock or use the oscillator on the Red Pitaya Click Shields.


Oscillator
~~~~~~~~~~~~

.. figure:: img/Click_Shield_Oscillator_Sync.png
    :width: 700
    :align: center

When using the oscillator, the first Red Pitaya Click Shield transmits the clock and trigger signals to all devices in the chain. Here are the most important things to check:

**Primary board:**

* Jumpers J4 and J5 connected. Connect the oscillator to the clocking transmission line.
* Jumpers J6 and J7 connected. Connect the Red Pitaya trigger to the trigger transmission line.
* Jumper J1 disconnected (unless using a single wire clock).
* CLK OSC switch in ON position.
* CLK SELECT switch in EXT position.

**Secondary board:**

* Jumper J6 connected. Connect the trigger to the Ext. trigger pin.
* Jumper J1 disconnected (unless using a single wire clock).
* CLK OSC switch in OFF position.
* CLK SELECT switch in EXT position.

If an external trigger signal is used, copy the secondary board's trigger connections to the primary board (disconnect J7 and connect the external trigger U.FL cable). 
Otherwise, DIO0_N acts as external trigger output (on the primary board), and DIO0_P acts as external trigger input.


External Clock
~~~~~~~~~~~~~~~~

.. figure:: img/Click_Shield_Ext_Clock_Sync.png
    :width: 700
    :align: center

When using an external clock and external trigger, the clock and trigger signals are transmitted to all devices in the chain. All the Click Shields share the same configuration:

**Primary and Secondary boards:**

* Jumper J6 connected. Connect the trigger to the Ext. trigger pin.
* Jumper J1 disconnected (unless using a single wire clock).
* CLK OSC switch in OFF position.
* CLK SELECT switch in EXT position.


Hardware specifications
-------------------------

For more information on the Click Shield, please see the :ref:`Click Shield documentation <click_shield>`.



.. _x-ch_streaming:

X-Channel Synchronisation
==========================

The Red Pitaya X-Channel System is a system that allows you to synchronise clock and trigger signals between multiple Red Pitaya boards. The X-Channel System consists of one **primary** device and one or more **secondary** devices connected in a daisy chain with SATA (Gen 1) or USB-C (Gen 2) cables.

.. note::

    We have decided to use primary and secondary device terminology instead of the standard master and slave device.

.. image:: img/RPs_to_PC_conn.png
    :width: 600


Setup
-------

.. figure:: img/Primary-and-secondary.png
    :width: 800

The Red Pitaya X-Channel system includes two types of devices:

    * one STEMlab 125-14 primary device (STEMlab 125-14 Low Noise).
    * one or more STEMlab 125-14 Low Noise secondary devices denoted by an "S" sticker.

S1 and S2 connectors are used to connect the primary and secondary devices:

    * **S1** - output for clock and trigger signals.
    * **S2** - input for (external) clock and trigger signals.

In order to achieve synchronization, the primary device outputs its clock and trigger signals through the S1 connector. The cable connection should therefore connect S1 connector of the primary device with S2 connector of the secondary device.
To continiue the daisy chain, connect the S1 connector of the first secondary device to the S2 connector of the second secondary device, and so on. 

It should be noted that **the secondary devices differ from the primary device hardware-wise**. The secondary devices are a special type of external clock Red Pitaya that receives the clock signal from the "FPGA".

.. note::

    **Booting secondary units without the external clock present?**
    The official Red Pitaya OS will not boot on the secondary units without providing an external clock as it relies on reading the FPGA register map, which is available if the ADC clock is present.
    However, by modifying the software, the Linux OS itself can boot even without the external clock present, but please note it will crash when trying to read from the FPGA without the external clock present.

The S1 and S2 connectors are SATA connectors on Gen 1 boards and USB-C connectors on Gen 2 boards. Usually, USB-C cables are bipolar can be connected in either direction, however, the S1 and S2 connectors are meant for sharing the clock and trigger signals and not connecting external devices.
Therefore, the orientation of the cable is improtant. On Gen 2 boards, two LEDs (**L** - Link and **O** - Orientation) are located next to the S1 connector:

* The **O** LED indicates the orientation of the cable.
* The **L** LED indicates whether the connection between the boards was successfully established.

When connecting the boards, make sure both LEDs are lit. If the **O** LED is not lit, change the orientation of the cable.

.. note::

    We recommend using :ref:`OS 2.00-23 or higher <prepareSD>` for the X-channel system.

    * With 2.00 OS both the primary and the secondary devices use the SAME OS!
    * With 1.04 OS the primary and secondary boards use DIFFERENT OS!



Alternative uses of S1 and S2 connectors
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The S1 and S2 connectors can also be used to connect to external devices directly to the FPGA. On Gen 1 boards where SATA connectors are used, this is slightly easier as the connectors are standard SATA. Gen 2 presents a challenge as the S1 and S2 connectors do not support the USB-C standard.

In either case, connecting external devices to the S1 and S2 connectors requires a modification in the FPGA as the default firmware does not support this feature.


Board compatibility
---------------------

The X-channel synchronisation is compatible with the following Red Pitaya board models:

* :ref:`STEMlab 125-14 Pro Gen 2 <top_125_14_pro_gen2>`.
* :ref:`STEMlab 125-14 Pro Z7020 Gen 2 <top_125_14_pro_Z7020_gen2>`.
* :ref:`STEMlab 125-14 (Gen 1) <top_125_14>`.
* :ref:`STEMlab 125-14 Low Noise (Gen 1) <top_125_14_LN>`.
* :ref:`STEMlab 125-14 Z7020 Low Noise (Gen 1) <top_125_14_Z7020_LN>`.

The secondary devices all require hardware modifications to be able to receive the clock signal from the primary device.


Example - signal acquisition (streaming client)
-------------------------------------------------

**Simultaneous acquisition of 6 input signals.**

In this example, we will acquire data from three synchronised Red Pitaya units (X-channel system), which gives us a total of six RF input channels.
For client installation and usage, please see the :ref:`Streaming application <streaming_top>` documentation.

.. code-block:: shell-session

    PRIMARY_IP=192.168.2.141, SECONDARY1_IP=192.168.2.60 SECONDARY2_IP=192.168.2.25


1. **Open the streaming app** on all Red Pitaya boards (primary and secondary) via the web interface.
#. **Adjust the streaming mode and settings.** For more information on specific settings check the :ref:`Data stream control application <streaming_top>`.

    .. code-block:: shell-session

        rpsa_client.exe -c -h 192.168.2.141,192.168.2.60,192.168.2.25 -s F -f test.conf -v

        2022.06.02-15.20.21.173:  Connected: 192.168.2.141
        2022.06.02-15.20.21.176:  Connected: 192.168.2.25
        2022.06.02-15.20.21.178:  Connected: 192.168.2.60
        2022.06.02-15.20.21.278:  Send configuration to: 192.168.2.141
        2022.06.02-15.20.21.291:  Send configuration to: 192.168.2.25
        2022.06.02-15.20.21.291:  SET: 192.168.2.141 [OK]
        2022.06.02-15.20.21.303:  Send configuration to: 192.168.2.60
        2022.06.02-15.20.21.309:  Send configuration save command to: 192.168.2.141
        2022.06.02-15.20.21.324:  SET: 192.168.2.25 [OK]
        2022.06.02-15.20.21.332:  Send configuration save command to: 192.168.2.25
        2022.06.02-15.20.21.337:  SET: 192.168.2.60 [OK]
        2022.06.02-15.20.21.343:  Send configuration save command to: 192.168.2.60
        2022.06.02-15.20.21.350:  SAVE TO FILE: 192.168.2.141 [OK]
        2022.06.02-15.20.21.357:  SAVE TO FILE: 192.168.2.25 [OK]
        2022.06.02-15.20.21.363:  SAVE TO FILE: 192.168.2.60 [OK]

#. **Start the X-channel streaming** of 6 inputs.

    .. code-block:: shell-session

        --streaming --host PRIMARY IP, SECONDARY1 IP, SECONDARY2 IP, --format=wav --dir=NAME
        --limit=SAMPLES

        rpsa_client.exe -s -h 192.168.2.141,192.168.2.60,192.168.2.25 -f wav -d ./acq -l 10000000 -v

        2022.06.02-15.25.00.795:  Connected: 192.168.2.141
        2022.06.02-15.25.00.798:  Connected: 192.168.2.25
        2022.06.02-15.25.00.804:  Connected: 192.168.2.60
        2022.06.02-15.25.00.907:  Send stop command to master board 192.168.2.141
        2022.06.02-15.25.00.925:  Streaming stopped: 192.168.2.141 [OK]
        2022.06.02-15.25.01.32:  Send stop command to slave board 192.168.2.25
        2022.06.02-15.25.01.36:  Send stop command to slave board 192.168.2.60
        2022.06.02-15.25.01.37:  Streaming stopped: 192.168.2.25 [OK]
        2022.06.02-15.25.01.45:  Streaming stopped: 192.168.2.60 [OK]
        2022.06.02-15.25.01.156:  Send start command to slave board: 192.168.2.25
        2022.06.02-15.25.01.169:  Send start command to slave board: 192.168.2.60
        2022.06.02-15.25.01.286:  Streaming started: 192.168.2.25 TCP mode [OK]
        2022.06.02-15.25.01.307:  Streaming started: 192.168.2.60 TCP mode [OK]
        2022.06.02-15.25.01.407:  Send start command to master board: 192.168.2.141
        2022.06.02-15.25.01.542:  Streaming started: 192.168.2.141 TCP mode [OK]
        2022.06.02-15.25.01.639:  Send start ADC command to slave board: 192.168.2.25
        Run write to: ./1/data_file_192.168.2.25_2022-06-02_13-25-00.wav
        Run write to: ./1/data_file_192.168.2.60_2022-06-02_13-25-00.wav
        Run write to: ./1/data_file_192.168.2.141_2022-06-02_13-25-00.wav
        2022.06.02-15.25.01.659:  Send start ADC command to slave board: 192.168.2.60
        2022.06.02-15.25.01.660:  ADC is run: 192.168.2.25
        Available physical memory: 16260 Mb
        Used physical memory: 8130 Mb
        Available physical memory: 16260 Mb
        Used physical memory: 8130 Mb
        Available physical memory: 16260 Mb
        2022.06.02-15.25.01.741:  Connect 192.168.2.25
        2022.06.02-15.25.01.730:  ADC is run: 192.168.2.60
        Used physical memory: 8130 Mb
        2022.06.02-15.25.01.752:  Connect 192.168.2.141
        2022.06.02-15.25.01.764:  Connect 192.168.2.60
        2022.06.02-15.25.01.826:  Send start ADC command to master board: 192.168.2.141
        2022.06.02-15.25.01.834:  ADC is run: 192.168.2.141
        2022.06.02-15.25.04.402:  Error 192.168.2.25
        2022.06.02-15.25.04.408:  Error 192.168.2.141
        2022.06.02-15.25.04.410:  Error 192.168.2.60
        2022.06.02-15.25.04.415:  Send stop command to master board 192.168.2.141
        2022.06.02-15.25.04.420:  Streaming stopped: 192.168.2.141 [OK]
        2022.06.02-15.25.04.422:  Streaming stopped: 192.168.2.141 [OK]
        2022.06.02-15.25.04.526:  Send stop command to slave board 192.168.2.25
        2022.06.02-15.25.04.529:  Send stop command to slave board 192.168.2.60
        2022.06.02-15.25.04.530:  Streaming stopped: 192.168.2.25 [OK]
        2022.06.02-15.25.04.533:  Streaming stopped: 192.168.2.60 [OK]
        2022.06.02-15.25.04.536:  Streaming stopped: 192.168.2.25 [OK]
        2022.06.02-15.25.04.545:  Streaming stopped: 192.168.2.60 [OK]

        2022.06.02-15.25.04.635 Total time: 0:0:2.794
        =====================================================================================================================
        Host              | Bytes all         | Bandwidth         |    Samples CH1    |    Samples CH2    |      Lost        |
        +--------------------------------------------------------------------------------------------------------------------|
        192.168.2.141     | 38.188 Mb         | 13.668 MB/s       | 10010624          | 10010624          |                  |
                        +...................+...................+...................+...................+ 0                |
                        |Lost in UDP: 0                         |Lost in file: 0                        |                  |
                        +...................+...................+...................+...................+                  |
        192.168.2.25      | 38.188 Mb         | 13.668 MB/s       | 10010624          | 10010624          |                  |
                        +...................+...................+...................+...................+ 0                |
                        |Lost in UDP: 0                         |Lost in file: 0                        |                  |
                        +...................+...................+...................+...................+                  |
        192.168.2.60      | 38.188 Mb         | 13.668 MB/s       | 10010624          | 10010624          |                  |
                        +...................+...................+...................+...................+ 0                |
                        |Lost in UDP: 0                         |Lost in file: 0                        |                  |
                        +...................+...................+...................+...................+                  |
        =====================================================================================================================

#. To **view acquired data**, drag the .wav files from **/acq** to |Audacity|.

    .. figure:: img/audacity_2.png
        :width: 800

    In this example, a 1 kHz sinewave signal was connected to all 6 inputs.



Code examples
=================

Here are examples for synchronising the X-channel system and Click shields through SCPI commands.

* :ref:`Multiboard synchronisation examples <examples_multiboard_sync>`.



.. _faq_multiboard:

Multiboard synchronisation Q&A
===============================

Here is a special Q&A section regarding the Red Pitaya Click Shields and their comparison to the X-Channel System. For general Red Pitaya Q&A, please see the :ref:`FAQ section <faq>`.

Can I synchronise multiple different Red Pitaya board models with the Click Shields?
--------------------------------------------------------------------------------------

Yes, you can. There can be different board models in a Red Pitaya Click Shield daisy chain. For example, the primary device can be a *STEMlab 125-14 4-Input* board,
the first secondary device a *STEMlab 125-14 ext. clk.*, and the second secondary device another *4-Input*. We recommend daisy chaining only devices with the same core clock speed.

Please take into account that *SDRlab 122-16 ext. clk.* is meant to receive a 122.88 MHz clock signal, so although synchronisation with *STEMlab 125-14* boards is possible, we do not recommend it.

While multiple different board models can be daisy chained, some features might be unavailable. See the :ref:`Click Shield compatibility section <click_shield_compatibility>`.


What is the difference between Red Pitaya X-channel System and Red Pitaya Click Shield Synchronisation?
--------------------------------------------------------------------------------------------------------

In this section we will talk about the difference between the Red Pitaya X-channel System and Red Pitaya Click Shield Synchronisation. It might seem like these two are completely the same, but that is far from the truth.

More info on :ref:`Red Pitaya X-channel System <top_125_14_MULTI>`.

.. note::

    Please note that the limitations of the Streaming applications are the same for both systems (continuous streaming). More information is available :ref:`here <streaming_top>`.


+--------------------------------+--------------------------------------------+--------------------------------------------+
|                                | **X-Channel System**                       | **Click Shield Synchronisation**           |
+================================+============================================+============================================+
| **Clock & Sampling rate**                                                                                                |
+--------------------------------+--------------------------------------------+--------------------------------------------+
| Recommended sampling rate      | Up to 100 ksps                             | Up to full sampling rate                   |
+--------------------------------+--------------------------------------------+--------------------------------------------+
| Shared clock signal            | Primary device CLK                         | Click Shield Oscillator OR external clock  |
+--------------------------------+--------------------------------------------+--------------------------------------------+
| External clock type            | N/A                                        | See |ZL40213| AC clock input specs         |
+--------------------------------+--------------------------------------------+--------------------------------------------+
| Clock signal delays            | | Slightly higher delay per unit           | 1x clock buffer per unit - |ZL40213|       |
|                                | | (signal through each FPGA) [#f1]_        |                                            |
+--------------------------------+--------------------------------------------+--------------------------------------------+
| Trigger signal delays          | | Slightly higher delay per unit           | 1x Trigger buffer per unit -               |
|                                | | (signal through each FPGA) [#f1]_        |  |74FCT38072DCGI|                          |
+--------------------------------+--------------------------------------------+--------------------------------------------+
| **Pinout**                                                                                                               |
+--------------------------------+--------------------------------------------+--------------------------------------------+
| GPIO access                    | Full access [#f2]_                         | Max 10 digital pins [#f3]_                 |
+--------------------------------+--------------------------------------------+--------------------------------------------+
| Slow analog access             | Full access (4/4)                          | Max 2 pins (2/4) [#f3]_                    |
+--------------------------------+--------------------------------------------+--------------------------------------------+
| Digital communication pins     | 1x UART, 1x SPI, 1x I2C, 2x CAN            | 2x UART, 2x SPI, 2x I2C (no CAN) [#f3]_    |
+--------------------------------+--------------------------------------------+--------------------------------------------+
| **Units**                                                                                                                |
+--------------------------------+--------------------------------------------+--------------------------------------------+
| | Compatible Red Pitaya board  | | Primary - STEMlab 125-14 LN              | | STEMlab 125-14 Pro Gen 2                 |
| | models                       | |                                          | | STEMlab 125-14 (LN) Ext Clk              |
| |                              | | Secondary - STEMlab 125-14 LN Secondary  | | SDRlab 122-16 Ext Clk                    |
| |                              | |                                          | | STEMlab 125-14 4-Input                   |
+--------------------------------+--------------------------------------------+--------------------------------------------+
| | Choosing between External    | No                                         | Yes [#f4]_                                 |
| | and Internal clock           |                                            |                                            |
+--------------------------------+--------------------------------------------+--------------------------------------------+
| Aluminium case compatibility   | No                                         | Yes                                        |
+--------------------------------+--------------------------------------------+--------------------------------------------+

.. rubric:: Footnotes

.. [#f1] Exact measurements will be provided in the future.

.. [#f2] Depending on the board model there can be either 16, 19, or 22 GPIO pins. Check the :ref:`Gen 1 <rp-board-comp-gen1>` or :ref:`Gen 2 <rp-board-comp-gen2>` comparison table for more information.
 
.. [#f3] Through the microBUS connectors.

.. [#f4] 4-Input and future HW board redesigns only.


.. substitutions

.. |ZL40213| raw:: html

    <a href="https://www.digikey.si/en/htmldatasheets/production/1239190/0/0/1/zl40213" target="_blank">ZL40213</a>

.. |74FCT38072DCGI| raw:: html

    <a href="  https://www.digikey.si/en/products/detail/renesas-electronics-corporation/74FCT38072DCGI/2017578" target="_blank">74FCT38072DCGI</a>

.. |Audacity| raw:: html

    <a href="https://www.audacityteam.org" target="_blank">Audacity</a>
