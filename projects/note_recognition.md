# Real-Time Piano Note Recognition

Nir Cohen | Electrical Engineering, Tel Aviv University

---

## Overview

A real-time note recognition system running on an Arduino. A microphone captures audio, the MCU computes an FFT, and a harmonic matching algorithm identifies piano notes (C4--B4). Recognized sequences are transmitted over I2C to a second Arduino for playback.

The system uses no Arduino timing abstractions -- ADC sampling is interrupt-driven via direct register configuration (ADMUX, ADCSRA, TCCR1A/B), giving deterministic sampling at 7.2 kHz.

---

## Architecture

```mermaid
flowchart LR
    mic[Microphone] --> adc[ADC + Timer ISR] --> fft[FFT + Harmonic Analysis] --> sm[State Machine] --> i2c[I2C] --> playback[Playback Arduino]
```

Audio is sampled at a fixed rate via Timer1 interrupts. A 128-point FFT (Hamming window, DC removed) produces the magnitude spectrum. Peaks are matched against known note frequencies using squared-error minimization across harmonics. A state machine handles noise rejection, timeouts, and sequence validation before transmitting the result over I2C.

---

## Technical Details

| | |
|---|---|
| **Platform** | Arduino (ATmega), direct register access |
| **Sampling** | 7.272 kHz, interrupt-driven ADC |
| **FFT** | 128 samples, Hamming window, arduinoFFT library |
| **Recognition** | Harmonic peak matching, squared-error minimization |
| **Notes** | C4, D4, E4, F4, G4, A4, B4 |
| **Communication** | I2C to secondary Arduino |

---

## Code

[View source](../piano-recognition.ino)

| Function | Role |
|---|---|
| `ISR(ADC_vect)` | Real-time ADC sampling |
| `computeFFT()` | FFT and magnitude computation |
| `decideFrequency()` | Harmonic matching algorithm |
| `allNotesPlayed()` | Sequence validation and I2C transmission |

---

Grade: 100 in Hardware Lab course.

---
