/*
 * SimpleIRTest.cpp
 *
 * This is the simplest possible IR test program.
 * Is accesses the blink hardware directly with no interrupts and requires no library code (runs bare metal).
 *
 * It has three modes: TX, RX, and sleep
 *
 * Assumes factory default fuses (runs at 1Mhz).
 *
 * TX mode
 * =======
 * Blink sends an IR pulse every 750us on the active face
 *
 * The 'A' pin on the service port connector goes high while the IR LED is on.
 *
 * The RGB LED on the active face shows green to indicate TX mode
 *
 * Long button press (>0.5s) cycles though pulse transmitted pulse width
 * The pulse width is indicated by the number of BLUE LEDS lit:
 *
 *    1 blue = 5us
 *    2 blue = 7us
 *    3 blue = 10us
 *    4 blue = 12us
 *    5 blue = 15us
 *
 * Short button press (<0.5s) the button advances to the next face
 * Short button press after the last face switches to RX mode
 *
 * RX mode
 * =======
 * Blink listens for IR triggers on the active face
 *
 * The 'A' pin on the service port connector pulses high when the active IR LED is triggered. This
 * pulse width is fixed and only indicates that a trigger happened.
 *
 * The RGB LED on the active face shows RED to indicate RX
 * The time since the last trigger is indicated by which BLUE LEDS lit:
 *
 *    P2  Cyan    = <650us              (Too short)
 *    P3  Blue    = >650us and <950us   (Just right)
 *    P4  Magenta = >950us              (Too long)
 *
 * The two error cases are latching, so if even a single error is detected then the pixel will stay on.
 *
 * Short button press clears any errors.
 * Long button press advances to the next face
 * Long button press after the last face goes to sleep mode
 *
 * SLEEP MODE
 * ==========
 *
 * Blink goes into low power sleep.
 * Pressing the button in sleep mode wakes the blink and goes to TX mode
 *
 * Usage
 * =====
 * 1. Put one blink in TX mode (one face green) and select a medium pulse width (3 blue LEDs).
 * 2. Put another blink in RX mode (one face red).
 * 3. Aim the green face from the TX mode blink and the red face from the RX mode blink at each other.
 *
 * For the blink in RTX mode...
 * Cyan indicates that the IR LED is triggering by ambient light before the pulse arrives, or something is touching the LED
 * Blue indicates that pulses with expected timing are being received
 * Magenta in Rx mode indicates that some TX pulses are being missed
 * Each of these indications will stay lit for about 0.1 second after the event so you can see it.
 *
 * If only blue on RX lights then this is a good sign that good communication is possible.
 *
 * Try different TX pulse widths by long pressing the button. Longer widths (more blue LEDs) have more energy so
 * are better able to trigger though poor communications paths, but take longer and add noise.
 *
 * Connect an oscilloscope to the A pin on both blinks. Trigger on the A pin from the TX blink and watch to
 * see how long it takes for the RX to trigger after the TX pulse is sent.
 * Watch for the min and max of the timing between the RX and TX going high on pin A of the two blinks
 * Short and consistent times are good.
 *
 * Note that service port pin A is connect to ATMEGA pin 19 (PE2).
 *
 */
