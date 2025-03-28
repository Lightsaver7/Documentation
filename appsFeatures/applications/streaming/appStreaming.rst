.. _streaming_top:

#########
Streaming
#########

.. ! JOIN X-Channel Streaming and this
.. ! OPEN STREAMING on all boards

.. figure:: img/settings_2.00.png
    :width: 1000
    :align: center

The Streaming application enables users to stream data from Red Pitaya to:

    * A file saved on the Red Pitaya SD card
    * A file saved on a remote computer via the ethernet protocol (UDP or TCP).

The user can set:

    * The sampling frequency (rate)
    * Input channel count (Channel 1, Channel 2, or Both (4 Channels for STEMlab 125-14 4-Input))
    * Input channel resolution (8 or 16 bits)
    * Input attenuation (HV/LV mode) (for 125-xx, a switch of the jumper is required)
    * Whether to use the calibration or not (for 125-xx, the filter is also calibrated)
    * RAW / Volts mode
    * The number of samples or unlimited sampling

Streamed data can be stored into:

    * Standard audio WAV file format
    * Technical Data Management Streaming (TDMS) file format
    * Fast and compact binary format (BIN). It can be converted to CSV format.

Max. streaming speeds (per board) are limited to:

    * 10 MB/s for streaming to an SD card (SD card class 10 is recommended for optimal streaming performance)
    * 20 MB/s for streaming over 1 Gbit network (A :ref:`direct connection <dir_cab_connect>` is recommended to achieve the best streaming performance)

.. note::

    The maximum continuous streaming speeds (per board) are limited to the total input data rate, not the network transfer rates. If the maximum data rate is exceeded, the data pipeline inside Red Pitaya starts to clog, which leads to unpredictable behaviour.
    Here are a few examples of maximum data rates:

    * One channel, 8-bits per sample: Max sampling frequency 20 MHz.
    * One channel, 16-bits per sample: Max sampling frequency 10 MHz.
    * Two channels, 8-bits per sample: Max sampling frequency per channel 10 MHz (assuming same frequencies for both channels)
    * Two channels, 16-bits per sample: Max sampling frequency per channel 5 MHz (assuming same frequencies for both channels)

    If acquiring a limited amount of samples in a short duration, it is possible to reach higher sampling frequencies (up to the sampling speed of fast analog inputs).

**Minumum streamed data size**

To increase the efficiency of the application, there is a minimum data size that can be sent through the network. This can have a big impact at high decimation values, as it takes a long time to fill a chunck. If the stream is stopped before a chunck is completed, the data is discarded and the saved file has a size of **0 b**.

Here are the minimum chunck limitations sorted by file type and units:


.. list-table::
    :widths: 20 20 20
    :header-rows: 1

    * - File type \\ Units
      - VOLTS
      - RAW
    * - WAV 
      - 128.043 kb
      - 64.043 kb
    * - TDMS
      - 128.133 kb
      - 64.133 kb
    * - BIN
      - 64.090 kb
      - 64.090 kb


.. note::

    We plan to expand the functionality by adding the generation to the Streaming application in the future. For now, it is possible for a user to implement it by themselves.


.. ! TODO: Document the new fast streaming feature and prepare the examples

Getting started with the Red Pitaya streaming feature
=======================================================

Run the Streaming app from the Red Pitaya Web interface

.. figure:: img/redpitaya_main_page.png
    :width: 600
    :align: center


Stream locally to a file on Red Pitaya's SD card
=================================================

.. tabs::

    .. group-tab:: OS version 2.00-15 or older

        #. Configure the stream properties & click **RUN**

            .. figure:: img/settings.png
                :width: 800
            
            Example: streaming on ch1, 8-bit resolution, 5.208 MS/s into TDMS file format

        #. Press **STOP** to stop streaming

        #. Click *Browse* to open the data file directory. Each data stream is split into three sections; *DATA* (collected data stream), *.log* (data log of the specific stream), *.log.lost* (report on lost packets). Click on the selected file to download it from Red Pitaya to the computer.

            .. figure:: img/capture.png
                :width: 800
                :align: center

        #. Open the file in a program that supports the selected file format, visualisation, and processing, such as |DIAdem| for TDMS files, or |Audacity| for WAV.

            .. figure:: img/diadem_tdms_file_viewer.png
                :width: 800
                :align: center

    .. group-tab:: OS version 2.00-23 or newer

        #. Configure the stream properties & click **RUN**

            .. figure:: img/Streaming_app_local.png
                :width: 1000
            
            Example: streaming on CH1 and CH2, 8-bit resolution, 100 ksps into WAV file format

        #. Press **STOP** to stop streaming

        #. Check the *Files on SD card* section for the data files. Each data file has three buttons; *LOG* (data log of the specific stream), *LOST* (report on lost packets), and *DOWNLOAD* (collected data stream). Click on the selected file to download it from Red Pitaya to the computer.

            .. figure:: img/Streaming_app_local.png
                :width: 1000
                :align: center

        #. Open the file in a program that supports the selected file format, visualisation, and processing, such as |DIAdem| for TDMS files, or |Audacity| for WAV.

            .. figure:: img/diadem_tdms_file_viewer.png
                :width: 800
                :align: center

