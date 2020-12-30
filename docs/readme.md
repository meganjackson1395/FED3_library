## [FED3 library](https://github.com/KravitzLabDevices/FED3_library) documentation

### Variables.  These can be set or recalled with 'fed3.Variable'
- **LeftCount**: Int, total number of pokes on the left nosepoke, initializes to 0 
- **leftInterval**: Int, duration of the last left nosepoke in ms
- **RightCount**: Int, number of pokes on the right nosepoke, initializes to 0 
- **rightInterval**: Int, duration of the last riht nosepoke in ms
- **PelletCount**: Int, total number of pellets dispensed, initializes to 0
- **retInterval**: Int, how long the pellet remained in the well before it was taken in ms. Maximum value is 60000.
- **Event**: String, variable containing which type of event triggered the datalogging, options are Left, Right, or Pellet

---
  
### Functions. These can be called with 'fed3.Function()'
> Poke functions.  
- **logLeftPoke()**: Causes FED3 to increment **LeftCount** and log the left poke and duration to the SD card
- **logRightPoke()**: Causes FED3 to increment **RightCount** and log the right poke and duration to the SD card

> Feeding functions
- **Feed()**: Causes FED3 to drop a pellet. FED3 will continue attempting to drop pellet until it is detected in the pellet well, and will initiate jam clearing operations automatically if it fails to dispense on the following schedule: Every 5th attempt (**MinorJam**); Every 10th attempt (**VibrateJam**); Every 10th attempt (**ClearJam**). Once it detects a pellet it increments **PelletCount** and waits for up to 60 seconds for the pellet to be removed so it can calculate **retInterval**.  
- **MinorJam()**: Causes FED3 pellet disk to make a small backwards movement 
- **VibrateJam()**: Causes FED3 pellet disk to make a vibrating movement for ~10 seconds, stopping this movement if a pellet is detected
- **ClearJam()**: Causes FED3 pellet disk to make a full rotation backwards and forwards, stopping this movement if a pellet is detected
- **Timeout(seconds)**: Starts a timeout period, length controlled by **seconds**.  Duration of timeout counts down on FED3 screen. Example: Timeout(10) will make FED unresponsive for 10 seconds when called.

> Audio and Neopixel functions
- **pixelsOff()**: Turn all Neopixels off
- **ConditionedStimulus()**: Turn all pixels on a green/blue color and a 4000Hz tone for 200ms
- **Click()**: Short "click" (800Hz tone for 8ms)
- **Noise()**: ~500ms random noise stimulus

> FED3 output port
- **BNC(delay, number)**: Send **number** of pulses of length **delay** from the BNC output port. Example: BNC(20, 5) will send five 20ms pulses from the port. 

> Display functions
- Under construction

> SDcard logging functions
- Under construction

---

### Examples
> Fixed-Ratio 1: The left poke causes conditioned stimulus and a pellet, and right poke does nothing 

```
#include <FED3.h>                                       //Include the FED3 library 
String sketch = "FR1";                                  //Unique identifier text for each sketch
FED3 fed3 (sketch);                                     //Start the FED3 object

void setup() {
  fed3.begin();                                         //FED3 hardware setup commands
  fed3.FEDmode = 1;                                     //Set display to FEDmode == 1 for an operant session
  fed3.EnableSleep = true;                              //Enable sleep functionality to save battery
}

void loop() {
  fed3.run();                                           //Call fed.run at least once per loop

  if (fed3.Left) {                                      //If left poke is triggered
    fed3.logLeftPoke();                                 //Log left poke
    fed3.ConditionedStimulus();                         //Deliver conditioned stimulus (tone and lights)
    fed3.Feed();                                        //Deliver pellet
  }
  
  if (fed3.Right) {                                     //If right poke is triggered
    fed3.logRightPoke();                                //Log right poke
  }
}
```