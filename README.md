# <b> Sun-Tracking Solor Panel</b>

####  This is a project to demonstrate the ease of making an extra-efficient solar panel, a device that can help us maintain the health of our planet. 

### Purpose:
My purpose for this project was to demonstrate the simplicity of certain designs and how they can improve the world around us. I hope my project will help others to have a more open mind toward helping make advances in our society.

### Code: (also in attached file)

```python

import machine
import utime

# Setup PWM on GPIO 16 (Physical Pin 21)
servo = machine.PWM(machine.Pin(16))
servo.freq(50)

def set_angle(angle):
    # Map 0-180 degrees to 1638-8192 duty cycle
    duty = int((angle / 180) * (8192 - 1638) + 1638)
    servo.duty_u16(duty)

# --- REAL-WORLD TIMING CONFIGURATION ---
START_ANGLE = 50
END_ANGLE = 120
TOTAL_STEPS = END_ANGLE - START_ANGLE # 70 degrees of total travel

DAYLIGHT_HOURS = 12
NIGHT_HOURS = 12

# Calculate seconds to wait between each 1-degree move
# (12 hours * 60 mins * 60 secs) / 70 steps = ~617.14 seconds
TRACKING_DELAY = (DAYLIGHT_HOURS * 60 * 60) / TOTAL_STEPS

# Smooth reset speed (0.2 seconds per degree)
RESET_DELAY = 0.2  

# Calculate how long to sleep at night (in seconds)
NIGHT_DELAY = NIGHT_HOURS * 60 * 60

print("Starting Full-Day Solar Tracker...")
print(f"Tracking delay: {TRACKING_DELAY} seconds per degree.")

try:
    # Initialize at the dawn position immediately
    set_angle(START_ANGLE)
    utime.sleep(2)

    while True:
        # 1. DAYTIME: Track the sun (12 hours)
        print("Good morning! Tracking the sun...")
        for angle in range(START_ANGLE, END_ANGLE + 1, 1):
            set_angle(angle)
            utime.sleep(TRACKING_DELAY)
            
        print("Sunset reached. Preparing to reset...")
        utime.sleep(5) 
            
        # 2. DUSK: Slow reset to dawn position (~14 seconds)
        print("Resetting to dawn position...")
        for angle in range(END_ANGLE - 1, START_ANGLE - 1, -1):
            set_angle(angle)
            utime.sleep(RESET_DELAY)
            
        # 3. NIGHTTIME: Wait for the next sunrise (12 hours)
        print("Reset complete. Sleeping until dawn...")
        utime.sleep(NIGHT_DELAY)

except KeyboardInterrupt:
    servo.deinit()
    print("\nTracking stopped by user.")
```
