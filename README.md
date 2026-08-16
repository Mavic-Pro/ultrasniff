# UltraSniff SIGINT

**An Android toolkit that detects, visualizes, decodes, and maps the inaudible ultrasonic signals around you — including the covert "ultrasonic beacons" used to track people across their devices.**

Most people never learn that the air around them can carry hidden data. Advertising, television, retail stores, and mobile apps have all experimented with sound *above the range you can hear* — roughly 18–22 kHz — to move small pieces of information between a speaker and a nearby phone microphone. You can't hear it, but your phone can. UltraSniff turns that invisible layer into something you can actually see, read, and record.

---

## Why this app exists

Ultrasonic signaling has a legitimate side (offline data transfer, proximity pairing, second-screen sync). But the same physics enables a privacy problem that is almost impossible to notice with the naked ear: **cross-device tracking through inaudible sound.**

The idea is simple and effective. A TV commercial, a web page, or a shop's PA system plays a high-frequency tone that humans can't hear. An app on your phone — one that quietly bundled a tracking SDK — is always listening for that tone. When it "hears" the beacon, it silently links your phone to that TV, that ad, that physical location, and to any other device that heard the same sound. The result is a profile that follows you across screens and spaces, built from signals you were never aware of and never consented to.

The problem is that this channel is **designed to be imperceptible**. It sits just above human hearing, so no one notices it. It usually isn't logged, isn't shown to the user, and isn't inspected by ordinary privacy tools that focus on the network. UltraSniff exists to close that blind spot: to give a curious or privacy-conscious person a way to **observe the ultrasonic spectrum directly**, catch beacons in the act, decode what they can, and keep a record of where and when they appeared.

This is a **counter-surveillance / signal-analysis** tool. It listens only on your own device's microphone, in your own environment. It is deliberately tuned *above* the human-voice band and ignores everything below ~15 kHz, so it is a data-tone monitor — **not** a speech recorder. When it runs in the background it shows a permanent notification: it is disclosed, never hidden.

---

## Real-world examples of ultrasonic tracking

This is not hypothetical. Ultrasonic side-channels have been deployed commercially for years:

- **SilverPush** — Popularized "unique audio beacons": inaudible tones embedded in television commercials and in-app advertising. A phone running an app that contained their listening SDK would recognize the beacon and silently tie the TV, the ad, and the phone to a single identity for cross-device tracking and ad attribution — with no visible indication to the user. Regulators took notice: consumer-protection authorities formally warned app developers about shipping this kind of undisclosed audio tracking. Independent security research later found **hundreds of Android apps** in mainstream app stores carrying ultrasonic-tracking listeners.

- **Shopkick** — A shopping-rewards app that uses inaudible ultrasonic signals broadcast by small in-store transmitters to confirm that a shopper has physically walked into a store, then awards points. In effect, a presence/attendance beacon that verifies your body was in a specific place.

- **Lisnr** — "Smart tones": a data-over-audio system that encodes information in near-ultrasonic sound for proximity, ticketing, payments, and attribution use cases — turning ordinary speakers into short-range data transmitters.

- **Signal360 (formerly Sonic Notify)** — Ultrasonic (and Bluetooth) beacons used for proximity marketing and audience analytics, e.g. triggering content, ads, or measurement when a device is near a venue or a broadcast.

The common thread: these tones live in a narrow band just above what most adults can hear but well within what a phone microphone captures. That makes them a **covert side-channel** — invisible to people, generally invisible to standard privacy tooling, and therefore worth being able to see for yourself.

---

## What UltraSniff does

**Reception & spectrum**
- Real-time **waterfall spectrogram** of the 15–24 kHz band, with a live peak-frequency and SNR readout.
- Decodes standard audible + ultrasonic **data-over-sound** payloads and reports the protocol.
- Extra tone/DSP decoders (narrowband, DTMF, generic 2-FSK) for signals that aren't a known protocol.
- **Unknown-signal hunter**: when a strong, sustained ultrasonic tone appears but nothing decodes, it automatically dumps the last few seconds of raw audio to a `.wav` file for offline analysis.

**Beacon & payload intelligence**
- **Ultrasonic beacon fingerprinting** — the realistic tracker detector. Instead of hoping a hidden beacon decodes to a readable string (proprietary trackers never do), it recognizes the *shape* of a beacon in the spectrum — a sparse, stable comb of strong tones — and flags likely tracking emissions, classifying common families and raising a clear **[TRACKER DETECTED]** alert.
- Automatic payload analysis: detects and expands Base64 / HEX / JSON / URLs, estimates entropy, sniffs compression, extracts identifiers (URL / IP / MAC / UUID / IMEI / GPS / timestamps), and offers a chainable transform workbench.

**Geo & records**
- Optional **acoustic wardriving**: every detection can be tagged with GPS coordinates and plotted on a tactical map, so you can see *where* ultrasonic activity is happening.
- Everything is logged locally to CSV and a queryable database, with export to CSV / JSON / KML / GPX.

