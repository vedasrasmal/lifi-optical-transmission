# LiFi — Optical Data and Audio Transmission

Data and audio sent across a room as amplitude-modulated light — no wires and no radio
— using a transistor-driven laser diode and LED transmitter and a photodiode receiver
with speaker output.

In one line: the signal controls the brightness of the light, and the receiver turns
the brightness changes back into sound.

Author: **Vedas Omprakash Rasmal** — B.Tech, Electronics and Telecommunication
Engineering (3rd year), G H Raisoni College of Engineering and Management, Wagholi,
Pune. Coursework project, previous semester.

Full project report (5 pages): [docs/lifi-project-report.pdf](docs/lifi-project-report.pdf)

## Recorded results

Both **data and audio** were transferred using the laser **and** the LED.

| Link | Recorded result |
| --- | --- |
| LED | Up to **5 m in the dark**, and **1.5–2 m in light** (ambient light) |
| Laser | Tested up to **10 m** — testing stopped there, so that is **not a maximum range** |

**Projected range (not measured):** a 30 mW laser was projected to reach 500 m; the
5 mW laser actually used, chosen for safety, was projected to reach up to 50 m. Neither
projected figure was tested; only 10 m was tested.

No bandwidth, signal-to-noise or fidelity figures were recorded. The 5 mW and 3 W
figures are component ratings, not measured optical output.

## How it works

LiFi (Light Fidelity) systems carry information on light instead of radio waves. This
project applies that idea to data and to an analogue audio signal: the signal
amplitude-modulates the emitter current, so the light rises and falls in brightness
with the signal, and a photodiode receiver converts it back — heard as sound in the
audio case.

The link is a single analogue chain with no digital encoding at any point. Each stage
hands a varying signal to the next, and the audio information is carried first as
voltage, then as current, then as light intensity, and then back through the same
forms in reverse.

```
Audio source → BD139 driver → AM of emitter current → 5 mW laser diode / 3 W LED
        → free-space channel (modulated light beam)
        → BPW34 photodiode → LM358 conditioning → PAM8403 amplifier → Speaker
```

The audio waveform is never digitised: its amplitude is the message at every stage.

### Amplitude modulation

In amplitude modulation, the information sits in the size of the signal rather than
in its timing. Here the audio waveform is combined with the steady drive current of
the emitter, so the instantaneous current — and with it the brightness of the emitted
light — follows the audio. Loud passages produce stronger swings in brightness and
quiet passages produce weaker ones, while the average brightness stays set by the
steady bias current.

At the receiver the process reverses. The photodiode current follows the received
light intensity, so the brightness swings reappear as a small varying electrical
signal with the same waveform shape as the original audio. Conditioning and power
amplification then raise that signal first to a clean line level and then to the level
a speaker needs, without changing what the waveform represents.

Because the whole chain is analogue, alignment matters at every moment of operation:
the receiver has to sit in the light path, and anything that blocks or deflects the
beam interrupts the audio. That directness is also what makes the project a clear
teaching vehicle for modulation — every stage can be probed and heard.

The same link was also used to transfer data, not only audio, with both the laser
diode and the LED as emitters. No data rate was recorded for those transfers, so none
is claimed here.

## Transmitter

An audio source on its own cannot supply the current an optical emitter needs, and
connecting it directly would load the source and distort the signal. The transmitter
therefore uses a **BD139 NPN transistor** as a driver stage: the audio signal
controls the transistor, and the transistor controls the much larger current flowing
through the emitter. The emitter current is the sum of a steady operating current and
the audio-driven variation, which is exactly the amplitude modulation the link
depends on.

Why the driver stage matters:

- **Current gain:** the transistor lets a low-power audio signal set a much larger
  emitter current, which is what makes practical brightness swings possible.
- **Bias sets the operating point:** the steady current keeps the emitter in the
  region where brightness follows current, so both the loud and quiet halves of the
  waveform modulate the light instead of clipping against fully off.
- **Source protection:** the audio source drives the transistor input rather than the
  emitter load, so the source is not loaded down by the emitter current.

### Two emitters, two different beams

The build supports two emitters, which behave very differently as light sources even
though the driver and modulation are the same:

| Emitter | Rating | Behaviour in this link |
| --- | --- | --- |
| Laser diode | 5 mW | Emits a narrow, coherent beam. The light stays concentrated over distance, so the receiver picks up a strong signal when the beam is aimed at it — but aiming is critical, and a small misalignment loses the link. Recorded result: the laser link was tested up to 10 m; testing stopped there, so the maximum range was not measured. Projected (not measured): this 5 mW laser was projected to reach up to 50 m; a 30 mW laser was projected to reach 500 m. |
| LED | 3 W | Emits incoherent light that spreads out as it travels. Alignment is far more forgiving and the lit area is wide, but the intensity at the receiver falls quickly with distance because the same light is spread over a growing area. Recorded result: the LED link reached up to 5 m in the dark and 1.5–2 m in light. |

