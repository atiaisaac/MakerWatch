# MakerWatch
![Image](https://github.com/atiaisaac/MakerWatch/blob/main/images/image_1.png)

## About
An open source smartwatch built by a maker for the maker community packed with interesting features and runs on the zephry RTOS

## Hardware
Outlined below is a breakdown of the hardware componenets considered for this design;

* __NINA-B306__ module (nrf52840 based microcontroller)
    * 1MB flash memory
    * 256KB of RAM
    * Bluetooth LE
    * internal pcb antenna
* __LSM6DSOX__ IMU with 6-DOF from stmicroelectronics. Primarily for step counting (pedometer) and other cool navigation gestures.
* __NPM1300__ single cell li-ion charger with power path managament. It also includes 2 buck converters capable of providing 200mA each and 3 LDOs. More than enough for our setup. When paired with the right nrf chipset, it can serve as a fuel guage as well.
* __MAX86141__ an AFE for optical data acquisition. Paired with MAX32664
* __MAX32664__ an ultra-low power biometric sensor hub with embedded firmware for heart rate monitoring and blood oxygen saturation measurement.
* __LIS2DS12__ paired with MAX32664 to compensate for artifacts of motion required for proper HRM and SpO2 measurement.
* __FIREFLY E2218__ true green color for health monitoring i.e HRM and pulse oximetry
* __SFH 7015__ hyper-red and infrared for HRM and pulse oximetry.
* __SFH 2703__ is a photodiode for capturing reflected light needed by the HRM and SpO2 algorithm
* 1.28'' round tft display with __GC9a01__ display driver
* __RV-8523-C3__ crystal to serve as real time clock/calendar
* __USB-C__ interface for easy flashing of firmware for developers/makers

## Software
