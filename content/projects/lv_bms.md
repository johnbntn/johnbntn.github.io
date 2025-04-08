+++
title = 'Low Voltage BMS'
date = 2025-04-05T12:52:32-04:00
draft = false
+++

Wrote firmware for a low voltage (20V) passive BMS system for a FSAE vehicle. This was done using an STM32L432KC and a BQ76905 BMS chip. The system communicates over I2C and also sends CAN to the vehicle's bus. There is support for a multitude of protections including over/under voltage, over/under temperature, short circuit in charge/discharge, and more. Unfortunately I cannot share the code as it is in a private repository.