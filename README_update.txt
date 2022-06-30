# Build option in easy way ..
### Installation from source on Linux (Tested on Ubuntu 20.04 LTS)
## This gui for generating config files..  ONLY
If you have a fresh installation of Ubuntu follow the following steps

```
$ sudo apt-get update
$ sudo apt-get install qtbase5-dev
$ sudo apt-get install qt5-default
```
Install the Previous Dependencies.
Then navigate to the src directory and run following

```
Previously Told might work some people cases //$ qmake gnss_sdr_gui.pro DESTDIR=/path/to/executable/ GUI_FILES_LOCATION="/path/to/gui_ini_files/"
open terminal and take it to the source folder of gui_ini_files like in my case i had ("/home/sa/Downloads/gnss_sdr_gui-master/src/gnss_sdr_gui.pro")
Here run th efollowing command ( $ qmake ./gnss_sdr_gui.pro )
then ($ make )

Now you will see some binary named as gnss-sdr-gui 
"/home/sa/Downloads/gnss_sdr_gui-master/src/gnss-sdr-gui"
Run the binary which will give the following error..  YOU MUST PUT THE gui_ini_files to home folder
in this case  copy the gnss_ini_files (folder) to your home in my case  it was (home/sa/) and thats it you are good to go
 //$ make
```

i tried the way that was  told in the Repository butt that didnt worked in my case so i did as told before. 
