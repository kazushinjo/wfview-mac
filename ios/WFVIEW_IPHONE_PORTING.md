# wfview-iphone porting notes

Apply and verify the following iOS-specific fixes when the `wfview-iphone`
project is updated from this repository.

## Transmit audio and IC-7300

- Keep `NSMicrophoneUsageDescription` in `Info.plist`.
- Before constructing `wfmain`, check `QMicrophonePermission`. If its state is
  `Undetermined`, wait for the permission request to complete before creating
  the LAN audio input path.
- Configure the iOS `AVAudioSession` for `PlayAndRecord` before starting Qt
  audio input.
- For an IC-7300 connected through wfserver, select the radio's pure `USB`
  modulation input for both DATA OFF and DATA1. Do not leave it at `M/A`.
- The initial commands queued from `receiveRigCaps()` can be removed later by
  `initPeriodicCommands()`. Re-enforce the USB selection from
  `receiveModInput()` when the radio reports `M/A` or another non-USB source.
- Set a usable USB modulation level. The iPad implementation starts at
  204/255 (80%); adjust downward if ALC is excessive.
- On wfserver, `TX Audio Output` must be the IC-7300 `USB Audio CODEC` device.

Expected main-screen indication after connection:

`RF  AF  SQ  USB  TX  Mon  WF`

During diagnosis, verify the stages separately: iOS raw microphone samples,
converted TX level, outgoing audio packets, wfserver output device, and the
radio's USB modulation source.

## ATU and SWR

- IC-7300 tuner value `2` starts tuning; final status is `0` (off/failure) or
  `1` (enabled/success). Poll every five seconds for up to twenty seconds.
- Do not leave an ATU tuning/status-check message visible after returning to
  receive.
- Collect SWR samples during transmission and display only the final sample
  after returning to receive. Keep it visible until the next measurement.
- Display the final numeric value in the SWR meter. Values below 3 are blue;
  the portion at and above 3 is red.
- In the IC-7300 and IC-7300MK2 rig definitions, the final SWR calibration
  point is `RigVal=241`, not `2410`.

