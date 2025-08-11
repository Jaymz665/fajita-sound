Pipewire is required, so install `postmarketos-base-ui-audio-pipewire`


Edit file `/usr/share/alsa/ucm2/OnePlus/fajita/HiFi.conf`

```
# Modified HiFi use case configuration for OnePlus 6T (fajita) with call support
SectionVerb {
    EnableSequence [
        # Playback paths
        cset "name='SLIMBUS_0_RX Audio Mixer MultiMedia1' 1"
        cset "name='QUAT_MI2S_RX Audio Mixer MultiMedia3' 1"
        
        # Capture paths
        cset "name='MultiMedia2 Mixer SLIMBUS_0_TX' 1"
        cset "name='MultiMedia4 Mixer SLIMBUS_1_TX' 1"

        # Voice call support (added)
        cset "name='SLIMBUS_0_RX Voice Mixer VoiceMMode1' 1"
        cset "name='VoiceMMode1 Capture Mixer SLIMBUS_0_TX' 1"
        cset "name='VoiceMMode1 Capture Mixer SLIMBUS_1_TX' 1"

        # Bottom Mic
        cset "name='AIF1_CAP Mixer SLIM TX7' 1"
        cset "name='CDC_IF TX7 MUX' DEC7"
        cset "name='AMIC4_5 SEL' AMIC4"

        # Top Mic
        cset "name='AIF2_CAP Mixer SLIM TX6' 1"
        cset "name='CDC_IF TX6 MUX' DEC6"
        
        # Earpiece
        cset "name='SLIM RX0 MUX' AIF1_PB"
        cset "name='RX INT0 DEM MUX' CLSH_DSM_OUT"
        cset "name='RX INT0_1 MIX1 INP0' RX0"
    ]

    DisableSequence [
        cset "name='SLIMBUS_0_RX Audio Mixer MultiMedia1' 0"
        cset "name='QUAT_MI2S_RX Audio Mixer MultiMedia3' 0"
        cset "name='MultiMedia2 Mixer SLIMBUS_0_TX' 0"
        cset "name='MultiMedia4 Mixer SLIMBUS_1_TX' 0"
        
        # Voice call cleanup (added)
        cset "name='SLIMBUS_0_RX Voice Mixer VoiceMMode1' 0"
        cset "name='VoiceMMode1 Capture Mixer SLIMBUS_0_TX' 0"
        cset "name='VoiceMMode1 Capture Mixer SLIMBUS_1_TX' 0"
        
        cset "name='CDC_IF TX7 MUX' ZERO"
        cset "name='CDC_IF TX6 MUX' ZERO"
        cset "name='RX INT0_1 MIX1 INP0' ZERO"
    ]

    Value {
        TQ "HiFi"
    }
}

SectionDevice."Speaker" {
    Comment "Speaker"
    EnableSequence [
        cset "name='QUAT_MI2S_RX Audio Mixer MultiMedia3' 1"
    ]
    DisableSequence [
        cset "name='QUAT_MI2S_RX Audio Mixer MultiMedia3' 0"
    ]
    Value {
        PlaybackPriority 200
        PlaybackPCM "hw:${CardId},2"
    }
}

SectionDevice."Earpiece" {
    Comment "Earpiece"
    EnableSequence [
        cset "name='SLIMBUS_0_RX Audio Mixer MultiMedia1' 1"
        cset "name='SLIM RX0 MUX' AIF1_PB"
        cset "name='RX INT0 DEM MUX' CLSH_DSM_OUT"
        cset "name='RX INT0_1 MIX1 INP0' RX0"
    ]
    DisableSequence [
        cset "name='SLIMBUS_0_RX Audio Mixer MultiMedia1' 0"
        cset "name='RX INT0_1 MIX1 INP0' ZERO"
    ]
    Value {
        PlaybackPriority 100
        PlaybackPCM "hw:${CardId},0"
        PlaybackVolume "RX0 Digital Volume"
    }
}

SectionDevice."Mic1" {
    Comment "Bottom Microphone"
    EnableSequence [
        cset "name='ADC MUX7' AMIC"
        cset "name='AMIC MUX7' ADC4"
    ]
    DisableSequence [
        cset "name='ADC MUX7' ZERO"
        cset "name='AMIC MUX7' ZERO"
    ]
    Value {
        CapturePriority 200
        CapturePCM "hw:${CardId},1"
        CaptureMixerElem "ADC4"
        CaptureVolume "ADC4 Volume"
    }
}

SectionDevice."Mic2" {
    Comment "Top Microphone"
    EnableSequence [
        cset "name='ADC MUX6' AMIC"
        cset "name='AMIC MUX6' ADC3"
    ]
    DisableSequence [
        cset "name='ADC MUX6' ZERO"
        cset "name='AMIC MUX6' ZERO"
    ]
    Value {
        CapturePriority 100
        CapturePCM "hw:${CardId},3"
        CaptureMixerElem "ADC3"
        CaptureVolume "ADC3 Volume"
    }
}
```

Edit file `/usr/share/alsa/ucm2/OnePlus/fajita/fajita.conf`

```
Syntax 4

SectionUseCase."HiFi" {
    File "/OnePlus/fajita/HiFi.conf"
    Comment "HiFi quality Music."
}

SectionUseCase."Voice Call" {
    File "/OnePlus/fajita/HiFi.conf"  
    Comment "Make a phone call using HiFi profile"
}

Include.card-init.File "/lib/card-init.conf"
Include.ctl-remap.File "/lib/ctl-remap.conf"

BootSequence [
    cset "name='RX0 Digital Volume' 75"
    cset "name='ADC4 Volume' 16"
    cset "name='DEC7 Volume' 75"
    cset "name='ADC3 Volume' 16"
    cset "name='DEC6 Volume' 75"
]
```

And remove `/usr/share/alsa/ucm2/OnePlus/fajita/VoiceCall.conf`
