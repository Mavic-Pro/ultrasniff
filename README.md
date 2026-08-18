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

- **Fidzup** — An advertising presence beacon that plays an inaudible ~19 kHz tone from a shop's speakers; a phone carrying the listening SDK recognizes it and reports that the person entered that specific store. Its beacon format was publicly reverse-engineered, so UltraSniff can decode the beacon id itself, not just detect the tone.

The common thread: these tones live in a narrow band just above what most adults can hear but well within what a phone microphone captures. That makes them a **covert side-channel** — invisible to people, generally invisible to standard privacy tooling, and therefore worth being able to see for yourself.

### Hear it for yourself: real beacon recordings

You don't have to take any of this on faith. The **[Releases](../../releases/latest)** page includes **`realworld-samples.zip`** (~7 MB): eleven short recordings of **genuine** ultrasonic beacons captured in the wild, curated so you can test UltraSniff against the real thing. Play a file from a PC or phone speaker near the device running UltraSniff and watch the waterfall and detector react.

The set was chosen to cover every major technology with at least one easy case and one hard case:

| File | What it is | Why it's included |
|------|-----------|-------------------|
| `silverpush_ad_geico.wav`, `silverpush_ad_hungama.wav` | SilverPush beacons extracted from **real TV commercials** (GEICO, Hungama) | The textbook positive case: very strong, near-pure tones. Start here. |
| `shopkick_store_reno.wav`, `shopkick_store_douglas_15s.wav` | Shopkick beacons recorded with a microphone **inside real shops** of two retail chains | Two different stores = two different acoustic conditions. |
| `signal360_topchef_tv_15s.wav` | A Signal360 beacon riding inside a real **"Top Chef" TV broadcast** | The "second screen" scenario: continuous broadband signal under programme audio. |
| `lisnr_statictesttone.wav`, `lisnr_test1.wav` | A Lisnr static tone and an actual **data packet** | Data-over-audio rather than a plain tracking chirp. |
| `nearby_thought_send_clean_7s.wav` | Google Nearby transmission, captured internally on the sending phone | The clean, easy Nearby case: strong, well-defined bursts. |
| `nearby_radon_send_noisy_24s.wav` | Google Nearby captured by a microphone **at a distance** (~-72 dB) | Deliberately the hardest file in the set — a weak-signal stress test that shows where a detector's sensitivity ends. |
| `prontoly_broadcast_12s.wav` | A Prontoly emoticon broadcast (FM-style ultrasonic modulation) | A different modulation family from everything above. |
| `mixed_real_environment_16s.wav` | A real-environment recording with intermittent bursts from **multiple technologies** | The closest thing to what you'd actually encounter in the wild. |

All files are mono 16-bit WAV at 44.1 kHz — kept lossless on purpose, because lossy formats (MP3/OGG) throw away exactly the >16 kHz band these signals live in. The signals sit around 17–21.5 kHz, so most adults will hear little or nothing during playback — which is, of course, the point. One practical tip: many consumer speakers roll off sharply above ~18 kHz, so use a decent speaker at moderate distance; the SilverPush and Lisnr files are the strongest and the easiest to start with.

The recordings were collected by security and privacy researchers; full provenance, credits and license (CC BY-SA 3.0) are in the `NOTICE.txt` inside the archive.

---

## What UltraSniff does

**Reception & spectrum**
- Real-time **waterfall spectrogram** of the 15–24 kHz band, with a live peak-frequency and SNR readout.
- Decodes standard audible + ultrasonic **data-over-sound** payloads and reports the protocol.
- Extra tone/DSP decoders (narrowband, DTMF, generic 2-FSK) for signals that aren't a known protocol.
- **Unknown-signal hunter**: when a strong, sustained ultrasonic tone appears but nothing decodes, it automatically dumps the last few seconds of raw audio to a `.wav` file for offline analysis.

**Beacon & payload intelligence**
- **Ultrasonic beacon fingerprinting** — the realistic tracker detector. Instead of hoping a hidden beacon decodes to a readable string (proprietary trackers never do), it recognizes the *shape* of a beacon in the spectrum — a sparse, stable comb of strong tones — and flags likely tracking emissions, classifying common families and raising a clear **[TRACKER DETECTED]** alert.
- **Beacon decoders** for the two trackers whose wire format is publicly known — **SilverPush** and **Fidzup** — actually recover the beacon id, not just detect the tone.
- **Vendor recognition** by name across the whole known ecosystem of ultrasonic and near-ultrasonic systems (SilverPush, Fidzup, Shopkick, Lisnr, Signal360, Sonarax, CopSonic, Trillbit, ToneTag, Cue Audio and more), with passive "listen-only" recognizers that emit nothing clearly marked as such.
- Automatic payload analysis: detects and expands Base64 / HEX / JSON / URLs, estimates entropy, sniffs compression, extracts identifiers (URL / IP / MAC / UUID / IMEI / GPS / timestamps), and offers a chainable transform workbench.

**Geo & records**
- Optional **acoustic wardriving**: every detection can be tagged with GPS coordinates and plotted on a tactical map, so you can see *where* ultrasonic activity is happening.
- Everything is logged locally to CSV and a queryable database, with export to CSV / JSON / KML / GPX.

**Transmit & test (for hardware you own or are authorized to test)**
- Data-over-sound transmitter, tone / sweep / chirp generators, WAV replay, and a device-calibration sweep that measures your phone's real usable ultrasonic band.

**Countermeasures & self-awareness**
- **Ultrasonic jammer** — a defensive firewall that floods a chosen band with noise so a nearby tracking beacon can't be cleanly demodulated (acoustic only, with honest range limits shown in-app).
- **Active-transmission monitor** — shows what *your own phone* is emitting: the active audio-playback streams and, best-effort, the app behind them.

