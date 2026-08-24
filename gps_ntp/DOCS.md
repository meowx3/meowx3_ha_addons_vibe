# GPS NTP Server

This app reads a USB GNSS receiver with GPSD and provides an NTP server with
Chrony. The receiver is configured as a trusted stratum 1 reference. GPSD
passes through all constellations reported by the receiver, including all
GLONASS satellites and identifiers supported by the receiver.

Open the **GPS NTP** panel in Home Assistant for a live sky view, receiver fix
information, likely blocked sky sectors, and Chrony source and tracking stats.
Blocked sectors are a visibility heuristic. They cannot distinguish physical
obstruction from radio interference.

## Configuration

- **GPS device path**: Leave empty for automatic detection. The app checks
  `/dev/serial/by-id/`, then `/dev/ttyACM*` and `/dev/ttyUSB*`. Set this when
  multiple receivers are attached or a fixed device is required.
- **Use Internet NTP pool**: Enable to add the configured pool servers as
  fallback references. The GNSS reference remains preferred.
- **NTP pool servers**: A list of DNS names, defaulting to `pool.ntp.org`.

The app uses host networking and serves NTP on UDP port 123. The receiver must
be visible to Home Assistant and output a supported GNSS protocol. PPS input is
not required; a receiver with PPS can improve timing when GPSD and the host
expose it.
