# GNSS-SDR GUI for generating Configuration files for Receivers

This page gives information about GUI developed for GNSS-SDR as part of GSoC 2017. The goal of this project was to develop a friendly Graphical User Interface (GUI) for the generation of configuration files for user defined receivers. The application reads the reference files of all the different implementations available in GNSS-SDR for different blocks and allows user to select implementations as desired by receiver.  All the options for a selected implementation will show up automatically in GUI so there is no need to remember the variable names. Developed GUI allow users to configure supl, signal sources, signal conditioner, acquisition, tacking, telemetry decoder, observable and PVT blocks. This will allow a friendly, and intuitive receiver definition. The GUI is easy to use and populate itself dynamically based on user input. After the user finish giving the inputs the application generates the configuration file that is compatible to be used with GNSS-SDR. Qt, a very popular and widely used graphical toolkit for creating GUI applications, was used to develop GUI.


## Getting Started

These instructions will get you a copy of the project up and running on your local machine for development and testing purposes.

### Installation from source on Linux (Tested on Ubuntu 16.04.3 LTS)

If you have a fresh installation of Ubuntu follow the following steps

```
$ sudo apt-get update
$ sudo apt-get install qtbase5-dev
$ sudo apt-get install qt5-default
```

Then navigate to the src directory and run following

```
$ qmake gnss_sdr_gui.pro DESTDIR=/path/to/executable/ GUI_FILES_LOCATION="/path/to/gui_ini_files/"
$ make
```

For example If you want to place the executable in /home/username/ and you have placed "gui_ini_files" folder in /home/username/Documents/. Use following qmake command in src directory

```
$ qmake gnss_sdr_gui.pro DESTDIR=/home/username/ GUI_FILES_LOCATION="/home/username/Documents/"
$ make
```

### Using build script(Tested on Ubuntu 16.04.3 LTS)

