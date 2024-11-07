# MakerWatch
![Image](https://github.com/atiaisaac/MakerWatch/blob/main/images/Screenshot%20from%202024-10-12%2020-48-24.png)

## About
An open source smartwatch built by a maker for the maker community packed with interesting features and runs on the zephry RTOS

## Hardware
Outlined below is a breakdown of the hardware componenets considered for this design;

* __NINA-B306__ module (nrf52840 based microcontroller)
    * 1MB flash memory
    * 256KB of RAM
    * Bluetooth LE
    * internal pcb antenna
* __LSM6DSOX__ IMU with 6-DOF from stmicroelectronics.
* __BQ24230__ single cell li-ion charger with dynamic power path managament(DPMM) from texas instruments
* __BQ27441-G1__ battery fuel guage also from texas instruments
* __TPS62202__ high-efficiency step-down DC-DC converter
* __MAXM86146__ complete biometric sensor hub - a wrist-based heart rate monitoring and pulse oximetry. All computations are done on device freeing the MCU to perform other tasks.
* __FIREFLY E2218__ true green color for health monitoring i.e HRM and pulse oximetry
* __SFH 7015__ hyper-red and infrared for HRM and pulse oximetry
* 1.28'' round tft display with __GC9a01__ display driver
* __RV-8523-C3__ crystal to serve as real time clock/calendar
* __USB-C__ interface for easy flashing of firmware

## Software
