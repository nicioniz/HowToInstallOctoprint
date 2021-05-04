# How to install OctoPrint on OrangePi PC #

## 1. Choose an OS ##
### 1a. Armbian ###
The recomended OS is [Armbian](www.armbian.com), at its last release.

The releases available for the OrangePi PC are here [www.armbian.com/orange-pi-pc/](www.armbian.com/orange-pi-pc/).

The image can be flashed on the SD using Balena Etcher, available on the site [www.balena.io/etcher/](www.balena.io/etcher/).

Once flashed the SD card, power on the system (connected to a monitor and a keyboard). At first boot the system will request you to set the root password and to set a new user to use the OS.


### 1b. Debian ###
Another choice can be Debian server, available from the OrangePi productor site [www.orangepi.org/](www.orangepi.org/).

Search for the OrangePi PC section and found the right image [www.orangepi.org/downloadresources/](www.orangepi.org/downloadresources/).

Once downloaded the image unzip it and flash the `*.img` file with some tool, for example Win32DiskImager on Windows.

The user needed to login is `root` and the password is `orangepi`.

After the first login change the default password and create a new user.


### 1c. Any other Ubuntu or Debian based OS ###
Any Os that is Ubuntu or Debian based is suitable.

First thing to do, on any OS, is to update everything that is upgradable with the commands:
```
sudo apt update
sudo apt upgrade
```


## 2. Add permission to the user ##
The created user need some permission in order to run OctoPrint.
```
sudo usermod -a -G tty USER
sudo usermod -a -G dialout USER
sudo adduser USER sudo
```


## 3. Install Python ##
You need to install the last Python release, so it is needed to download the source and compile it.

1. Install all the dependencies needed.
```
sudo apt update
sudo apt install build-essential zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev libssl-dev libreadline-dev libffi-dev curl libbz2-dev
```

2. Download the sources from the official site (ftp mirror). Look for the last version and download the `*.tar.xz` package. Then extract it.
```
curl -O https://www.python.org/ftp/python/X.Y.Z/Python-X.Y.Z.tar.xz
tar -xf Python-X.Y.Z.tar.xz
```

3. Compile the sources.
```
cd Python-X.Y.Z
./configure --enable-optimizations
make -j $(nproc)
```
This will take a lot of time, something like 45 minutes.

4. Install the python binaries.

If you DON'T have python installed on your system you can use:
```
sudo make install
```

Instead, if you want to not overwrite your previous Python install, you can use:
```
sudo make altinstall
```

5. Now you can verify the installed version.
```
python3 --version
```



## 4. Install OctoPrint ##
You need to install the last OctoPrint release. It is possible to install from pip manager.
These steps are taken from the [official OctoPrint community](community.octoprint.org/t/setting-up-octoprint-on-a-raspberry-pi-running-raspbian-or-raspberry-pi-os/2337).

1. Install all the dependencies needed.
```
sudo apt update
sudo apt install python3-pip python3-dev python3-setuptools python3-venv git libyaml-dev build-essential
sudo apt install git libyaml-dev build-essential
```

2. Create the environment needed to install OctoPrint. Create a directory and a Python virtualenv.
```
cd ~
mkdir OctoPrint && cd OctoPrint
python3 -m venv venv
source venv/bin/activate
```

3. Upgrade `pip` manager.
```
pip install pip --upgrade
```

4. Install OctoPrint (`--no-cache-dir` is needed to ensure the downloading of the last OctoPrint version).
```
pip install --no-cache-dir octoprint
```

5. It is a good thing now to reboot your system.
```
sudo shutdown -r now
```

6. Test that everything is working.
```
OctoPrint/venv/bin/octoprint serve
```
And try to connect to `http://<pi's IP>:5000`. You should be greeted with the OctoPrint UI.


### 4a. Automatic Startup ###

Setup the automatic startup (save OctoPrint as a system service).
   
1. Download the service script
```
wget https://github.com/OctoPrint/OctoPrint/raw/master/scripts/octoprint.service
```

2. Modify the script based on your environment
```
nano octoprint.service
```
and modify the `User` part with your `USER` and the `ExecStart` part with `/home/USER/OctoPrint/venv/bin/octoprint`

3. Move the script to its installation folder
```
sudo mv octoprint.service /etc/systemd/system/octoprint.service
```

4. Try if everything is correctly set up
```
sudo systemctl start octoprint.service
```

5. If everything works you can enable OctoPrint to start at boot
```
sudo systemctl enable octoprint.service
```



## 5. Enjoy it and print! :muscle: :fire: ##