.. _stream_command_client:

Streaming to a remote computer via Command Line or Terminal
=============================================================

.. tabs::

    .. group-tab:: OS version 2.00-15 or older

        #. Download the streaming client for your computer. Clients are located on the board itself and can be downloaded from there.

            .. figure:: img/download_client.png
                :width: 800
                :align: center

        #. Configure the stream properties & click **RUN**

                .. figure:: img/tcp_settings.png
                    :width: 300
                    :align: center

                Example: streaming on CH1, 16-bit resolution 5 Msps, TCP

        #. Execute the *streaming client* via *Command Line or Terminal* on a remote computer (copy the IP address from the web interface and choose the required file format).

                .. tabs::

                    .. group-tab:: WAV

                        .. code-block:: console

                            rpsa_client.exe -h 192.168.1.29 -p TCP -f ./ -t wav

                        .. figure:: img/tcp_client.png
                            :width: 600
                            :align: center

                        Data streaming can be stopped by pressing *Ctrl+C*.

                        The created wav file can be read or viewed in |Audacity| or another program that supports WAV file type:

                        .. figure:: img/audacity.png
                            :width: 600
                            :align: center

                    .. group-tab:: TDMS

                        .. code-block:: console

                            rpsa_client.exe -h 192.168.1.29 -p TCP -f ./ -t tdms

                        .. figure:: img/tcp_client2.png
                            :width: 600
                            :align: center

                        Data streaming can be stopped by pressing *Ctrl+C*.

                        The created tdms file can be read or viewed in |DIAdem| or another program that supports TDMS file type.

                        .. figure:: img/diadem_tdms_file_viewer.png
                            :width: 600
                            :align: center

                    .. group-tab:: CSV

                        .. code-block:: console

                            rpsa_client.exe -h 192.168.1.29 -p TCP -f ./ -t csv -s 100000 -v


                        .. figure:: img/tcp_client3.png
                            :width: 600
                            :align: center


                        The application saves data from the board in binary (BIN) format.

                        .. figure:: img/csv_list.png
                            :width: 600
                            :align: center

                        The binary file can be converted using the *convert_tool* application.

                        .. figure:: img/csv_list.png
                            :width: 600
                            :align: center

                        The created CSV file can be opened with any text editor, spreadsheet editor, or any other application that supports the CSV file type:

                        .. figure:: img/csv_view.png
                            :width: 600
                            :align: center

                        .. note::

                            Using the *convert_tool application* you can also see the structure of the received file and the state of the file.

                            .. figure:: img/csv_state.png
                                :width: 600
                                :align: center

    .. group-tab:: OS version 2.00-23 or newer

        #. Download the *command line streaming client* for your computer. Clients are located on the board itself and can be downloaded from there.

            .. figure:: img/Streaming_app_cmd_clients.png
                :width: 1000
                :align: center

        #. Configure the stream properties & click **RUN**

                .. figure:: img/Streaming_app_network.png
                    :width: 1000
                    :align: center

                Example: streaming on CH1 and CH2, 16-bit resolution, 100 ksps, TCP 

        #. Execute the *streaming client* via *Command Line or Terminal* on a remote computer (copy the IP address from the web interface and choose the required file format).

                .. tabs::

                    .. group-tab:: WAV

                        .. code-block:: console

                            rpsa_client.exe -h 192.168.1.29 -p TCP -f ./ -t wav

                        .. figure:: img/tcp_client.png
                            :width: 600
                            :align: center

                        Data streaming can be stopped by pressing *Ctrl+C*.

                        The created wav file can be read or viewed in |Audacity| or another program that supports WAV file type:

                        .. figure:: img/audacity.png
                            :width: 600
                            :align: center

                    .. group-tab:: TDMS

                        .. code-block:: console

                            rpsa_client.exe -h 192.168.1.29 -p TCP -f ./ -t tdms

                        .. figure:: img/tcp_client2.png
                            :width: 600
                            :align: center

                        Data streaming can be stopped by pressing *Ctrl+C*.

                        The created tdms file can be read or viewed in |DIAdem| or another program that supports TDMS file type.

                        .. figure:: img/diadem_tdms_file_viewer.png
                            :width: 600
                            :align: center

                    .. group-tab:: CSV

                        .. code-block:: console

                            rpsa_client.exe -h 192.168.1.29 -p TCP -f ./ -t csv -s 100000 -v


                        .. figure:: img/tcp_client3.png
                            :width: 600
                            :align: center


                        The application saves data from the board in binary (BIN) format.

                        .. figure:: img/csv_list.png
                            :width: 600
                            :align: center

                        The binary file can be converted using the *convert_tool* application.

                        .. figure:: img/csv_list.png
                            :width: 600
                            :align: center

                        The created CSV file can be opened with any text editor, spreadsheet editor, or any other application that supports the CSV file type:

                        .. figure:: img/csv_view.png
                            :width: 600
                            :align: center

                        .. note::

                            Using the *convert_tool application* you can also see the structure of the received file and the state of the file.

                            .. figure:: img/csv_state.png
                                :width: 600
                                :align: center

