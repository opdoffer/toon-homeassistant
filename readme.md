# Toon Python3 Module for Home Assistant
Toon Python3 module for Home Assistant. This python module is based on the [rvdm/toon](https://github.com/rvdm/toon). All credits for the development for this module and client to rvdm. Many thnx for that.
The following changes I made to the original module:
- made this module Python3 compliant print-command is requirering ();
- made some adjustments regarding spacing and tabs;
- made adjustments in the returned values so Home Assistant can interper that as measurable units (like Celsius degrees)
- added 'gas_usage' as an option in the toonclient
- added verbose mode -v option to the client for troubleshooting the python script.

Additionally I created a yaml-config-file to integrate this module in "Home Assistant".

## Prequisites
Basic understanding of [Home Assistant](https://homeAssistant.io).
Basic understanding of the original python2 script toonclient.py and its options.

There are basically installation scenarios: General installation for Linux systems based on Debian e.g. Ubuntu or the Home Assistant Docker version. More information on home assistant in Docker check this:https://home-assistant.io/getting-started/installation-docker/

## Installation (general installation e.g. RaspBerry Pi or Ubuntu virtual machine)
1. Dowload the toon.py en toonclient.py to a location of your choice (e.g. path/to/config). Home Assitent must be able to access those scripts.
2. Import toon.py as a module in your python environment. Use the following command: ```python toon.py install```
3. Add the following lines into you Home Assistant configuration.yaml (or create a seperate sensor.yaml file and include that in your configuration.yaml file):
```
sensor:
  - platform: command_line
    name: Toon_temp
    command: "python /config/scripts/toonclient.py -t -U <USERNAME> -P <PASSWORD>"
    unit_of_measurement: '°C'

  - platform: command_line
    name: Toon_PowerUsage
    command: "python /config/scripts/toonclient.py -p -U <USERNAME> -P <PASSWORD>"
    unit_of_measurement: 'Watt' 

  - platform: command_line
    name: Toon_Program_State
    command: "python /config/scripts/toonclient.py -c -U <USERNAME> -P <PASSWORD>"


switch:    
# The following swith set Toon to "Comfort" with the oncmd and sets Toon to "Sleep" with the offcmd.
  - platform: command_line
    switches:
      toon_prog_comfort:
        oncmd: "python /config/scripts/toonclient.py -C 0 -U <USERNAME> -P <PASSWORD>"
        offcmd: "python /config/scripts/toonclient.py -C 2 -U <USERNAME> -P <PASSWORD>"

# The following swith set Toon to "Thuis" with the oncmd and sets Toon to "Weg" with the offcmd.      
  - platform: command_line
    switches:
      toon_prog_thuis:
        oncmd: "python /config/scripts/toonclient.py -C 1 -U <USERNAME> -P <PASSWORD>"
        offcmd: "python /config/scripts/toonclient.py -C 3 -U <USERNAME> -P <PASSWORD>"
       
# Special Scenes to easily activate the different Toon Programm States
scene:
  - name: Toon Comfort
    entities:
      switch.toon_prog_comfort: on

  - name: Toon Slapen
    entities:
      switch.toon_prog_comfort: off

  - name: Toon Thuis
    entities:
      switch.toon_prog_thuis: on

  - name: Toon Weg
    entities:
      switch.toon_prog_thuis: off
       
```
 4. Restart Home Assistant



## Install in Docker Container homeassistant/home-assistant 
Only use the following steps when you use homeassistant as a container in Docker.

1. First follow these instructions to install Home Automation in a docker container: [homeassistant/home-assistant](https://hithub.com/homeassistant/home-assistant)
2. Import toon.py module with the following command:

```
docker exec <CONTAINERDID> python /config/scripts/toon.py install
```
3. 3. Add the following lines into you Home Assistant configuration.yaml (or create a seperate sensor.yaml file and include that in your configuration.yaml file):
```
# Add the following lines to your configuration.yaml
# Make sure you enter the correct username and password credentials (without <>), which are the same you are using for your Toon mobile app

Sensor:
  - platform: command_line
    name: Toon_temp
    command: "python /config/scripts/toonclient.py -t -U <USERNAME> -P <PASSWORD>"
    unit_of_measurement: '°C'

- platform: command_line
    name: Toon_PowerUsage
    command: "python /config/scripts/toonclient.py -p -U <USERNAME> -P <PASSWORD>"
    unit_of_measurement: 'Watt'

# Following sensor returns current state
# Program Turned Off(programma staat niet aan) == -1
# RELAX (comfort) == 0
# ACTIVE (thuis) == 1
# SLEEP (slapen) == 2
# AWAY (weg) == 3
# HOLIDAY (vakantie) == 4
- platform: command_line
    name: Toon_Program_State
    command: "python /config/scripts/toonclient.py -c -U <USERNAME> -P <PASSWORD>"

```
4. Restart Home Assistant

## Make switches to set Toon program
See the configuraion files for examples. Later on more explanation on this.

## Screendump example HA grouping Toon
![alt tag](https://github.com/opdoffer/toon-homeassistant/blob/master/screen_dump_toon.png)


