# UxPlay Context

UxPlay is an open-source implementation of an AirPlay mirroring and audio receiver. It enables non-Apple systems to act as AirPlay server destinations for screen sharing, media streaming, and audio playback from Apple clients.

## Language

### Core Components

**Server**:
The UxPlay application instance running on a host machine that advertises itself and receives incoming streams.
_Avoid_: Receiver, target, host process

**Client**:
The Apple device (e.g., iPhone, iPad, Mac) or emulator that initiates the AirPlay connection and transmits data.
_Avoid_: Sender, source device

### Network & Discovery

**DNS-SD**:
A network service discovery protocol used to publish and locate services (such as UxPlay) on a local area network using multicast DNS.
_Avoid_: Bonjour, Avahi, Zeroconf, mDNS (unless referring specifically to the protocol or implementation rather than service discovery itself)

**Beacon**:
A Bluetooth Low Energy (LE) advertising service that broadcasts the Server's IP address and TCP/UDP ports to Clients, used primarily on networks where multicast DNS-SD is blocked.
_Avoid_: Bluetooth advertiser, BT scanner

### Streaming & Playback Modes

**Mirroring**:
A connection mode where the Client's entire screen and audio are captured, encoded, and sent lossily (H.264 video and AAC audio) to the Server.
_Avoid_: Casting, screen mirroring (as a generic verb), projection

**Audio-only**:
A connection mode where the Client streams audio content (e.g., from Apple Music) as Apple Lossless (ALAC) audio with metadata and cover art, without sending screen video.
_Avoid_: Lossless streaming, RAOP stream

**HLS Streaming**:
A streaming mode where the Client hands off a media URL (HTTP Live Streaming m3u8) to the Server, allowing the Server to fetch and decode the media stream directly from the network rather than through the Client.
_Avoid_: URL handoff, direct player

### Security & Pairing

**Legacy Pairing**:
A fallback Apple-pairing security mechanism allowing legacy Apple devices or pairing without modern encryption.
_Avoid_: Simple pairing

**PIN/Password Pairing**:
An access control system requiring the Client to submit a Server-generated on-screen PIN or a pre-configured password before establishing a stream.
_Avoid_: Auth code, client validation

## Flagged Ambiguities

**AirPlay**:
Historically, the term AirPlay is used for audio streaming, mirroring, and video streaming. In UxPlay, we distinguish between **Mirroring** (real-time screen sharing), **Audio-only** (ALAC audio streaming), and **HLS Streaming** (media URL playback) to avoid confusion.

## Example Dialogue

**Developer**: "If a Client is on a corporate Wi-Fi network that blocks multicast packets, how does it discover our Server?"
**Domain Expert**: "In that scenario, the user runs the **Beacon** on the Server machine. The **Beacon** broadcasts the Server's network details over Bluetooth LE. The **Client** hears these advertisements and connects directly to the Server's IP and port, bypassing the need for standard network **DNS-SD**."

**Developer**: "When a user streams a YouTube video using the YouTube app, does it use **Mirroring**?"
**Domain Expert**: "By default, it would use **Mirroring**, but if the `-hls` option is enabled on the Server, the Client switches to **HLS Streaming** and sends the playlist URL to the Server. The Server then decodes the HLS stream directly, which yields much better performance than transcoding the screen in **Mirroring** mode."
