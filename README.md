# Sensor Protocol for Eddystone-URL

<img src="https://github.com/google/eddystone/blob/master/branding/assets/png/EddyStone_final-01.png" alt="Eddystone logo" width="300px" align="middle">

This specification is designed to offer simple and flexible way to broadcast different type of sensor values in an [Eddystone-URL](https://github.com/google/eddystone/tree/master/eddystone-url) frame. One possible usage scenario would be:

> [RuuviTag](http://ruuvitag.com) sensor beacon broadcasts an encoded URL address in an Eddystone-URL frame: `http://ruu.vi#53WG3vW`. Once user visits the link, **ruu.vi** website decodes the value `53WG3vW` and shows the data in a human-readable format. Please note that ruu.vi website rounds the data. 

[![Ruuvi Measurements](https://github.com/ruuvi/sensor-protocol-for-eddystone-url/blob/master/images/website.png)](http://ruuvi.com)

The data part of the URL can be encoded in firmware of the beacon. The most powerful way to encode the data would be Base94 because the URL field of the Eddystone-URL has a support for 94 different characters. Normally it's mandatory to encode the data because of maximum length (18 characters) of the [Eddystone-URL](https://github.com/google/eddystone/tree/master/eddystone-url) frame's URL field. We are using URL-safe Base64 for support across as many devices as possible.

## Protocol Specification (Data Format 2)

The decoded data is a packet of bytes. First byte defines what kind of data the field contains.

Offset | Allowed values | Description
-----|:-----:|-----------
 0 | `0-255` | Data format definition (2 = current sensor readings)
 1 | `0-200` | Humidity (one lsb is 0.5%, e.g. 128 is 64%)
 2 | `0-127, signed` | Temperature (MSB is sign, next 7 bits are decimal value)
 3 | `0-99` | Temperature (fraction, 1/100.)
 4 | `0-9` | Pressure (MSB - 50kPa)
 5 | `0-9` | Pressure (LSB)

### Temperature
Values supported: -127.99 °C to +127.99 °C in 0.1 °C increments.
####Example
Value | Measurement
----|-----------
 `0x0000` | 0 °C
 `0x8145` | -1.45 °C
 `0x0145` | +1.45 °C

### Humidity
Values supported: 0.0 % to 100 % in 0.5 % increments.
####Example
Value | Measurement
----|-----------
 `000` | 0%
 `350` | 35.5%
 `999` | 99.9%

### Atmospheric Pressure
Values supported: 50000 Pa to 115536 Pa in 1 Pa increments.
####Example
Value | Measurement
----|-----------
 `00000` | 50000 Pa
 `41325` | 101325 Pa (average sea-level pressure)
 `65536` | 115536 Pa
 


### Data Format (Offset 0)
The first byte tells the receiver (ie. website) what kind of type of data the URL has. Only the first one `(1)` is implemented so far.

Decimal | Description
----|-----------
 1 | Historical use, not supported anymore. 
 2 | Described here
 3 | Reserved for future use
 4 | Reserved for future use
 5 | Reserved for future use
 6 | Reserved for future use
 7 | Reserved for future use
 8 | Reserved for future use
 9 | Reserved for future use
 