.. |DIAdem| raw:: html

    <a href="https://www.ni.com/en-us/shop/data-acquisition-and-control/application-software-for-data-acquisition-and-control-category/what-is-diadem.html" target="_blank">DIAdem</a>


.. |Audacity| raw:: html

    <a href="https://www.audacityteam.org" target="_blank">Audacity</a>



Instructions for the rpsa_client
-----------------------------------


1. **Detect Mode**

	This mode allows you to determine the IP addresses that are in the local network in streaming mode. By default, the search takes 5 seconds.

   	.. literalinclude:: include/detectMode.txt

2. **Configuration Mode**

	This mode allows you to get or set the streaming configuration on the boards.

   	.. literalinclude:: include/configMode.txt

    Variables can also be set individually:

    .. literalinclude:: include/configModeSingle.txt

3. **Remote control Mode**
      
    This mode allows you to control streaming as a client.

   	.. literalinclude:: include/remoteControlMode.txt

4. **Streaming Mode**

    This mode allows you to control streaming as a client, and also captures data in network streaming mode.

    .. literalinclude:: include/streamingMode.txt

5. **DAC streaming Mode**

    This mode allows you to generate output data using a signal from a file.

    .. literalinclude:: include/dacStreamingMode.txt

6. **Configuration Variables**

    Configuration file variables and their valid values.

    .. literalinclude:: include/configVariables.txt


Convert tool
--------------

.. tabs::

    .. group-tab:: OS version IN DEV

        The convert tool allows you to convert the *.bin* file format into a *.csv*, *.tdms*, or *.wav* file.

        .. literalinclude:: include/convert_tool.txt

        To convert the binary file, first check the file information using:

        .. code-block:: bash

            .\convert_tool.exe .\<path_to_bin_file>\data_file.bin -i

        .. literalinclude:: include/convert_tool_info.txt

        The file information includes the number of segments into which the data is split. Using the convert tool, you can choose to convert only the specfied portion of the streamed file to the desired forma

        .. code-block:: bash

            .\convert_tool.exe .\<path_to_bin_file>\data_file.bin -s 1 -e 18 -f CSV

        The converted file will appear next to the original file.

        .. note::

            The file type (CSV, TDMS or WAV) must be capitalised.

.. _stream_desktop_app:

Streaming to a remote computer via Desktop Application (Linux, Windows)
=========================================================================

.. note::

    The state of the settings in the web interface does not necessarily reflect the actual settings of the streaming application. To get the current settings from each board, please use the "Get settings" button.


The other option for streaming is utilyzing the Desktop Application.

#. Download the client application

    .. tabs::

        .. group-tab:: OS version 2.00-15 or older

            Files with clients are available |Streaming Client|.

        .. group-tab:: OS version 2.00.23 or newer

            Files with clients are in the Streaming Application (Data Stream Control). You can download it from Red Pitaya itself.

            .. figure:: img/Streaming_app_desktop_client.png
                :width: 1000
                :align: center


#. Unzip and run the client

    - For Linux clients, after unpacking, the files (rpsa_client_qt.sh, bin/rpsa_client_qt) must be made executable.

        .. figure:: img/qt1.png
            :width: 800
            :align: center

    - For Windows clients, you need to grant access to the network.

        .. note::

           It is possible that an Antivirus program might block the desktop client. If you experience this issue, we recommend whitelisting the Streaming Client folder.

#. Once the Desktop application is running it automatically detects boards on the network, if the Streaming Application is running on them. The boards and the client must be on the same network.

    .. figure:: img/qt2.png
        :width: 1000
        :align: center


.. |Streaming Client| raw:: html

    <a href="https://downloads.redpitaya.com/downloads/Clients/streaming/desktop/" target="_blank">here</a>


Streaming data to Red Pitaya Linux
====================================

Downloading and extracting the **RP rpsa** streaming client onto the Red Pitaya board allows you to access the streamed data from Python code running directly on the Red Pitaya.

This mode is currently **IN DEV**. Documentation will be updated when full functionality is available.


Source code
==============

The `Streaming application source code <https://github.com/RedPitaya/RedPitaya/tree/master/apps-tools/streaming_manager>`_ is available on our GitHub.

