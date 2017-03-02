# Server Guide: From Boot to Operation
*Index*:
* Server OS installation and consolidation
* Multi User Management setup
* Software Installation
* Temperature Sensors

## Server OS installation and consolidation
1. Install Ubuntu Server Edition
* Use logical naming convention (nodexx, ex: node01, node10, node11); the numbering must be sequential
* Install SSH on the installation process
2. Package installation
3. Add groups allowssh and allownfs
4. Backup /etc/ssh/sshd_config to /etc/ssh/sshd_config-backup
5. Correct/add the following options to /etc/ssh/sshd_config-backup:
* LogLevel VERBOSE
* PermitRootLogin no
* X11Forwarding no
* Uncomment Banner /etc/issue.net
* Add AllowGroups allows
* Save and restart ssh:
`sudo restart ssh`
6. Edit /etc/issue.net to indicate the machine’s name.
7. Edit /etc/init/control-alt-delete.conf: comment the exec shutdown -r now "Control-Alt-Delete pressed" line to stop the computer from rebooting when Ctrl+Alt+Del keyboard combination is pressed.

## Multi User Management setup
Folder structure and permissions.
1. Add folder "simulations" to /home/share/simulations:
`sudo mkdir -p /home/share/simulations`
2. Change folder group ownership to "allownfs":
`sudo chirp allownfs /home/share/simulations`
3. Change the folder's permissions to 2775:
`sudo chmod 2775 /home/share/simulations`
4. Symlink shared directory in /etc/skel/
`sudo ln -nsf /home/share/simulations /etc/skel/simulations`

## Software installation
Software that is system based, meaning, it lives in ubuntu repositories and is not GROMACS, FFTW or DLPOLY, should be installed using apt-get. Anything else must be installed under /home/ubu[user]/software using the ubu[user] as the compiler and owner of each and every file under that folder.
1. Create the folder /home/ubu[user]/software
2. Install compilers (gcc):
    `sudo apt-get install build-essential`
3. Install Gfortran:
    `sudo apt-get install gfortran`
4. Install MPICH2:
    `sudo apt-get install mpich2 mpich2-doc libcr-dev`
5. Install DLPOLY width default fftw and PARallel mode using MPICH2:
    1. Copy MakePAR in the `build/` directory to the `source/` directory.
    2. Rename MakePAR in the `source/` directory to Makefile.
    3. Compile: `make gfortran`.
6. Install fftw:
    1. `./configure --enable-float --enable-threads --prefix=/home/$HOSTNAME/software/fftw/fftw-x.x.x/prog-threads`
    2. `make && make install`
7. Install Gromacs

*Environment Variable Declaration*
Edit/Create the file /etc/profile.d/software_vars.sh
```
# Programs' Home Folders
export DLPOLY_HOME=/home/$HOSTNAME/software/dl_poly/dl_class_1.9
export GROMACS_HOME=/home/$HOSTNAME/software/gromacs

# Add to $PATH
export PATH=$PATH:$DLPOLY_HOME/execute:$GROMACS_HOME/
```

## Temperature Sensors
1. Install the `lm-sensors` package: `sudo apt-get install lm-sensors`
2. Detect the sensors using `sudo sensors-detect`. The default answers to each input request are reasonable except the last one regarding changes to be made on the /etc/modules file. The answer should be YES to write changes.
3. Load the new modules into the kernel: `sudo /etc/init.d/module-init-tools`
*Usage*: type `sensors` to get the temperatures.
*Tweaking*: edit `/etc/sensors.conf` (man sensors.conf)
