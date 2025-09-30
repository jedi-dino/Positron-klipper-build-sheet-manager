# Positron Klipper Build Sheet Manager

**Positron Klipper Build Sheet Manager** is a custom tool to help manage build sheets for the Positron 3D printer.

This project was inspired by [this Klipper Discourse post](https://klipper.discourse.group/t/build-sheet-manager-adjust-live-z/4013/7). While their solution didn’t work perfectly for me, some of their commands and ideas are used here—credit to the original authors!

---

## Features

* Easy build sheet management from your printer's screen
* Supports multiple build sheets (e.g., Smooth PEI, Textured PEI)
* Integrated into your existing Positron macros

---

## Installation

1. **Upload the configuration:**
   Copy `build_sheets.cfg` to your main Klipper directory.

2. **Replace KlipperScreen config:**
   Replace your `klipperscreen.cfg` with the one provided in this repo.

3. **Update `PRINT_START` macro:**
   Replace your existing `PRINT_START` macro in `positronmacros.cfg` with the following:

   ```gcode
   [gcode_macro PRINT_START]
   description: Print startup sequence
   gcode:
       {% set BED_TEMP = params.BED|default(65)|float %}
       {% set READY_TEMP = BED_TEMP * 0.9 %}
       {% set EXTRUDER_TEMP = params.EXTRUDER|default(190)|float %}

       SAVE_GCODE_STATE NAME=STATE_PRINT_START
       LED_PENDING
       ;PCF_CHECK                          
       M107                                ; turn off part fan
       M140 S{BED_TEMP}                    ; set bed temperature
       G28                               
       NOZZLE_DOCK                       
       TEMPERATURE_WAIT SENSOR="heater_bed" MINIMUM={READY_TEMP}   
       M109 S{EXTRUDER_TEMP}              
       M190 S{BED_TEMP}                    
       NOZZLE_UNDOCK                       
       SET_GCODE_OFFSET Z=0.0  
       APPLY_BUILD_SHEET_ADJUSTMENT
       G90          
       RESTORE_GCODE_STATE NAME=STATE_PRINT_START
   ```

4. **Include build sheets config:**
   Add this line at the start of `positronmacro.cfg`:

   ```gcode
   [include build-sheets.cfg]
   ```

5. **Add build sheet macros:**
   Add the following after including build sheets:

   ```gcode
   [gcode_macro INSTALL_TEXTURED_SHEET]
   gcode:
       INSTALL_BUILD_SHEET NAME="Textured PEI"

   [gcode_macro INSTALL_SMOOTH_SHEET]
   gcode:
       INSTALL_BUILD_SHEET NAME="Smooth PEI"
   ```

6. **Restart your printer.**

---

## Usage

1. On the screen, select **Build Sheets**, then choose **Glass**.
2. In the terminal, run:

   ```gcode
   SET_BUILD_SHEET_OFFSET OFFSET=<your Z offset>
   ```
3. Repeat the above two steps for **Textured PEI**.
4. Start your print and enjoy!

   

---

## Contact

* Join the Positron Discord and contact me: `hello:)`
* Or make a pull request here on GitHub