**Platform**
- Background foreground-service listening (survives a locked screen, with a persistent notification), dark "terminal" interface, English + Italian.

---

## Updates

UltraSniff keeps growing. Here's what has been added and, more importantly, why each piece matters when you're trying to see and understand the signals around you.

**A brain that learns new trackers by itself.** UltraSniff now carries a compact, purpose-built **machine-learning engine that runs entirely on your phone** — no cloud, and no audio ever leaves the device. Instead of leaning only on hand-written rules, it listens to the *shape* of an ultrasonic emission and recognizes which tracker family it belongs to, with a calibrated confidence. More importantly it is **open-set**: when it meets a pattern that matches nothing it knows, it doesn't shrug it off — it groups those unknowns, and once the same new signal keeps reappearing it **discovers a brand-new family on its own** and writes a fresh signature for it. Those discoveries are **shared**: a newly found signature is uploaded to a community catalog, and every copy of UltraSniff quietly **pulls the latest catalog** so the whole userbase benefits from what any one person runs into — your own tracker database grows from the entire community's encounters, not just yours. To keep false alarms down, a merely *guessed* tracker has to reappear before it raises an alert, while a fully decoded beacon id still alerts at once.

**Name the tracker, and fight back.** Reception now recovers the beacon id of a second commercial tracker whose format is public — **Fidzup**, the ~19 kHz shop-entrance beacon — joining SilverPush as one that UltraSniff decodes rather than merely detects. Recognition was widened to the whole known ecosystem of ultrasonic and near-ultrasonic vendors (Sonarax, CopSonic, Trillbit, ToneTag, Cue Audio and more), with the passive "listen-only" systems that emit nothing honestly marked as such so you're never told a spectrum scanner can catch something it physically can't. Two new active tools round it out: an **ultrasonic jammer** that floods a band with noise so a nearby beacon can't be demodulated, and an **active-transmission monitor** that shows what your own phone is emitting and, as far as Android allows, which app is behind it.

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

**Catch the trackers that hide between the tones.** Not every beacon is a chord of tones played at once. Some send a *melody* — one tone at a time, hopping from note to note — and a detector that only looks for a stable comb walks right past them. UltraSniff now watches the peak over time as well, so a fast run of distinct ultrasonic notes is flagged for what it is. And every beacon it catches, comb or melody, is reduced to a compact **fingerprint** of its tones. That fingerprint turns scattered sightings into a story: the app can now **correlate the same emitter across time and places** — the identical beacon seen in one shop on Monday and another on Friday lines up under one id. Combs it has never seen before, if they keep reappearing, are quietly **learned** and given a name, so your personal tracker database grows itself from what you actually encounter — and you can export it to share, or import someone else's.

**Sharper ears, and payloads that give themselves up.** The receiver now analyses overlapping slices of sound rather than back-to-back chunks, so short bursts are no longer smeared or missed, and every tone is pinned to a more exact frequency. When a strong signal appears and nothing standard decodes it, UltraSniff automatically tries to pull raw bits out of it with on-off and Manchester demodulation, and links whatever it recovers back to the recording it came from. For payloads you're taking apart by hand, a new **auto-solver** searches decode chains for you — un-hexing, un-Base64-ing, decompressing, XOR-ing — and stops when it finds something readable.

**Yours to keep, yours to erase.** Detections are stored with far more detail now — the exact recovered bytes and a checksum, the emitter fingerprint, fix quality, and a link to any captured audio — while staying fully local. You choose how much location precision to keep (down to none) and how long to hold history before it auto-purges, and a real **wipe** clears everything, the geo log included. Records export to more formats — including **GeoJSON** and a **WiGLE**-style CSV — with proper UTC timestamps, so your survey drops cleanly into mapping and analysis tools.

**Smoother in the field, and self-updating.** Detector settings now take effect **live**, without stopping and restarting reception, so you can tune sensitivity while you hunt. The waterfall gained hands-on controls — brightness, a freeze to study a moment, and a tunable low edge to zoom the band. And because UltraSniff is distributed as an APK rather than through a store, it can now **check for and install its own updates** straight from its release page, so you always have the latest version.

**Sharper recognition, right on the phone.** UltraSniff now reads the ultrasonic band at much finer resolution and pairs it with a retrained on-device model, so the tone combs that tell one beacon family from another are actually resolved instead of blurred together. That model leads family identification for the emitters that can only be fingerprinted, while the SilverPush and Fidzup decoders stay in charge whenever a real beacon id can be recovered. Frequency-hopping emitters get first-class treatment too: a stable fingerprint of the grid they hop across, a read-out of their hop rate and symbol stream, and dependable recognition of the wide-roaming "spread" signals that older detectors let slip. Under the hood there's a new signal-processing toolkit — matched-filter correlation, analytic-signal envelope and instantaneous-frequency, and chirp, frequency-hopping and framed transmit generators — alongside steadier demodulation with symbol-timing recovery and soft decisions. And a new multi-slide walkthrough introduces every tool up front, including how to test with quality headphones or speakers (ordinary PC speakers can't reach the ultrasonic band; real beacons in the field are unaffected).

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
- **Internet** — to load map tiles and to check its release page for updates.
- **Install unknown apps** *(optional)* — only used if you let UltraSniff install an update it downloaded for you.

---

## Responsible use

UltraSniff is built for privacy research, education, and defensive awareness. Use it to understand and inspect the ultrasonic environment around **you**, and use its transmit/test features only on devices and signals you own or are explicitly authorized to work with.
