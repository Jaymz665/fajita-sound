Pulseaudio is required, so install `postmarketos-base-ui-audio-pulseaudio`


Next, you need to find and comment the line in `/etc/pulse/default.pa`
like this `#load-module module-suspend-on-idle`


Because the instruction is "universal", we edit the file to simplify it
`/usr/sbin/call_audio_idle_suspend_workaround`

```
#!/bin/sh

# dbus-monitor is run as a child process to this script. Kill child process too when the script terminates.
trap 'pkill -9 -P $$ && exit 0' INT TERM

interface=org.freedesktop.ModemManager1.Call
member=StateChanged

dbus-monitor --system "type='signal',interface='$interface',member='$member'" |
        while read -r line; do
                state=$(echo "$line" | awk '/\<int32\>/ {print $2}')
                if [ -n "$state" ]; then
                        # Call State is based on https://www.freedesktop.org/software/ModemManager/doc/latest/ModemManager/ModemManager-Flags-and-Enumerations.html#MMCallState
                        if [ "$state" -eq '0' ] || [ "$state" -eq '3' ]; then
                                echo "Call Started"

                                pactl set-default-sink alsa_output.platform-sound.HiFi__Earpiece__sink
                                pactl set-sink-volume @DEFAULT_SINK@ 15%

                        fi

                        if [ "$state" -eq '7' ]; then
                                echo "Call Ended"

                                sleep 1
                                pactl set-default-sink alsa_output.platform-sound.HiFi__Speaker__sink
                        fi
                fi
        done &

wait
```
