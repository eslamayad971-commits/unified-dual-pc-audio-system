# Unified Dual-PC Audio System

### Real-Time Network Audio Mixing, Routing & Automation

A software-based dual-computer audio system that allows a single wired headset to play audio from two independent Windows 11 computers, individually or simultaneously, over a local Wi-Fi network.

This project documents the design, configuration, troubleshooting, and testing of a practical network audio solution using Voicemeeter Banana, VBAN, and custom keyboard shortcuts.

## The Problem

I regularly use a desktop PC and a laptop together, controlled with one keyboard and mouse through Deskflow.

However, I wanted to use my HyperX Cloud III wired headset, which remains permanently connected to the desktop, to listen to audio from either computer without unplugging cables, buying an audio mixer, or switching headsets.

## The Solution

I configured the desktop as a central audio mixer and used VBAN to transmit laptop audio over the local network.

- Laptop audio is sent through Voicemeeter's B1 bus using VBAN.
- The desktop receives the stream on a separate mixer input.
- Desktop audio and laptop audio are mixed locally through Voicemeeter Banana.
- Both audio sources are routed to the HyperX Cloud III headset.
- Voicemeeter Macro Buttons provides three listening modes controlled by keyboard shortcuts.

## System Architecture

```text
              LAPTOP (Windows 11)
                Applications
                     |
          Voicemeeter Virtual Input
                     |
             B1 Audio Bus
                     |
               VBAN Sender
                     |
               Wi-Fi 6 LAN
                     |
               VBAN Receiver
                     |
              DESKTOP PC (Windows 11)
                     |
          Voicemeeter Hardware Input 1
                     |                  Desktop Applications
                     |                          |
                     |                 Voicemeeter Virtual Input
                     |                          |
                     +-----------+--------------+
                                 |
                           Mixer Output A1
                                 |
                       HyperX Cloud III USB
```

## Hardware and Software

| Component | Configuration |
|---|---|
| Desktop OS | Windows 11 |
| Laptop OS | Windows 11 |
| Headset | HyperX Cloud III Wired (USB) |
| Network | Wi-Fi 6, 5 GHz, same local access point |
| Audio mixer | Voicemeeter Banana |
| Network audio transport | VBAN |
| Automation | Voicemeeter Macro Buttons |
| Keyboard and mouse sharing | Deskflow (existing setup, unchanged) |

## Audio Modes and Keyboard Shortcuts

| Mode | Shortcut | Desktop audio | Laptop audio |
|---|---|---|---|
| PC Only | Shift + F10 | ON | OFF |
| Laptop Only | Shift + F11 | OFF | ON |
| Both | Shift + F12 | ON | ON |

The shortcuts operate on the desktop mixer and switch which sources reach the headset.

## Macro Buttons Configuration

### PC Only
```text
Strip[0].A1 = 0;
Strip[3].A1 = 1;
```

### Laptop Only
```text
Strip[0].A1 = 1;
Strip[3].A1 = 0;
```

### Both Computers
```text
Strip[0].A1 = 1;
Strip[3].A1 = 1;
```

These commands correspond to the particular Voicemeeter Banana strip layout used in this configuration.

## Independent Audio Routing

The laptop uses separate Voicemeeter buses:

- **A1:** Laptop's Realtek speakers (local monitoring).
- **B1:** Audio sent to the desktop through VBAN.

This allows laptop speaker playback to be enabled or disabled independently of network transmission.

On the desktop, laptop audio and desktop audio enter different mixer strips, allowing independent volume adjustment and source switching.

## Network Configuration

- VBAN uses UDP port 6980.
- Audio is transmitted at 48 kHz, stereo, PCM 16-bit.
- Both computers are connected to the same 5 GHz Wi-Fi access point.
- The setup uses local IP addresses, which should be adapted for other networks.

## Testing and Troubleshooting

During development, several issues were investigated and resolved:

1. **Desktop audio was silent:** Identified an incorrect Windows default audio output and routed playback through Voicemeeter's virtual input.
2. **Laptop audio was not arriving:** Verified the VBAN receiver stream name, destination, and sender IP configuration.
3. **Intermittent audio cutouts:** Tested VBAN network quality settings and checked Wi-Fi latency using a 100-packet ping test.
4. **Laptop speakers and headset played simultaneously:** Separated local speaker monitoring (A1) from VBAN transmission (B1).
5. **Manual switching was inconvenient:** Created three Voicemeeter Macro Buttons with keyboard shortcuts.
6. **Startup behavior needed verification:** Saved mixer, macro, and VBAN configurations and tested application startup and audio reconnection after restarting Windows.

## Test Results

- Audio from both computers was successfully mixed into a single headset.
- All three listening modes were tested successfully.
- Macro shortcuts were tested successfully.
- The laptop's network audio connection resumed after restarting Windows.
- Both Voicemeeter and Macro Buttons can run with their windows hidden.
- A 100-packet laptop-to-desktop ping test showed 0% ICMP packet loss, 4 ms average round-trip time, and 56 ms maximum round-trip time.

**Note:** ICMP ping results do not measure end-to-end audio latency or prove zero VBAN packet loss. Some VBAN error indicators were observed during testing; long-term stability remains an area for further evaluation.

## Limitations and Future Improvements

- Audio streaming quality depends on local network stability.
- The current configuration shares playback audio from the laptop to the desktop; headset microphone sharing is not configured.
- Future improvements may include microphone routing, more detailed latency measurements, and enhanced automation.
- Private network addresses and machine-specific settings are intentionally omitted from this public documentation.

## Project Scope

This is a systems integration and configuration project built with existing third-party software. The project work includes architecture design, network/audio routing, debugging, functional testing, and automation—not development of Voicemeeter, VBAN, or Deskflow themselves.

## Software References

- [Voicemeeter Banana](https://vb-audio.com/Voicemeeter/banana.htm)
- [VBAN](https://vb-audio.com/Voicemeeter/vban.htm)
- [Deskflow](https://github.com/deskflow/deskflow)
