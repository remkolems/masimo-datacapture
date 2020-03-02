`masimo-datacapture`
=======
This is a basic program to capture data from Masimo Brand Pulse Oximeter and store the data in an influx database.
(ToDo) Furthermore, data is populated in a Home-Assistant instance via a MQTT gateway for additional functionalities.

# Masimo Rad-8
See https://www.masimo.com/products/continuous/rad8/

The Masimo Rad-8 needs to be set in ASCII mode 1. By default the device is set to ASCII mode 2 for the serial output. Hold down the Enter Button and the Down Button for 5 seconds to change the parameter. See [manual](https://github.com/remkolems/masimo-datacapture/blob/master/Masimo Rad-8/Masimo Rad-8 Operator's Manual.pdf) page 4-14 (section 4 Setup menu level 3).

## Front
Masimo Rad-8/masimo-rad8_front.png
## Back
Masimo Rad-8/masimo-rad8_backpanel.png
## Display
Masimo Rad-8/masimo-rad8_display.png

A) Signal I.Q.® (SIQ) bar is a signal quality indicator. The LED rises and falls with the pulse, its height indicating signal quality. When Signal IQ is low, the display turns red.
B) The Alarm Status Indicator flashes when an alarm condition is present.
C) Perfusion Index (Pi) indicates arterial pulse signal strength. The Pi display is green when perfusion index is greater than or equal to 0.5 while the Pi display is red when perfusion index is less than 0.5.