## CAN

!!! note
    These commands should not be needed during normal operation of the Hexapod as there is a service to bring CAN up and down automatically, but they may be useful during debugging.

    These commands should be run in the Pi terminal, not in the docker container.

* Bring CAN down

```bash
sudo ip link set can0 down
```

* Set CAN bitrate

```bash
sudo ip link set can0 type can bitrate 500000
```

* Bring CAN up

```bash
sudo ip link set can0 up
```

* Monitor CAN

```bash
candump can0
```

* Send a dummy message

```bash
cansend can0 123#DEADBEEF
```
