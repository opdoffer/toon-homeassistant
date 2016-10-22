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
**Step 1.** Dowload the toon.py en toonclient.py to a location of your choice (e.g. path/to/config). Home Assitent must be able to access those scripts.

**Step 2.** Import toon.py as a module in your python environment. Use the following command: ```python toon.py install```

**Step 3.** Add the following lines into you Home Assistant configuration.yaml (or create a seperate sensor.yaml file and include that in your configuration.yaml file):

```
sensor:
#Toon
 - platform: command_line
   name: toon
   command: "python /home/pi/.homeassistant/scripts/toonclient.py -t -p -g -c -U <USERNAME> -P <PASSWORD>"
   scan_interval: 60

 - platform: template
   sensors:
     toontemp:
       value_template: '{{ states.sensor.toon.state.split("\n")[0] }}'
     toonpowerusage:
       value_template: '{{ states.sensor.toon.state.split("\n")[1] }}'
     toongasusage:
       value_template: '{{ states.sensor.toon.state.split("\n")[2] }}'
     toonprogramm:
       value_template: '{% if states.sensor.toon.state.split("\n")[3] == "0" %}Comfort{% elif states.sensor.toon.state.split("\n")[3] == "1" %}Home{% elif states.sensor.toon.state.split("\n")[3] == "2" %}Sleep{% elif states.sensor.toon.state.split("\n")[3] == "3" %}Away{% endif %}'

# The toonprgramma sensor returns current state
# Program Turned Off(programm off) == -1
# RELAX (comfort) == 0
# ACTIVE (home) == 1
# SLEEP (sleep) == 2
# AWAY (away) == 3
# HOLIDAY (holiday) == 4


switch:    
# The following swith set Toon to "Comfort" with the oncmd and sets Toon to "Sleep" with the offcmd.
  - platform: command_line
    switches:
      toon_prog_comfort_sleep:
        command_on: "python /config/scripts/toonclient.py -C 0 -U <USERNAME> -P <PASSWORD>"
        command_off: "python /config/scripts/toonclient.py -C 2 -U <USERNAME> -P <PASSWORD>"

# The following swith set Toon to "Home" with the oncmd and sets Toon to "Away" with the offcmd.      
  - platform: command_line
    switches:
      toon_prog_home_away:
        command_on: "python /config/scripts/toonclient.py -C 1 -U <USERNAME> -P <PASSWORD>"
        command_off: "python /config/scripts/toonclient.py -C 3 -U <USERNAME> -P <PASSWORD>"
       
# Special Scenes to easily activate the different Toon Programm States
scene:
  - name: Toon Comfort
    entities:
      switch.toon_prog_comfort: on

  - name: Toon Sleep
    entities:
      switch.toon_prog_comfort: off

  - name: Toon Home
    entities:
      switch.toon_prog_thuis: on

  - name: Toon Away
    entities:
      switch.toon_prog_thuis: off
       
```
**Step 4.** Restart Home Assistant



## Install in Docker Container homeassistant/home-assistant 
Only use the following steps when you use homeassistant as a container in Docker.


**Step 1.** First follow these instructions to install Home Automation in a docker container: [homeassistant/home-assistant](https://github.com/home-assistant/home-assistant)

**Step 2.** Import toon.py module with the following command:

```
docker exec <CONTAINERDID> python /config/scripts/toon.py install
```
> Replace <CONTAINERID> with the ID of the container that is running homeassistant. Use "docker ps" to list your containers and determine the containerid.

**Step 3.** Add the same lines as step 3 of the general installation

**Step 4.** Restart Home Assistant

## Make switches to set Toon program
See the configuraion files for examples. Later on more explanation on this.

## Screendump example HA grouping Toon
![alt tag](https://github.com/opdoffer/toon-homeassistant/blob/master/screen_dump_toon.png)

## ToDo
- [x] Convert to python3
- [x] Adjust python client
- [x] Create home assistent examples
- [x] Make installation steps more clear
- [X] One sensor and extract it with a value_template to reduce payload on enecowebserver to prevent timeouts
- [ ] Make polling intervals variable (depends on home assistant core feautures)


