# masimo-datacapture
This is a basic program to capture data from [Masimo Brand Pulse Oximeter](https://www.masimo.com/products/continuous/rad8/) and store the data in an [Influx database](https://www.influxdata.com/) for further extensive analysis.

Home-assistant (smart home automation system) is used to display real-time data and interact with end-users/care-takers and other connected devices within a smart home environment. Masimo-datacapture is connected via MQTT to Home-Assistant.

# Masimo Rad-8
The Masimo Rad-8 needs to be set in ASCII mode 1. By default the device is set to ASCII mode 2 for the serial output. Hold down the Enter Button and the Down Button for 5 seconds to change the parameter. See [manual](https://github.com/remkolems/masimo-datacapture/blob/master/Masimo%20Rad-8/Masimo%20Rad-8%20Operator's%20Manual.pdf) page 4-14 (section 4 Setup menu level 3).

## Front
![Front panel](https://github.com/remkolems/masimo-datacapture/blob/master/Masimo%20Rad-8/masimo-rad8_front.png)
## Back
![back panel](https://github.com/remkolems/masimo-datacapture/blob/master/Masimo%20Rad-8/masimo-rad8_backpanel.png)
## Display
![Main display](https://github.com/remkolems/masimo-datacapture/blob/master/Masimo%20Rad-8/masimo-rad8_display.png)

A. Signal I.Q. (SIQ) bar is a signal quality indicator. The LED rises and falls with the pulse, its height indicating signal quality. When Signal IQ is low, the display turns red.\
B. The Alarm Status Indicator flashes when an alarm condition is present.\
C. Perfusion Index (Pi) indicates arterial pulse signal strength. The Pi display is green when perfusion index is greater than or equal to 0.5 while the Pi display is red when perfusion index is less than 0.5.

# What data is retrieved from the Masimo Rad-8 device?
## Special note
Do not rely on the date and time of the Masimo device. That is by far not accurate enough! Connect your computer node to a Network Time Protocol (NTP) server. Preferably a local NTP server, such as your firewall. In other words: it means that all connected computers in your network do use and should have the same time.
## Serial output of Masimo Rad-8
```
03/19/16 13:37:12 SN=0000093112 SPO2=---% BPM=--- DESAT=-- # PIDELTA=+-- ALARM=0000 EXC=000001
```
## Tags retrieved
| Serial output field name | Tags         | Type    | Description                        | Source                      | Example                |
|--------------------------|--------------|---------|------------------------------------|-----------------------------|------------------------|
| SN                       | Serialnumber | integer | Unique serial number of device     | Masimo device               | 93112                  |
|                          | SourceFQDN   | string  | Fully qualified domain name (FQDN) | Deducted from computer node | masimo_rad8.patient.local |

## Data points or field names retrieved
| Serial output field name | Field name | Value    | Value type           | Description   |
|--------------------------|------------|----------|----------------------|---------------|
| SPO2                     | SPO2       | integer  | percentage           | Saturation Percentage Oxygen |
| BPM                      | BPM        | integer  | number               | Heart beats per minute |
| *DESAT                    |  ???          | integer  | number               | ??? |
| PIDELTA                  | PI         | float    | number with decimals | Perfusion Index (Pi) indicates arterial pulse signal strength  |
| ALARM                    | Alarm      | integer  | hexidecimal number   | Value is Masimo proprietary information, deduced and translated to other field names |
| EXC                      | EXC        | integer  | hexidecimal number   | Exceptions, also translated to other field names |
| EXC1                     | EXC1       | integer  | hexidecimal number   | Exceptions |

## Alarm field names (translated)
| Translated Alarm field name | Description | Data value |
|-----------------------------|-------------|-------------|
| alm_mute_pressed            | With proper sensor data collected, press and release mute button | 0x20 being set on ALARM and cleared -> (bit 5) |
| alm_triggered               | Based on data that everytime Alarm is audible and flashing | 0x10 -> bit 4 set                                    |
| alm_bpm_low                 | BPM is lower than preset range (BPM is too low) | 0x18 -> bit 3 set                                               |
| alm_bpm_high                | BPM is higher than preset range (BPM is too high) | 0x14 -> bit 2 set                                             |
| alm_spo2_low                | Oxygen saturation is too low | 0x12 -> This should imply bit 1 is O2 lower than limit                             |
| alm_spo2_high               | Oxygen daturation is too high | 0x11 -> This should imply bit 0 is O2 higher than limit                           |
| alm_unknown                 | All other none known values. Save it for future analysis                                                          |

## Exceptions (translated)
The exceptions are displayed as a 3 digit, ASCII encoded, hexadecimal value. The binary bits of the hexadecimal value are encoded as follows:

| Translated field name   | EXC value | Description       |
|-------------------------|-----------|-------------------|
| *exc_normal_operation    | 000       | Normal operation; no exceptions |
| exc_sensor_no           | 001       | No Sensor (not connected to device) |
| exc_sensor_defective    | 002       | Defective Sensor |
| exc_low_perfusion       | 004       | Low Perfusion |
| exc_pulse_search        | 008       | Searching for pulse |
| exc_interference        | 010       | Interference |
| exc_sensor_off          | 020       | Sensor Off (not connected to patient) |
| exc_ambient_light       | 040       | Ambient Light |
| exc_sensor_unrecognized | 080       | Unrecognized Sensor |
| exc_unknown             | 100       | reserved |
| exc_unknown             | 200       | reserved |
| exc_low_signal_iq       | 400       | Low Signal IQ |
| exc_masimo_set          | 800       | Masimo SET. This flag means the algorithm is running in full SET mode. It requires a SET sensor and needs to acquire some clean data for this flag to be set. |
| exc_unknown             | nnn       | whereby nnn can be any other EXC value |

# Overall setup

# Hardware required

# Software dependencies

## Python packages

## Python virtual environment

## Ubuntu service

# Task list
## Basic
- [x] Document source code to readable text (this readme)
- [ ] Fully remove ElasticSearch and MySQL
- [x] Add Influx database support
- [x] Add/check source code for *exc_no_sensor
- [x] Add/check source code for *exc_normal_operation
- [ ] Validate exc_sensor_no
- [ ] Validate exc_normal_operation
- [ ] Add/check source code with the serial output field DESAT
- [ ] Validate DESAT
- [ ] Update source code to latest standards and security practices
- [ ] Add MQTT support
- [ ] Add Home-Assistant support
- [ ] Add Home-Assistant specific configuration files
- [ ] Add information about required hardware and setup
- [ ] Add information about software dependencies
- [ ] Add information about required software (packages)
- [ ] Describe and add required configuration files
- [ ] Describe and add service configuration files (run at startup/reboot)
- [ ] Describe use case, overall setup and dataflow

## Advanced
- [ ] Describe reference hard- and software model
- [ ] Add badges with build passed Python, Ubuntu, etc..
- [ ] Describe and add custom Internet of Medical Things (IoMT) hardware module to make the Masimo RAD-8 a true mobile/mesh data IoMT device/hub. Unfortunately RAD-8 is very old tech / pre mobile era ... heavily endorsed by the conservative (academic) health care and/or insurrance sector.
- [ ] Transition to Kubernetes (k3s)
- [ ] Explore KubeEdge
- [ ] Explore machine/deep learning and predictive analytics
- [ ] Integration with other medical devices
- [ ] Add Prometheus support

# References
1. [Masimo RAD-8](https://www.masimo.com/products/continuous/rad8/)
2. [Home-Assistant](https://www.home-assistant.io/)
3. [Influx database](https://github.com/hassio-addons/addon-influxdb)
4. [MQTT Broker](https://github.com/home-assistant/hassio-addons/tree/master/mosquitto)
5. [OpenICE](https://www.openice.info/) Java ... and ... medical devices (:grimacing:) / site is buggy
6. https://github.com/samdware Sourceforge data of OpenICE
7. http://mdpnp.mgh.harvard.edu/ Operating since 2004 / MD PnP program

# Credits
- Jeroen Baten @ http://www.jeroenbaten.nl/cardio-oxygen-saturation-monitoring-home/
- Nishanth Menon @ https://github.com/nmenon/masimo-datacapture