Working with both emitters makes the central trade-off of free-space optical
communication concrete: a concentrated beam is expected, in principle, to carry
intensity further but demands alignment, while a spreading source is easy to aim but
weakens with distance. For the laser, the team projected longer ranges from the
narrow, coherent beam: a 30 mW laser was projected to reach 500 m. For safety, a
5 mW laser was used instead, and that 5 mW laser was projected to reach up to 50 m.
These are projections only — testing was carried out to 10 m, so operation at 50 m
or 500 m was not measured or demonstrated. The ratings above are the component
ratings of the parts fitted; the transmitter's delivered optical output was not
measured.

> **Safety note.** A laser diode, even at a 5 mW rating, must never be viewed
> directly or aimed at eyes, and a high-power LED is bright enough to be
> uncomfortable at close range. Optical transmitters should be aimed only at the
> receiver during testing.

## Receiver

Only a fraction of the emitted light reaches the receiver, so the electrical signal
recovered from it is small. The receiver is therefore built in three steps: convert
the light back to an electrical signal, condition that signal to a usable level, and
then amplify it with enough power to move a speaker.

| Stage | Part | Role in the receiver |
| --- | --- | --- |
| Optical detection | BPW34 photodiode | A PIN photodiode whose current follows the intensity of the light falling on it. The modulated brightness reappears as a small varying signal carrying the audio waveform. |
| Conditioning | LM358 op-amp | Amplifies the small photodiode signal to a clean line level suitable for an audio amplifier input. This is where most of the voltage gain of the receiver is provided. |
| Audio output | PAM8403 amplifier | A class-D audio amplifier stage that takes the conditioned signal and drives a speaker efficiently, so the recovered audio can actually be heard. |

### Design considerations

- **Ambient light is noise:** room lighting adds its own steady and flickering light
  on top of the modulated signal, so the useful signal is only the part that varies
  with the audio. Keeping the receiver aimed at the emitter and out of direct bright
  light improves what the photodiode sees.
- **Gain has to be staged:** a photodiode signal is far too small to drive a speaker
  directly. Splitting the gain between the LM358 conditioning stage and the PAM8403
  power stage keeps each stage doing the job it suits — voltage gain first, power
  delivery second.
- **Line of sight is the medium:** the channel is the light path itself. Distance,
  angle and obstruction all act directly on the received intensity, and therefore on
  how loud and clear the recovered audio is.

## What limits a link like this

An analogue AM optical link has no error correction and no way to recover signal lost
in the channel. Intensity falls with distance — fastest for the spreading LED source
— ambient light competes with the signal, and any misalignment or obstruction weakens
or interrupts the audio. These are properties of the approach rather than faults of
this build, and they are the reason practical LiFi systems add focusing optics,
filtering and, in digital systems, encoding. None of those additions formed part of
this coursework build.

## Status and verification — stated plainly

- The project is recorded by the author as a completed last-semester coursework build
  using the parts listed in this report.
- Transferred both data and audio using both emitters — the laser diode and the LED —
  as reported by the author.
- Recorded results: LED range up to 5 m in the dark and 1.5–2 m in light; laser
  tested up to 10 m, where testing stopped, so its maximum was not measured.
- Projected range (not measured): a 30 mW laser was projected to reach 500 m. A
  5 mW laser was used instead for safety, and that 5 mW laser was projected to reach
  up to 50 m. Neither projected figure was tested; only 10 m was tested.
- No bandwidth, signal-to-noise or fidelity figures were recorded. The 5 mW and 3 W
  figures are component ratings, not measured optical output.

## Skills demonstrated

| Area | Evidence in this project |
| --- | --- |
| Analogue circuit design | BD139 transistor driver stage with a biased operating point for modulation |
| Modulation concepts | Amplitude modulation applied to an optical carrier for data transmission |
| Optoelectronics | Laser diode and LED emitters; BPW34 photodiode detection |
| Signal conditioning | LM358 operational-amplifier stage raising a small sensor signal to line level |
| Audio amplification | PAM8403 class-D amplifier driving a speaker output |
| System thinking | End-to-end link reasoning: source, driver, channel, detector, conditioning, output |

## Possible extensions

Natural next steps would be focusing optics, filtering against ambient light, and a
digitally encoded signal, where quality could be measured as error rates. These are
future directions, not features of this build.

## Repository layout

```
lifi-optical-transmission/
├── README.md
└── docs/
    └── lifi-project-report.pdf   # Full 5-page project report
```

## License

MIT — see [LICENSE](LICENSE).

## Contributors

- Vedas Rasmal — https://github.com/vedasrasmal
- Vedant Vijay Gadekar — https://github.com/vedant04-08
