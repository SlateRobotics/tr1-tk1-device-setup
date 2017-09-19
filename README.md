# tr1-tk1-device-setup
Device setup and dependency install instructions for the NVIDIA Jetson TK1 on the Slate TR1

## Flash Device
1. Connect host machine to TK1 via ethernet to same router
2. Connect TK1 via USB
3. Open Terminal on host machine
    1. `./home/slate/Desktop/JetPack3/JetPack-L4T-3.1-linux-x64.run`
4. Follow on-screen instructions to flash device

## Install Packages
1. Connect TK1 to monitor via HDMI
2. Open Terminal
    1. `sudo apt-get update`
    2. `sudo apt-get upgrade`
    3. `sudo apt-get install git nano ssh xfce4 xfce4-goodies tightvncserver`
    4. `ssh localhost` - Test ssh
    5. `git clone https://github.com/jetsonhacks/installGrinch.git`
    6. `cd installGrinch`
    7. `./installGrinch.sh` - install Grinch kernels for Intel 7260 support
    5. `vncserver` - Configure vncserver (from https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-vnc-on-ubuntu-14-04)
    6. `vncserver -kill :1` - Kills server on port 5901'
    7. `mv ~/.vnc/xstartup ~/.vnc/xstartup.bak` - backup xstartup file
    8. `nano ~/.vnc/xstartup` - create new startup file
    ```bash
    #!/bin/bash
    xrdb $HOME/.Xresources
    startxfce4 &
    ```
    9. `sudo chmod +x ~/.vnc/xstartup` - grant permisions for new startup file
    10. `sudo nano /etc/init.d/vncserver` - create new vncserver service file
    ```bash
    #!/bin/bash
    PATH="$PATH:/usr/bin/"
    export USER="ubuntu"
    DISPLAY="1"
    DEPTH="16"
    GEOMETRY="1024x768"
    OPTIONS="-depth ${DEPTH} -geometry ${GEOMETRY} :${DISPLAY} -localhost"
    . /lib/lsb/init-functions
    
    case "$1" in
    start)
    log_action_begin_msg "Starting vncserver for user '${USER}' on localhost:${DISPLAY}"
    su ${USER} -c "/usr/bin/vncserver ${OPTIONS}"
    ;;
    
    stop)
    log_action_begin_msg "Stopping vncserver for user '${USER}' on localhost:${DISPLAY}"
    su ${USER} -c "/usr/bin/vncserver -kill :${DISPLAY}"
    ;;
    
    restart)
    $0 stop
    $0 start
    ;;
    esac
    exit 0
    ```
    11. `sudo chmod +x /etc/init.d/vncserver`
    12. `sudo service vncserver start`
    13. `sudo update-rc.d vncserver defaults` - add vncserver to default services to start on boot
