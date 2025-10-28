# Renode setup
The Raspberry Pico needs configuration files for Renode to work properly.

* On MacOS, the installation location is `/Applications/Renode.app/Contents/MacOs`
* On Linux, the location for Debian, Fedora, and Arch is `/opt/renode`
* On Windows, the location is `C://Program Files/Renode`

To add the Pico configuration files:
1. Copy `rp2040_spinlock.py` and `rp2040_divider.py` to the `scripts/pydev` directory of your Renode installation.
1. Copy `rpi_pico_rp2040_w.repl` to the `platforms/cpus` directory.


## Results from different time keeping methods:
# 1. sleep.c: sleep in main method loop
- With no delay/busy loop in the code:
T = 200.0016 ms ... StDev (Jitter) = 18 ns (very small)
f = 4.999 Hz ... StDev (Jitter) = 460 nHz (very small)
Duty Cycle = 50%

- With delay/busy loop
(for i = 0 ; i < 5,000,000)
T = 200.0016 ms ... StDev (Jitter) = 18 ns
f = 4.9999 Hz ... StDev (Jitter) = 460 nHz
Duty Cycle = 50%

# 2. task_delay.c: separate FreeRTOS task with delay inside it 
- With no delay/busy loop in the code:
T = 199.9988 m ... StDev (Jitter) = 552 ns (very small)
f = 4.999 Hz ... StDev (Jitter) = 6.84 uHz (very small)
Duty Cycle = 50%

- With delay/busy loop
(for i = 0 ; i < 500,000)
T = 199.9988 ms... StDev (Jitter) = 918 ns (still small)
f = 4.999 Hz ... StDev (Jitter) = 23.45 uHz (still small)
Duty Cycle = 50%

(for i = 0 ; i < 5,000,000)
T = 199.9988 ms... StDev (Jitter) = 736 ns (still small)
f = 5.0 Hz ... StDev (Jitter) = 7.41 uHz (still small)
Duty Cycle = 50%

# 3. timer.c: timer that repeats a callback with a set delay between callbacks
- With no delay/busy loop in the code:
T = 200.0016 ms ... StDev (Jitter) = 18 ns 
f = 4.999 Hz ... StDev (Jitter) = 460 nHz
Duty Cycle = 50%

- With delay/busy loop
(for i = 0 ; i < 5,000,000)
T =  200.0016 ms ... StDev (Jitter) = 18 ns
f = 4.9999 Hz ... StDev (Jitter) = 460 nHz
Duty Cycle = 50%

The scope was giving us the exact same numbers for the measurements and standard deviations even with the delay loop...which is a little suspicious. But after a lot of troubleshooting we weren't really sure if we should have expected a longer period or a higher standard deviation with the empty loop. That was our expected result, as we kept increasing the loop length, but it still kept giving us the same statistics.

## Computing delay:
# 1. sleep.c: 
periods per hour: 3600s/hr * 1period/0.2s = 18000 periods/hour
Drift: (mean - actual)*periods/hour = (200.0016-200)*18000 = 28.8s after an hour

# 2. task_delay.c:
periods per hour: 3600s/hr * 1period/0.2s = 18000 periods/hour
(199.9988-200)*18000 = 28.8s after an hour

# 3. timer.c:
periods per hour: 3600s/hr * 1period/0.2s = 18000 periods/hour
(200.0016-200)*18000 = -21.6s after an hour

# Measuring the delay with waveform generator
gpio_interrupt.c
without delay
![withoutDelay](https://github.com/user-attachments/assets/6befeb0b-3c78-4f1d-9976-47f6371b6f9f)
delta_t = 1.6 us

with delay
![withDelay](https://github.com/user-attachments/assets/b8ad8aa2-8ed2-44bc-9e1b-9fccebf6341f)
delta_t = 2.0 us

We added a 0.5 ms busy_wait...yet it did not add 500 us. We did this on multiple scopes, too. Also, the particular scope we were using was showing us that the pico was not keeping the voltage high the same duration as the waveform generator. The duty cycle was like 0.26%, instead of 50%...
A lot of things seemed to not be working very consistently for this lab...

