# Raspberry Pi Setup

This section will walk you through configuring the Raspberry Pi 5 with Raspberry Pi OS, Docker, and ROS2. 

## Requirements
- Raspberry Pi 5
- Micro SD Card (64GB+ preferred).
- Micro HDMI to HDMI cable.
- USB connected keyboard.
- USB connected mouse.

---

## Flash the SD Card

1. [Download the Raspberry Pi imager](https://www.raspberrypi.com/software/)
1. Select `Raspberry Pi 5` as the board
1. Select `Raspberry Pi OS (64-bit)`
1. Select your micro SD card.
1. Select your configuration settings. Recommended settings include:
    - Host Name: hexapod-<last_name>
    - User: hexapod<last_name>
    - Enable SSH via authentication.
1. Click save + wait.
1. Once flashed, plug your micro SD card into your Pi and connect peripherals.

## Install Docker

1. Update the system:
    ```bash
    sudo apt update && sudo apt full-upgrade -y
    ```

1. Download the Docker install script:
   ```bash
   curl -fsSL https://get.docker.com -o get-docker.sh
   ```

1. Install Docker:
   ```bash
   sudo sh get-docker.sh
   ```

1. Add your user to the `docker` group:
   ```bash
   sudo usermod -aG docker $USER
   ```

1. Log out and back in (or reboot) for the group change to take effect:
   ```bash
   sudo reboot
   ```

1. Verify the installation:
   ```bash
   docker --version
   docker compose version
   docker run hello-world
   ```

## Add the Necessary Config Overrides

1. Access the config file:

    ```bash
    sudo nano /boot/firmware/config.txt
    ```

1. Add the following lines to the end of the file:

    ```bash
    dtparam=spi=on
    dtoverlay=mcp2515-can0,oscillator=16000000,interrupt=25 
    ```

## Automatically Launch CAN at Startup

1. Get your IP:
    ```bash
    which ip
    ```

    ```
    /usr/sbin/ip
    ```


1. Create the file `/etc/systemd/system/canbus.service` with the following contents:
```bash
    [Unit]
    Description=CAN Bus Interface

    [Service]
    ExecStart=/sbin/ip link set can0 up type can bitrate 500000 #ip from the above step
    Type=oneshot
    RemainAfterExit=yes

    [Install]
    WantedBy=multi-user.target
```

1. Bring the service up:

    ```bash
    sudo systemctl start canbus.service
    ```

## Connect Xbox Core (Series S, X, etc) Controller

1. Connect the controller via Raspberry Pi OS's Bluetooth UI or via terminal.

## Update Docker User Config

1. Navigate to `non_real_time/container/src/ros2_ws/src/hexapod_manager/config/robot_name.local.txt`.
1. Add your Hexapod name to `robot_name.local.txt`. This should be the only text in the file.
1. Make sure the robot name is in `non_real_time/container/src/ros2_ws/src/hexapod_manager/launch/hexapod.launch.py` with a unique ROS_DOMAIN_ID.
1. Trigger a build:
    1. Initialize the dev container:

        ```bash
        ./init_dev.sh
        ```

    1. Get a new shell:

        ```bash
        ./new_dev_shell.sh
        ```

    1. Build the ROS2 Nodes

        ```bash
        colcon build
        ```

    1. Next time you restart the pi, the changes will take effect. To test you can also run:

        ```bash
        docker compose -f docker-compose-prod.yml up
        ```



