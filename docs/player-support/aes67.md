---
title: AES67 Multicast Stream Player Provider
description: Details for the AES67 Multicast Stream Player Provider
---

# AES67 Multicast Stream

Music Assistant supports streaming audio to professional AES67/RAVENNA/Dante (AES67 mode) receivers via IP multicast. This provider implements standards-compliant RTP/RTCP (RFC 3550) for audio transport and SAP/SDP (RFC 2974, RFC 4566) for automatic stream discovery. This component is contributed and maintained by [apophisnow](https://github.com/apophisnow).

AES67 is a technical standard for high-performance audio-over-IP interoperability, widely used in professional audio, broadcast, and commercial installation environments. It ensures seamless communication between devices from different manufacturers that support the standard, including RAVENNA, Dante (when in AES67 mode) equipment, and software players like VLC Media Player.

## Features

- Standards-compliant AES67 multicast streaming (RFC 3550, RFC 2974, RFC 4566)
- Automatic stream discovery via SAP/SDP announcements
- Professional-grade audio quality: up to 96kHz sample rate and 24-bit depth
- QoS (Quality of Service) support via DSCP marking for network prioritization
- Compatible with AES67, RAVENNA, Dante (AES67 mode) receivers, and software players like VLC
- Multi-instance support - create multiple independent multicast streams
- Low-latency streaming optimized for professional applications

## Settings

The AES67 provider supports multi-instance configuration, allowing you to create multiple independent multicast streams, each with its own configuration. When adding a new instance, Music Assistant will automatically suggest a unique multicast address and stream name.

### Basic Settings

- **Stream Name.** Name for this AES67 stream (will appear as a player in Music Assistant). Default: `AES67 Stream` (incremented for additional instances)
- **Multicast Address.** IPv4 multicast address for the stream. Default: `239.69.83.x` (auto-incremented for each instance). The 239.69.x.x range is recommended for AES67 as per the standard

### Advanced Settings

- **RTP Port.** UDP port for RTP packets (RTCP will use port+1). Default: `5004`. The AES67 standard recommends using ports in the range 5004-5127
- **Sample Rate (Hz).** Audio sample rate. Default: `48000` Hz (recommended by AES67 standard). Supported rates: 44100, 48000, 88200, 96000 Hz
- **Bit Depth.** Audio bit depth (16 or 24 bits). Default: `24` bits (recommended for professional use)
- **Channels.** Number of audio channels. Default: `2` (stereo)
- **Multicast TTL.** Time-to-live for multicast packets. Default: `32` (site-local). Options: 1 (subnet only), 32 (organization), 255 (global - use with caution)
- **DSCP (QoS).** Differentiated Services Code Point for Quality of Service. Default: `34` (Expedited Forwarding, recommended for professional audio per AES67 specification)
- **Enable SAP/SDP Announcements.** Broadcast stream information via SAP for automatic discovery by receivers. Default: `enabled`. When enabled, receivers can automatically discover and connect to the stream

## Setup

1. Navigate to Settings → Providers in Music Assistant
2. Click the `+` button to add a new provider
3. Select `AES67 Multicast Stream` from the list
4. Configure the stream settings:
   - Enter a descriptive stream name
   - Verify or adjust the multicast address (239.69.x.x range recommended)
   - Adjust advanced settings if needed for your network environment
5. Click `SAVE` to create the stream

To create additional streams, simply add another instance of the AES67 provider with a different multicast address and port combination.

## Receiver Configuration

### Auto-Discovery (SAP/SDP)

If your AES67 receiver supports SAP/SDP discovery and this feature is enabled in Music Assistant:

1. The receiver should automatically detect the Music Assistant stream
2. Look for the stream name you configured in your receiver's stream list
3. Select the stream to start receiving audio

### VLC Media Player (Testing and Playback)

VLC Media Player is an excellent way to test and play AES67 streams on any computer:

**Auto-Discovery:**

1. Open VLC Media Player
2. Navigate to `View` → `Playlist` (or press Ctrl+L / Cmd+L)
3. In the left sidebar, expand `Network streams (SAP)`
4. Your Music Assistant stream should appear by name
5. Double-click the stream to start playback

**Manual Connection:**

If the stream doesn't auto-discover, you can connect manually:

1. In VLC, go to `Media` → `Open Network Stream` (or press Ctrl+N / Cmd+N)
2. Enter the network URL in format: `rtp://@239.69.83.0:5004` (replace with your multicast address and port)
3. Click `Play`

### Professional AES67 Receivers (Manual Configuration)

If your professional receiver requires manual stream configuration:

1. Configure the receiver to listen to the multicast address you specified (e.g., `239.69.83.0`)
2. Set the RTP port (default: `5004`)
3. Configure audio format:
   - Sample rate: as configured (default 48000 Hz)
   - Bit depth: as configured (default 24-bit)
   - Channels: as configured (default 2/stereo)
   - Format: L24 (24-bit linear PCM) or L16 (16-bit linear PCM)

## Network Requirements

- **Multicast Support.** Your network infrastructure (switches, routers) must support IP multicast and have IGMP (Internet Group Management Protocol) enabled
- **Network Bandwidth.** Ensure sufficient bandwidth for uncompressed audio:
  - 48kHz/24-bit/stereo: approximately 2.3 Mbps
  - 96kHz/24-bit/stereo: approximately 4.6 Mbps
- **Firewall/Ports.** Ensure UDP ports for RTP (default 5004) and RTCP (default 5005) are not blocked
- **SAP Discovery.** If using SAP/SDP announcements, ensure UDP port 9875 and multicast address 239.255.255.255 are not blocked
- **Multicast Routing.** If receivers are on different network segments, ensure multicast routing is properly configured

## Known Issues / Notes

- This is a send-only provider - Music Assistant streams audio to AES67 receivers but does not receive streams
- The provider creates a virtual player in Music Assistant that represents the multicast stream
- All receivers listening to the same multicast address will receive identical audio (synchronized playback)
- Volume control is not supported as AES67 multicast streams do not have individual receiver control
- For Dante devices, ensure they are in AES67 mode - not all Dante devices support this mode
- Network congestion or insufficient multicast support may cause audio dropouts
- PTP (Precision Time Protocol) synchronization is handled by the receiver - ensure your network has a PTP master clock for best synchronization
- The multicast address should be unique for each stream on your network
- Using a TTL value higher than necessary may cause streams to propagate beyond your intended network

## Troubleshooting

- **Testing stream connectivity:** Use VLC Media Player to verify the stream is working before troubleshooting professional receivers. If VLC can play the stream, the issue is likely with the receiver configuration
- **No audio at receiver:** Verify multicast address, port, and audio format settings match between Music Assistant and receiver
- **Stream not discovered in VLC or receivers:** Check that SAP/SDP announcements are enabled in Music Assistant settings and that UDP port 9875 and multicast address 239.255.255.255 are not blocked by firewall
- **Audio dropouts:** Check network for multicast support and sufficient bandwidth. Consider increasing buffer settings on the receiver if available
- **Multiple streams interfering:** Ensure each Music Assistant AES67 instance uses a unique combination of multicast address and port
- **QoS not working:** Verify network switches support DSCP marking and have QoS policies configured
- **VLC shows stream but no audio:** Check that the stream is actively playing in Music Assistant - the virtual player must be in a playing state to send audio