If you have Qt5.x and git installed on your system you can use this shell [script](https://github.com/UHaider/utilities/blob/master/build_app.sh) as follow

```
$ chmod +x build_app.sh
$ ./build_app.sh -D /home/username/ -G /home/username/Documents/
```

The script will clone this repository, build it and will place the executable in /home/username/. Also the script will copy the reference files directory in  /home/username/Documents/. 

If you run the script without any parameters as below

```
$ chmod +x build_app.sh
$ ./build_app.sh
```

The script will clone this repository, build it and will place the executable in the src direcotry. Also the user has to place the reference files directory in user's home folder otherwise the GUI will not be populated.

### Running on Linux with Qt Creator 4.2.1 (Community):

Install Qt Creator 4.2.1 community edition. Available free [here](https://info.qt.io/download-qt-for-application-development)

*	Just open the project (.pro) file in Qt creator.
*	Click configure project.
*	Clean and build.
*	Set DESTDIR and GUI_FILES_LOCATION from Qt creator project configuration.
*	Run

### Running on Windows with Qt Creator 4.2.1 (Community):

Install Qt Creator 4.2.1 community edition. Available free [here](https://info.qt.io/download-qt-for-application-development)

*	Just open the project (.pro) file in Qt creator.
*	Click configure project.
*	Clean and build.
*	Set DESTDIR and GUI_FILES_LOCATION from Qt creator project configuration.
*	Run


## Generating the configuration file

### Video 

Please click the link below to watch a video showing the working of the developed GUI. The video also shows how to generate the example configuration file for the online tutorial “My first position fix”. The output of GNSS-SDR with the generated configuration file is also shown.

[Video](https://streamable.com/casws)

## Design

The application uses reference files for global parameters, gnss-sdr classes whereas qss files are used for styles. For every class present in the project that uses configuration interface for setting the configuration options a reference ini file is created.  The file has all the configuration options available for that class/block. At application startup this file is used to populate the GUI with widgets and layouts. An example of reference file for UHD signal source is shown below

```

[UHD_Signal_Source]
;src/algorithms/signal_source/adapters/uhd_signal_source.cc
SignalSource.device_address=
SignalSource.subdevice=
SignalSource.clock_source=
SignalSource.RF_channels=
SignalSource.sampling_frequency=
SignalSource.item_type=cshort
;For Each RF Channel
RF_channels/SignalSource.samples=
RF_channels/SignalSource.dump=
RF_channels/SignalSource.dump_filename=./data/signal_source.dat
RF_channels/SignalSource.freq=
RF_channels/SignalSource.gain=
RF_channels/SignalSource.IF_bandwidth_hz=
```

This approach will make addition of new blocks or configuration options easy since developer can create a file for the new block or can edit the existing file for adding/deleting configuration options. If a user adds a new class for acquisition and wants to configure it through GUI he can place its reference file in Acquisition directory. The overall directory structure that should be maintain for correct working is shown in next figure.

![Directory structure](/docs/directory_structure.png)
*Directory structure*

The Styles directory contains the qss files. These files define the color schemes of GUI. User can define their own color schemes by editing the qss stylesheet file present in Styles directory. User can select the color scheme using Load Style in menu bar as shown below

![Default Color](/docs/default_colors.png)
*Style*

A C++ class was written for every direct subdirectory of main reference file directory .  The class reads all the reference files present in it or its child subdirectories while populating the GUI based on user input.  Following classes are written 
*	Global: This class reads the ini reference files present in Global directory. The configure options are global option of GNSS-SDR.
*	Supl:  All SUPL related options are populated on GUI using this class. 
*	SignalSource: This class reads the reference files present in signalsource directory. All signal sources available in GNSS-SDR can be configured. The class also emits qt signals for other classes.
*	 SignalConditioner: This class reads reference files from the signal conditioner directory and its child directories and populate the GUI with data type adapter, input filter and resampler configuration options. The class accepts qt signals from signal source.
*	Channels: Configuration options for acquisition, tracking and telemetry decoder are populated by this class.  The class accepts qt signals from the signal source.
*	Observables: Class written for populating the options for observable implementations.
*	PVT: Class written for populating the options for PVT implementations.
*	Gnssgui: This class is responsible for overall GUI and communication between classes using signals and slots. This class also reads the configuration options and values of all the implementations selected by the user for different blocks. Output file is generated by this class. Further, color scheme of the GUI is managed by this class by reading the qss files present in styles directory.

Flowchart showing the working of the application is shown in Figure 5.

![Flowchart](/docs/simple_flowgraph.jpg)
*Flowchart*



## Sample interfaces

Figures showing user interface for different classes are shown below.

### SUPL

User can select supl implementation from the combobox and following interface for SUPL will show up

![SUPL](/docs/supl.png)
*SUPL interface*

### Global

User interface for global options is shown below

![Global](/docs/global.png)
*Global interface*

### Signal Source

The signal source user interface is dynamically populated. The first variable is Receiver.sources_count. This value determines the number of signal sources for the receiver. If user enters 2 and click the update button, two subtabs will show up as shown below one for each signal source. This will also send qt signals to signal conditioner and channels classes so that two signal conditioners are created and signal source IDs can be set. 

![Signal Source](/docs/signal_source.png)
*Signal Source interface*

### Signal Conditioner

The signal conditioner class accepts signals from signal source class. Since, number of signal conditioner depends on the number of signal sources and RF channels the signal source class sends this information via Qt signals to signal conditioner class.  Implementation of data type adapter, input filters and resampler can be selected using respective comboboxes. User interfaces for data type adapter, input filters and resampler for signal conditioner are shown below

![Data Type Adapter](/docs/data_type_adapter.png)
*Data Type Adapter interface*

![Input Filter](/docs/input_filter.png)
*Input Filter interface*

![Resampler](/docs/resampler.png)
*Resampler interface*

### Channels

Channels user interface has four subtabs. First tab allows user to enter the values for generic options of channel. These options will be used to populate the acquisition, tracking and telemetry decoder subtabs. So when the user enters the values on this page and click update relevant signals are generated and respective slots update the subtabs. The channel class accepts signals from signal source class as well for getting information about number of signal source and RF channels for each source for determining if the receiver is multi source and multi band. 
Acquisition, tracking and telemetry decoder subtabs allows user to select the generic implementation for all the channels and also allows user to select implementation for a specific channel it he wants too. Following pictures show the interface for all subtabs

![Channels](/docs/channels.png)
*Channels interface*

![Acquisition](/docs/acquisition.png)
*Acquisition interface*

![Tracking](/docs/tracking.png)
*Tracking interface*

![Telemetry Decoder](/docs/telemetry.png)
*Telemetry Decoder interface*

### Observables

Observables user interface is given below, user can select the observables implementation from the combobox and configuration options for the selected implementation will show up

![Observables](/docs/observables.png)
*Observables interface*

### PVT

PVT user interface is given below, user can select the PVT implementation from the combobox and configuration options for the selected implementation will show up

![PVT](/docs/pvt.png)
*PVT interface*


## Bug reporting and feedback

Please contact on GNSS-SDR mailing list for any bug reporting and feedbacks. You can subscribe [here](https://sourceforge.net/projects/gnss-sdr/lists/gnss-sdr-developers)


## Contributing

Please read [CONTRIBUTING.md](https://github.com/gnss-sdr/gnss-sdr/blob/master/CONTRIBUTING.md) for details on our code of conduct, and the process for submitting pull requests to us.


## License

GNSS-SDR GUI is released under the [General Public License (GPL) v3](http://www.gnu.org/licenses/gpl.html). Please visit following link for details

[License](https://github.com/gnss-sdr/gnss-sdr#about-the-software-license)

## Acknowledgments

* Luis Esteve Elfau 
* Carlos Fernández