**Transmit & test (for hardware you own or are authorized to test)**
- Data-over-sound transmitter, tone / sweep / chirp generators, WAV replay, and a device-calibration sweep that measures your phone's real usable ultrasonic band.

**Platform**
- Background foreground-service listening (survives a locked screen, with a persistent notification), dark "terminal" interface, English + Italian.

---

## Updates

UltraSniff keeps growing. Here's what has been added and, more importantly, why each piece matters when you're trying to see and understand the signals around you.

**Hear more, miss less — the decoder bank.** Reception no longer runs a single decoder. It runs a *bank* of them in parallel: every known data-over-sound protocol at once, several copies tuned to slightly shifted base frequencies (so a transmitter that is a little off-frequency or deliberately detuned still lines up), and a spread-spectrum variant. In practice this means a signal gets caught even when it doesn't sit exactly where a textbook says it should — and when one does decode, the log tells you how far it was shifted.

**Decode anything, offline.** Some signals won't give themselves up in real time. Any captured `.wav` — including the ones the Signal Hunter grabs automatically — can be run through an exhaustive sweep that tries every protocol across a fine grid of frequency shifts and spread modes, with no time limit, plus a touch-tone (DTMF) pass. This is the "leave no stone unturned" mode: it trades speed for the best possible chance of recovering a payload. It runs as a background job and drops the result into a notification, so you can start it and walk away.

**More kinds of signal.** Beyond the built-in data-over-sound protocols, UltraSniff now understands classic modulation schemes — OOK/ASK (on-off keying), FSK, and Manchester coding — and can auto-estimate their carrier and bit rate. A **demod workbench** lets you point those decoders at a recording and dial in the carrier, spacing, and baud by hand, which is exactly what you want when reverse-engineering an unknown tone you spotted on the waterfall.

**Transmit anything.** The transmitter is now a full companion to the receiver: it can send not just the standard data-over-sound protocols but also OOK, FSK, DTMF, and Manchester at frequencies and bit rates you choose. That makes UltraSniff a two-way tool — generate a known signal to test a receiver you own, or to check what your own detector catches.

**Cleaner reception.** An optional band-pass + automatic-gain pre-filter strips out-of-band noise and lifts weak ultrasonic tones before decoding. Imported recordings that aren't at the engine's sample rate are automatically resampled so their tones land where the decoders expect. And a rolling 30-second buffer means you can **save the last 30 seconds** of what the mic heard at any moment — useful for grabbing something the instant you notice it.

**Make sense of what you find.** Detections can be grouped into a **timeline / correlation** view that shows repeated emissions ("seen 12 times, first…last"), so a one-off blip is easy to tell apart from something that keeps reappearing. You can set an **alert rule** — a text pattern that buzzes the phone and raises a notification the moment a matching payload is decoded — and export a shareable **report** of everything you've collected. Tapping the waterfall now reads out the exact frequency and level at that point.

**Keep the tracker database current.** The ultrasonic-beacon fingerprints are no longer frozen in the app. You can **import a signature pack** (a small JSON file) to teach UltraSniff new beacon families as they're discovered — no update required.

**On the map.** Geo-tagged detections can be viewed as individual markers, **clustered** by area, or as a **heatmap** that makes dense zones obvious at a glance. A **time slider** replays your survey over time, an optional **track** line traces your path, and there's a lightweight **geofence** alert that warns you when you're back in a place where an ultrasonic tracker was seen before.

**Runs the way you want.** Handy notification actions (including a one-tap "save 30 s"), an optional restart-after-reboot, and a short first-run walkthrough that points you at the on-device calibration so you know your phone's real ultrasonic range from the start.

**Smaller downloads.** Releases now ship a separate, signed APK per CPU type in addition to the universal build, so most phones download roughly half the size.

## Download & install

Grab the latest APK from the **[Releases](../../releases/latest)** page and sideload it (you may need to allow "install from unknown sources" for your browser or file manager).

Pick the variant for your device:

| APK | Install on |
|-----|-----------|
| `app-arm64-v8a-release.apk` | **Most modern phones** (64-bit ARM) — recommended |
| `app-armeabi-v7a-release.apk` | Older 32-bit ARM devices |
| `app-x86_64-release.apk` | x86-64 devices / emulators |
| `app-universal-release.apk` | One file that runs on any of the above (larger) |

Requirements: Android 8.0 (API 26) or newer. If you don't know your CPU type, use the **universal** APK.

All release variants are signed with the same key, so future updates install cleanly over an existing installation without losing your data.

---

## Permissions & what they're for

- **Microphone** — the core function: listening for ultrasonic signals on your own device. UltraSniff only analyzes content above ~15 kHz; it is not a voice recorder.
- **Location** *(optional)* — only used to geo-tag detections for the map if you enable wardriving.
- **Notifications** — required to show the persistent "listening" notification while the engine runs in the background.
- **Internet** — only to load map tiles.

---

## Responsible use

UltraSniff is built for privacy research, education, and defensive awareness. Use it to understand and inspect the ultrasonic environment around **you**, and use its transmit/test features only on devices and signals you own or are explicitly authorized to work with.
