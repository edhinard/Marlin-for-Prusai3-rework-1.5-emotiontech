19 avril 2020

# 1 - Firmware original

## M501

```shell
ttyACM0 20°> M501
SENDING:M501
Stored settings retrieved
Steps per unit:
M92 X80.00 Y80.00 Z1600.00 E180.00
Maximum feedrates (mm/s):
M203 X500.00 Y500.00 Z12.00 E120.00
Maximum Acceleration (mm/s2):
M201 X9000 Y9000 Z500 E10000
Acceleration: S=acceleration, T=retract acceleration
M204 S2000.00 T1500.00
Advanced variables: S=Min feedrate (mm/s), T=Min travel feedrate (mm/s), B=minimum segment time (ms), X=maximum XY jerk (mm/s),  Z=maximum Z jerk (mm/s),  E=maximum E jerk (mm/s)
M205 S0.00 T0.00 B20000 X10.00 Z0.20 E2.50
Home offset (mm):
M206 X0.00 Y0.00 Z0.00
PID settings:  
M301 P22.20 I1.08 D114.00
```

## firmware compilé

dump du contenu de la flash et de la ROM avec les commandes :

```shell
avrdude -p m2560 -c wiring -P /dev/ttyACM0 -U flash:r:flash_backup_file.raw:r
avrdude -p m2560 -c wiring -P /dev/ttyACM0 -U eeprom:r:eeprom_backup_file.raw:r

avrdude -p m2560 -c wiring -P /dev/ttyACM0 -U flash:r:flash_backup_file.hex:h
avrdude -p m2560 -c wiring -P /dev/ttyACM0 -U eeprom:r:eeprom_backup_file.hex:h
```

Le cas échéant on pourra les recharger avec les commandes (le `:r:` devient `:w:`):

```shell
avrdude -p m2560 -c wiring -P /dev/ttyACM0 -U flash:w:flash_backup_file.raw:r
avrdude -p m2560 -c wiring -P /dev/ttyACM0 -U eeprom:w:eeprom_backup_file.raw:r
```

puis pour vérifier :

```shell
avrdude -p m2560 -c wiring -P /dev/ttyACM0 -U flash:v:flash_backup_file.raw:r
avrdude -p m2560 -c wiring -P /dev/ttyACM0 -U eeprom:v:eeprom_backup_file.raw:r
```

## fichiers originaux emotion tech

depuis https://www.reprap-france.com/support vers le répertoire original

le firmware avec fichier de `Configuration.h` prêt dans `Marlin_Prusa_i3_Rework_1.5`

à noter que la compilation doit se faire avec le logiciel arduino archivé (v1.0.6)

## différences / Marlin1.0

Par comparaison avec le github https://github.com/MarlinFirmware/Marlin, le firmware archivé par emotion tech dénommé Marlin_Prusa_i3_Rework_1.5 correspond exactement à 

```
commit 4ffdbcbd4e062ca9a822d49432ce2618d0006a8a (HEAD)
Merge: 443b64886 5c8107bcd
Author: Bernhard Kubicek <kubicek@gmx.at>
Date:   Sun Nov 9 20:38:30 2014 +0100
```

avec comme seules différence significatives sont dans pins.h et Configuration.h. Les autres différences sont :

- Suppression du makefile et de marin.pde, du fichier de copyright (hum), d'un XLSX, d'un PDF, de deux sctripts Python et du répertoire d'exemples
  - Choix de la langue FR dans language.h et renommage du type fpos_t en fpos_t1 dans SdBaseFile.h|cpp

La modification de pins.h est relative au capteur inductif et de sa carte de conversion de niveau qui utilise le connecteur dédié à l'allumage de l'imprimante.

```shell
$ diff Marlin/Marlin/pins.h Marlin_Prusa_i3_Rework_1.5/Marlin/pins.h 
612c612
<     #define Z_MIN_PIN          18
---
>     #define Z_MIN_PIN          12 //-18
673c673
<   #define PS_ON_PIN          12
---
>   #define PS_ON_PIN          -1//12
2893c2893
< 
---
> 
```

Coté Configuration.h :

- débit de liaison série
- carte RAMPS 1.4
- capteurs de température
- inversion axe Y et pas Z
- dimension du plateau
- paramètres du "bed leveling"
- steps et accélération
- eeprom
- températures maxi
- LCD
- servo
- capteur filament

```shell
diff Marlin/Marlin/Configuration.h Marlin_Prusa_i3_Rework_1.5/Marlin/Configuration.h
34c34
< #define BAUDRATE 250000
---
> #define BAUDRATE 115200
85c85
< #define MOTHERBOARD 7
---
> #define MOTHERBOARD 33
145,146c145,146
< #define TEMP_SENSOR_0 -1
< #define TEMP_SENSOR_1 -1
---
> #define TEMP_SENSOR_0 1
> #define TEMP_SENSOR_1 0
148c148
< #define TEMP_SENSOR_BED 0
---
> #define TEMP_SENSOR_BED 1
358,359c358,359
< #define INVERT_Y_DIR false    // for Mendel set to true, for Orca set to false
< #define INVERT_Z_DIR true     // for Mendel set to false, for Orca set to true
---
> #define INVERT_Y_DIR true    // for Mendel set to true, for Orca set to false
> #define INVERT_Z_DIR false     // for Mendel set to false, for Orca set to true
376c376
< #define Y_MAX_POS 205
---
> #define Y_MAX_POS 190
378c378
< #define Z_MAX_POS 200
---
> #define Z_MAX_POS 190
386c386
< //#define ENABLE_AUTO_BED_LEVELING // Delete the comment to enable (remove // at the start of the line)
---
> #define ENABLE_AUTO_BED_LEVELING // Delete the comment to enable (remove // at the start of the line)
411c411
<     #define LEFT_PROBE_BED_POSITION 15
---
>     #define LEFT_PROBE_BED_POSITION 40
413,414c413,414
<     #define BACK_PROBE_BED_POSITION 180
<     #define FRONT_PROBE_BED_POSITION 20
---
>     #define BACK_PROBE_BED_POSITION 170
>     #define FRONT_PROBE_BED_POSITION 40
428c428
<       #define ABL_PROBE_PT_2_Y 20
---
>       #define ABL_PROBE_PT_2_Y 40
430c430
<       #define ABL_PROBE_PT_3_Y 20
---
>       #define ABL_PROBE_PT_3_Y 40
436,438c436,438
<   #define X_PROBE_OFFSET_FROM_EXTRUDER -25
<   #define Y_PROBE_OFFSET_FROM_EXTRUDER -29
<   #define Z_PROBE_OFFSET_FROM_EXTRUDER -12.35
---
>   #define X_PROBE_OFFSET_FROM_EXTRUDER 0
>   #define Y_PROBE_OFFSET_FROM_EXTRUDER 21
>   #define Z_PROBE_OFFSET_FROM_EXTRUDER 0 //M851 Z-"..." to change and save with M500. 
440c440
<   #define Z_RAISE_BEFORE_HOMING 4       // (in mm) Raise Z before homing (G28) for Probe Clearance.
---
>   #define Z_RAISE_BEFORE_HOMING 0       // (in mm) Raise Z before homing (G28) for Probe Clearance.
443c443
<   #define XY_TRAVEL_SPEED 8000         // X and Y axis travel speed between probes, in mm/min
---
>   #define XY_TRAVEL_SPEED 9000         // X and Y axis travel speed between probes, in mm/min
445,446c445,446
<   #define Z_RAISE_BEFORE_PROBING 15    //How much the extruder will be raised before traveling to the first probing point.
<   #define Z_RAISE_BETWEEN_PROBINGS 5  //How much the extruder will be raised when traveling from between next probing points
---
>   #define Z_RAISE_BEFORE_PROBING 1    //How much the extruder will be raised before traveling to the first probing point.
>   #define Z_RAISE_BETWEEN_PROBINGS 1  //How much the extruder will be raised when traveling from between next probing points
455c455
< //  #define PROBE_SERVO_DEACTIVATION_DELAY 300
---
>   //#define PROBE_SERVO_DEACTIVATION_DELAY 1500
470,471c470,471
<     #define Z_SAFE_HOMING_X_POINT (X_MAX_LENGTH/2)    // X point for Z homing when homing all axis (G28)
<     #define Z_SAFE_HOMING_Y_POINT (Y_MAX_LENGTH/2)    // Y point for Z homing when homing all axis (G28)
---
>     #define Z_SAFE_HOMING_X_POINT (0)    // X point for Z homing when homing all axis (G28)
>     #define Z_SAFE_HOMING_Y_POINT (21)    // Y point for Z homing when homing all axis (G28)
495,497c495,497
< #define DEFAULT_AXIS_STEPS_PER_UNIT   {78.7402,78.7402,200.0*8/3,760*1.1}  // default steps per unit for Ultimaker
< #define DEFAULT_MAX_FEEDRATE          {500, 500, 5, 25}    // (mm/sec)
< #define DEFAULT_MAX_ACCELERATION      {9000,9000,100,10000}    // X, Y, Z, E maximum start speed for accelerated moves. E default values are good for Skeinforge 40+, for older versions raise them a lot.
---
> #define DEFAULT_AXIS_STEPS_PER_UNIT   {80,80,1600,180}  // default steps per unit for Ultimaker
> #define DEFAULT_MAX_FEEDRATE          {200, 200, 5, 35}    // (mm/sec)
> #define DEFAULT_MAX_ACCELERATION      {2000,2000,100,2000}    // X, Y, Z, E maximum start speed for accelerated moves. E default values are good for Skeinforge 40+, for older versions raise them a lot.
499,500c499,500
< #define DEFAULT_ACCELERATION          3000    // X, Y, Z and E max acceleration in mm/s^2 for printing moves
< #define DEFAULT_RETRACT_ACCELERATION  3000   // X, Y, Z and E max acceleration in mm/s^2 for retracts
---
> #define DEFAULT_ACCELERATION          2000    // X, Y, Z and E max acceleration in mm/s^2 for printing moves
> #define DEFAULT_RETRACT_ACCELERATION  2000   // X, Y, Z and E max acceleration in mm/s^2 for retracts
522c522
<   #define Z_PROBE_OFFSET_RANGE_MAX -5
---
>   #define Z_PROBE_OFFSET_RANGE_MAX 0
532c532
< //#define EEPROM_SETTINGS
---
> #define EEPROM_SETTINGS
535c535
< //#define EEPROM_CHITCHAT
---
> #define EEPROM_CHITCHAT
538,539c538,539
< #define PLA_PREHEAT_HOTEND_TEMP 180
< #define PLA_PREHEAT_HPB_TEMP 70
---
> #define PLA_PREHEAT_HOTEND_TEMP 200
> #define PLA_PREHEAT_HPB_TEMP 55
543c543
< #define ABS_PREHEAT_HPB_TEMP 100
---
> #define ABS_PREHEAT_HPB_TEMP 90
565c565
< //#define REPRAP_DISCOUNT_SMART_CONTROLLER
---
> #define REPRAP_DISCOUNT_SMART_CONTROLLER
759c759
< //#define NUM_SERVOS 3 // Servo index starts with 0 for M280 command
---
> #define NUM_SERVOS 1 // Servo index starts with 0 for M280 command
766,767c766,767
< //#define SERVO_ENDSTOPS {-1, -1, 0} // Servo index for X, Y, Z. Disable with -1
< //#define SERVO_ENDSTOP_ANGLES {0,0, 0,0, 70,0} // X,Y,Z Axis Extend and Retract angles
---
> #define SERVO_ENDSTOPS {-1, -1, 0} // Servo index for X, Y, Z. Disable with -1
> #define SERVO_ENDSTOP_ANGLES {0,0, 0,0, 20,90} // X,Y,Z Axis Extend and Retract angles
780,782c780
< // Uncomment below to enable
< //#define FILAMENT_SENSOR
< 
---
> #define FILAMENT_SENSOR
803c801
< #endif //__CONFIGURATION_H
---
> #endif //__CONFIGURATION_H
```

On a donc de quoi recompiler une version plus récente du firmware Marlin !!!

# 2 - Nouveau firmware

## essais

### modification pins.h

avec la configuration suivante on est bon pour la RAZ (homing) sur les 3 axes

mais il est impossible de descendre en dessous de 0, la butée Z (endstop) bloque. Il faut donc une configuration explicitement sans butée en Z. Or ici on a modifié pins_RAMPS.h pour que la broche Zmin corresponde à celle de la sonde.

```diff
diff --git a/Marlin/Configuration.h b/Marlin/Configuration.h
index f02e69ee5..7e2c8b43b 100644
--- a/Marlin/Configuration.h
+++ b/Marlin/Configuration.h
@@ -71,7 +71,7 @@
 // @section info
 
 // Author info of this build printed to the host during boot and M115
-#define STRING_CONFIG_H_AUTHOR "(none, default config)" // Who made the changes.
+#define STRING_CONFIG_H_AUTHOR "_EdH_"
 //#define CUSTOM_VERSION_FILE Version.h // Path from the root directory (no quotes)
 
 /**
@@ -121,7 +121,7 @@
  *
  * :[2400, 9600, 19200, 38400, 57600, 115200, 250000, 500000, 1000000]
  */
-#define BAUDRATE 250000
+#define BAUDRATE 115200
 
 // Enable the Bluetooth serial interface on AT90USB devices
 //#define BLUETOOTH
@@ -132,7 +132,7 @@
 #endif
 
 // Name displayed in the LCD "Ready" message and Info menu
-//#define CUSTOM_MACHINE_NAME "3D Printer"
+#define CUSTOM_MACHINE_NAME "Prusa i3 rework 1.5 / eMotionTech"
 
 // Printer's unique ID, used by some programs to differentiate between machines.
 // Choose your own or use a service like http://www.uuidgenerator.net/version4
@@ -145,7 +145,7 @@
 #define EXTRUDERS 1
 
 // Generally expected filament diameter (1.75, 2.85, 3.0, ...). Used for Volumetric, Filament Width Sensor, etc.
-#define DEFAULT_NOMINAL_FILAMENT_DIA 3.0
+#define DEFAULT_NOMINAL_FILAMENT_DIA 1.75
 
 // For Cyclops or any "multi-extruder" that shares a single nozzle.
 //#define SINGLENOZZLE
@@ -517,7 +517,7 @@
  * heater. If your configuration is significantly different than this and you don't understand
  * the issues involved, don't use bed PID until someone else verifies that your hardware works.
  */
-//#define PIDTEMPBED
+#define PIDTEMPBED
 
 //#define BED_LIMIT_SWITCHING
 
@@ -646,13 +646,13 @@
 #endif
 
 // Mechanical endstop with COM to ground and NC to Signal uses "false" here (most common setup).
-#define X_MIN_ENDSTOP_INVERTING false // Set to true to invert the logic of the endstop.
-#define Y_MIN_ENDSTOP_INVERTING false // Set to true to invert the logic of the endstop.
-#define Z_MIN_ENDSTOP_INVERTING false // Set to true to invert the logic of the endstop.
+#define X_MIN_ENDSTOP_INVERTING true
+#define Y_MIN_ENDSTOP_INVERTING true
+#define Z_MIN_ENDSTOP_INVERTING true
 #define X_MAX_ENDSTOP_INVERTING false // Set to true to invert the logic of the endstop.
 #define Y_MAX_ENDSTOP_INVERTING false // Set to true to invert the logic of the endstop.
 #define Z_MAX_ENDSTOP_INVERTING false // Set to true to invert the logic of the endstop.
-#define Z_MIN_PROBE_ENDSTOP_INVERTING false // Set to true to invert the logic of the probe.
+#define Z_MIN_PROBE_ENDSTOP_INVERTING true
 
 /**
  * Stepper Drivers
@@ -730,14 +730,14 @@
  * Override with M92
  *                                      X, Y, Z, E0 [, E1[, E2...]]
  */
-#define DEFAULT_AXIS_STEPS_PER_UNIT   { 80, 80, 4000, 500 }
+#define DEFAULT_AXIS_STEPS_PER_UNIT   { 80, 80, 1600, 180 }
 
 /**
  * Default Max Feed Rate (mm/s)
  * Override with M203
  *                                      X, Y, Z, E0 [, E1[, E2...]]
  */
-#define DEFAULT_MAX_FEEDRATE          { 300, 300, 5, 25 }
+#define DEFAULT_MAX_FEEDRATE          { 200, 200, 5, 35 }
 
 //#define LIMITED_MAX_FR_EDITING        // Limit edit via M203 or LCD to DEFAULT_MAX_FEEDRATE * 2
 #if ENABLED(LIMITED_MAX_FR_EDITING)
@@ -750,7 +750,7 @@
  * Override with M201
  *                                      X, Y, Z, E0 [, E1[, E2...]]
  */
-#define DEFAULT_MAX_ACCELERATION      { 3000, 3000, 100, 10000 }
+#define DEFAULT_MAX_ACCELERATION      { 2000, 2000, 100, 2000 }
 
 //#define LIMITED_MAX_ACCEL_EDITING     // Limit edit via M201 or LCD to DEFAULT_MAX_ACCELERATION * 2
 #if ENABLED(LIMITED_MAX_ACCEL_EDITING)
@@ -765,9 +765,9 @@
  *   M204 R    Retract Acceleration
  *   M204 T    Travel Acceleration
  */
-#define DEFAULT_ACCELERATION          3000    // X, Y, Z and E acceleration for printing moves
-#define DEFAULT_RETRACT_ACCELERATION  3000    // E acceleration for retracts
-#define DEFAULT_TRAVEL_ACCELERATION   3000    // X, Y, Z acceleration for travel (non printing) moves
+#define DEFAULT_ACCELERATION          2000    // X, Y, Z and E acceleration for printing moves
+#define DEFAULT_RETRACT_ACCELERATION  2000    // E acceleration for retracts
+#define DEFAULT_TRAVEL_ACCELERATION   2000    // X, Y, Z acceleration for travel (non printing) moves
 
 /**
  * Default Jerk limits (mm/s)
@@ -867,7 +867,7 @@
  * A Fix-Mounted Probe either doesn't deploy or needs manual deployment.
  *   (e.g., an inductive probe or a nozzle-based probe-switch.)
  */
-//#define FIX_MOUNTED_PROBE
+#define FIX_MOUNTED_PROBE
 
 /**
  * Use the nozzle as the probe, as with a conductive
@@ -956,7 +956,7 @@
  *
  * Specify a Probe position as { X, Y, Z }
  */
-#define NOZZLE_TO_PROBE_OFFSET { 10, 10, 0 }
+#define NOZZLE_TO_PROBE_OFFSET { 0, 21, 0 }
 
 // Most probes should stay away from the edges of the bed, but
 // with NOZZLE_AS_PROBE this can be negative for a wider probing area.
@@ -1056,7 +1056,7 @@
 // @section machine
 
 // Invert the stepper direction. Change (or reverse the motor connector) if an axis goes the wrong way.
-#define INVERT_X_DIR false
+#define INVERT_X_DIR true
 #define INVERT_Y_DIR true
 #define INVERT_Z_DIR false
 
@@ -1078,7 +1078,7 @@
 
 //#define UNKNOWN_Z_NO_RAISE      // Don't raise Z (lower the bed) if Z is "unknown." For beds that fall when Z is powered off.
 
-//#define Z_HOMING_HEIGHT  4      // (mm) Minimal Z height before homing (G28) for Z clearance above the bed, clamps, ...
+#define Z_HOMING_HEIGHT  3      // (mm) Minimal Z height before homing (G28) for Z clearance above the bed, clamps, ...
                                   // Be sure to have this much clearance over your Z_MAX_POS to prevent grinding.
 
 //#define Z_AFTER_HOMING  10      // (mm) Height to move to after homing Z
@@ -1092,8 +1092,8 @@
 // @section machine
 
 // The size of the print bed
-#define X_BED_SIZE 200
-#define Y_BED_SIZE 200
+#define X_BED_SIZE 195
+#define Y_BED_SIZE 189
 
 // Travel limits (mm) after homing, corresponding to endstop positions.
 #define X_MIN_POS 0
@@ -1205,21 +1205,21 @@
 //#define AUTO_BED_LEVELING_3POINT
 //#define AUTO_BED_LEVELING_LINEAR
 //#define AUTO_BED_LEVELING_BILINEAR
-//#define AUTO_BED_LEVELING_UBL
+#define AUTO_BED_LEVELING_UBL
 //#define MESH_BED_LEVELING
 
 /**
  * Normally G28 leaves leveling disabled on completion. Enable
  * this option to have G28 restore the prior leveling state.
  */
-//#define RESTORE_LEVELING_AFTER_G28
+#define RESTORE_LEVELING_AFTER_G28
 
 /**
  * Enable detailed logging of G28, G29, M48, etc.
  * Turn on with the command 'M111 S32'.
  * NOTE: Requires a lot of PROGMEM!
  */
-//#define DEBUG_LEVELING_FEATURE
+#define DEBUG_LEVELING_FEATURE
 
 #if ANY(MESH_BED_LEVELING, AUTO_BED_LEVELING_BILINEAR, AUTO_BED_LEVELING_UBL)
   // Gradually reduce leveling correction until a set height is reached,
@@ -1236,11 +1236,11 @@
   /**
    * Enable the G26 Mesh Validation Pattern tool.
    */
-  //#define G26_MESH_VALIDATION
+  #define G26_MESH_VALIDATION
   #if ENABLED(G26_MESH_VALIDATION)
     #define MESH_TEST_NOZZLE_SIZE    0.4  // (mm) Diameter of primary nozzle.
     #define MESH_TEST_LAYER_HEIGHT   0.2  // (mm) Default layer height for the G26 Mesh Validation Tool.
-    #define MESH_TEST_HOTEND_TEMP  205    // (°C) Default nozzle temperature for the G26 Mesh Validation Tool.
+    #define MESH_TEST_HOTEND_TEMP  190    // (°C) Default nozzle temperature for the G26 Mesh Validation Tool.
     #define MESH_TEST_BED_TEMP      60    // (°C) Default bed temperature for the G26 Mesh Validation Tool.
     #define G26_XY_FEEDRATE         20    // (mm/s) Feedrate for XY Moves for the G26 Mesh Validation Tool.
     #define G26_RETRACT_MULTIPLIER   1.0  // G26 Q (retraction) used by default between mesh test elements.
@@ -1284,7 +1284,7 @@
   //#define MESH_EDIT_GFX_OVERLAY   // Display a graphics overlay while editing the mesh
 
   #define MESH_INSET 1              // Set Mesh bounds as an inset region of the bed
-  #define GRID_MAX_POINTS_X 10      // Don't use more than 15 points per axis, implementation limited.
+  #define GRID_MAX_POINTS_X 7      // Don't use more than 15 points per axis, implementation limited.
   #define GRID_MAX_POINTS_Y GRID_MAX_POINTS_X
 
   #define UBL_MESH_EDIT_MOVES_Z     // Sophisticated users prefer no movement of nozzle
@@ -1356,7 +1356,7 @@
 // - Move the Z probe (or nozzle) to a defined XY point before Z Homing when homing all axes (G28).
 // - Prevent Z homing when the Z probe is outside bed area.
 //
-//#define Z_SAFE_HOMING
+#define Z_SAFE_HOMING
 
 #if ENABLED(Z_SAFE_HOMING)
   #define Z_SAFE_HOMING_X_POINT ((X_BED_SIZE) / 2)    // X point for Z homing when homing all axes (G28).
@@ -1442,7 +1442,7 @@
  *   M501 - Read settings from EEPROM. (i.e., Throw away unsaved changes)
  *   M502 - Revert settings to "factory" defaults. (Follow with M500 to init the EEPROM.)
  */
-//#define EEPROM_SETTINGS     // Persistent storage with M500 and M501
+#define EEPROM_SETTINGS     // Persistent storage with M500 and M501
 //#define DISABLE_M503        // Saves ~2700 bytes of PROGMEM. Disable for release!
 #define EEPROM_CHITCHAT       // Give feedback on EEPROM commands. Disable to save PROGMEM.
 #define EEPROM_BOOT_SILENT    // Keep M503 quiet and only give errors during first load
diff --git a/Marlin/src/pins/ramps/pins_RAMPS.h b/Marlin/src/pins/ramps/pins_RAMPS.h
index ffe7f7bb1..e6f99f639 100644
--- a/Marlin/src/pins/ramps/pins_RAMPS.h
+++ b/Marlin/src/pins/ramps/pins_RAMPS.h
@@ -102,7 +102,7 @@
 #endif
 #ifndef Z_STOP_PIN
   #ifndef Z_MIN_PIN
-    #define Z_MIN_PIN                         18
+    #define Z_MIN_PIN                         12
   #endif
   #ifndef Z_MAX_PIN
     #define Z_MAX_PIN                         19
```





## 2.1 - Modifications

On reprend les différences sur pins.h et Configuration.h pour les appliquer sur une version récente de Marlin (2.0.5.3). On suit pour cela la [documentation](https://marlinfw.org/docs/configuration/configuration.html#configuration.h) Marlin

### Firmware Info

```C
#define STRING_CONFIG_H_AUTHOR "_EdH_"
```

### Hardware Info

#### Baud Rate


```C
#define BAUDRATE 115200
```

#### Motherboard

Valeur par défaut

```C
#define MOTHERBOARD BOARD_RAMPS_14_EFB
```

#### Machine Name

```C
#define CUSTOM_MACHINE_NAME "Prusa i3 rework 1.5 / eMotionTech"
```

### Extruder Info

#### Filament Diameter

```CQL
#define DEFAULT_NOMINAL_FILAMENT_DIA 1.75
```

### Thermal Settings

#### Temperature Sensors

Valeurs par défaut

```C
#define TEMP_SENSOR_0 1
#define TEMP_SENSOR_1 0
#define TEMP_SENSOR_2 0
#define TEMP_SENSOR_3 0
#define TEMP_SENSOR_4 0
#define TEMP_SENSOR_5 0
#define TEMP_SENSOR_6 0
#define TEMP_SENSOR_7 0
#define TEMP_SENSOR_BED 0
#define TEMP_SENSOR_PROBE 0
#define TEMP_SENSOR_CHAMBER 0
```

#### PID

##### PID Values

Valeurs par défaut

```C
#define DEFAULT_Kp 22.2
#define DEFAULT_Ki 1.08
#define DEFAULT_Kd 114
```

##### Bed PID Values

Valeurs par défaut

```C
#define DEFAULT_bedKp 10.00
#define DEFAULT_bedKi .023
#define DEFAULT_bedKd 305.4
```

### Endstops

#### Endstop Inverting

```C
#define X_MIN_ENDSTOP_INVERTING true
#define Y_MIN_ENDSTOP_INVERTING true
#define Z_MIN_ENDSTOP_INVERTING true
#define Z_MIN_PROBE_ENDSTOP_INVERTING true
```

### Movement

Données reprise du fichier Configuration.h original.

#### Default Steps per mm
```C
#define DEFAULT_AXIS_STEPS_PER_UNIT   {80,80,1600,180}
```
#### Acceleration

Les valeurs sont en contradiction avec les données stockées en eeprom :

```shell
Maximum feedrates (mm/s):
M203 X500.00 Y500.00 Z12.00 E120.00
Maximum Acceleration (mm/s2):
M201 X9000 Y9000 Z500 E10000
Acceleration: S=acceleration, T=retract acceleration
M204 S2000.00 T1500.00
```

On conserve les valeurs de Configuration.h qui ont l'air plus raisonnables.

```C
#define DEFAULT_MAX_FEEDRATE          {200, 200, 5, 35}
#define DEFAULT_MAX_ACCELERATION      {2000,2000,100,2000}
#define DEFAULT_ACCELERATION          2000
#define DEFAULT_RETRACT_ACCELERATION  2000
#define DEFAULT_TRAVEL_ACCELERATION   2000
```

### Z Probe Options
#### Probe Pins

Inutile de modifier pins.h

```C
//#define Z_MIN_PROBE_USES_Z_MIN_ENDSTOP_PIN
#define Z_MIN_PROBE_PIN 12
```
??? pas certain ???

#### Probe Type

```
#define FIX_MOUNTED_PROBE
```

#### Probe Offsets

```C
#define NOZZLE_TO_PROBE_OFFSET { 0, 21, 0 }
```

### Stepper Drivers

#### Motor Direction

Valeurs par défaut ???

```
#define INVERT_X_DIR true
#define INVERT_Y_DIR true
#define INVERT_Z_DIR false


```

### Homing and Bounds

#### Z Homing Height

```C
#define Z_HOMING_HEIGHT 4
```

*0 temporairement*

#### Homing Direction

Valeurs par défaut

```C
#define X_HOME_DIR -1
#define Y_HOME_DIR -1
#define Z_HOME_DIR -1
```

#### Movement Bounds

```C
#define X_BED_SIZE 190
#define Y_BED_SIZE 190
```

### Bed Leveling

##### Bed Leveling Style

```C
#define AUTO_BED_LEVELING_UBL
#define RESTORE_LEVELING_AFTER_G28

#define DEBUG_LEVELING_FEATURE
```

##### Unified Bed Leveling Options

```C
#define GRID_MAX_POINTS_X 7
```

### Homing Options

#### Z Safe Homing

```C
#define Z_SAFE_HOMING
```

### Additonal Features

#### EEPROM

```C
#define EEPROM_SETTINGS
```

## 2.2 - Récapitulation

```C
$ git diff | cat
diff --git a/Marlin/Configuration.h b/Marlin/Configuration.h
index f02e69ee5..b6797fd3b 100644
--- a/Marlin/Configuration.h
+++ b/Marlin/Configuration.h
@@ -71,7 +71,7 @@
 // @section info
 
 // Author info of this build printed to the host during boot and M115
-#define STRING_CONFIG_H_AUTHOR "(none, default config)" // Who made the changes.
+#define STRING_CONFIG_H_AUTHOR "_EdH_"
 //#define CUSTOM_VERSION_FILE Version.h // Path from the root directory (no quotes)
 
 /**
@@ -121,7 +121,7 @@
  *
  * :[2400, 9600, 19200, 38400, 57600, 115200, 250000, 500000, 1000000]
  */
-#define BAUDRATE 250000
+#define BAUDRATE 125000
 
 // Enable the Bluetooth serial interface on AT90USB devices
 //#define BLUETOOTH
@@ -132,7 +132,7 @@
 #endif
 
 // Name displayed in the LCD "Ready" message and Info menu
-//#define CUSTOM_MACHINE_NAME "3D Printer"
+#define CUSTOM_MACHINE_NAME "Prusa i3 rework 1.5 / eMotionTech"
 
 // Printer's unique ID, used by some programs to differentiate between machines.
 // Choose your own or use a service like http://www.uuidgenerator.net/version4
@@ -145,7 +145,7 @@
 #define EXTRUDERS 1
 
 // Generally expected filament diameter (1.75, 2.85, 3.0, ...). Used for Volumetric, Filament Width Sensor, etc.
-#define DEFAULT_NOMINAL_FILAMENT_DIA 3.0
+#define DEFAULT_NOMINAL_FILAMENT_DIA 1.75
 
 // For Cyclops or any "multi-extruder" that shares a single nozzle.
 //#define SINGLENOZZLE
@@ -730,14 +730,14 @@
  * Override with M92
  *                                      X, Y, Z, E0 [, E1[, E2...]]
  */
-#define DEFAULT_AXIS_STEPS_PER_UNIT   { 80, 80, 4000, 500 }
+#define DEFAULT_AXIS_STEPS_PER_UNIT   { 80, 80, 1600, 180 }
 
 /**
  * Default Max Feed Rate (mm/s)
  * Override with M203
  *                                      X, Y, Z, E0 [, E1[, E2...]]
  */
-#define DEFAULT_MAX_FEEDRATE          { 300, 300, 5, 25 }
+#define DEFAULT_MAX_FEEDRATE          { 200, 200, 5, 35 }
 
 //#define LIMITED_MAX_FR_EDITING        // Limit edit via M203 or LCD to DEFAULT_MAX_FEEDRATE * 2
 #if ENABLED(LIMITED_MAX_FR_EDITING)
@@ -750,7 +750,7 @@
  * Override with M201
  *                                      X, Y, Z, E0 [, E1[, E2...]]
  */
-#define DEFAULT_MAX_ACCELERATION      { 3000, 3000, 100, 10000 }
+#define DEFAULT_MAX_ACCELERATION      { 2000, 2000, 100, 2000 }
 
 //#define LIMITED_MAX_ACCEL_EDITING     // Limit edit via M201 or LCD to DEFAULT_MAX_ACCELERATION * 2
 #if ENABLED(LIMITED_MAX_ACCEL_EDITING)
@@ -765,9 +765,9 @@
  *   M204 R    Retract Acceleration
  *   M204 T    Travel Acceleration
  */
-#define DEFAULT_ACCELERATION          3000    // X, Y, Z and E acceleration for printing moves
-#define DEFAULT_RETRACT_ACCELERATION  3000    // E acceleration for retracts
-#define DEFAULT_TRAVEL_ACCELERATION   3000    // X, Y, Z acceleration for travel (non printing) moves
+#define DEFAULT_ACCELERATION          2000    // X, Y, Z and E acceleration for printing moves
+#define DEFAULT_RETRACT_ACCELERATION  2000    // E acceleration for retracts
+#define DEFAULT_TRAVEL_ACCELERATION   2000    // X, Y, Z acceleration for travel (non printing) moves
 
 /**
  * Default Jerk limits (mm/s)
@@ -828,7 +828,7 @@
  *
  * Enable this option for a probe connected to the Z Min endstop pin.
  */
-#define Z_MIN_PROBE_USES_Z_MIN_ENDSTOP_PIN
+//#define Z_MIN_PROBE_USES_Z_MIN_ENDSTOP_PIN
 
 /**
  * Z_MIN_PROBE_PIN
@@ -846,7 +846,7 @@
  *      - normally-open switches to 5V and D32.
  *
  */
-//#define Z_MIN_PROBE_PIN 32 // Pin 32 is the RAMPS default
+#define Z_MIN_PROBE_PIN 12
 
 /**
  * Probe Type
@@ -867,7 +867,7 @@
  * A Fix-Mounted Probe either doesn't deploy or needs manual deployment.
  *   (e.g., an inductive probe or a nozzle-based probe-switch.)
  */
-//#define FIX_MOUNTED_PROBE
+#define FIX_MOUNTED_PROBE
 
 /**
  * Use the nozzle as the probe, as with a conductive
@@ -956,7 +956,7 @@
  *
  * Specify a Probe position as { X, Y, Z }
  */
-#define NOZZLE_TO_PROBE_OFFSET { 10, 10, 0 }
+#define NOZZLE_TO_PROBE_OFFSET { 0, 21, 0 }
 
 // Most probes should stay away from the edges of the bed, but
 // with NOZZLE_AS_PROBE this can be negative for a wider probing area.
@@ -1078,7 +1078,7 @@
 
 //#define UNKNOWN_Z_NO_RAISE      // Don't raise Z (lower the bed) if Z is "unknown." For beds that fall when Z is powered off.
 
-//#define Z_HOMING_HEIGHT  4      // (mm) Minimal Z height before homing (G28) for Z clearance above the bed, clamps, ...
+#define Z_HOMING_HEIGHT  4      // (mm) Minimal Z height before homing (G28) for Z clearance above the bed, clamps, ...
                                   // Be sure to have this much clearance over your Z_MAX_POS to prevent grinding.
 
 //#define Z_AFTER_HOMING  10      // (mm) Height to move to after homing Z
@@ -1092,8 +1092,8 @@
 // @section machine
 
 // The size of the print bed
-#define X_BED_SIZE 200
-#define Y_BED_SIZE 200
+#define X_BED_SIZE 190
+#define Y_BED_SIZE 190
 
 // Travel limits (mm) after homing, corresponding to endstop positions.
 #define X_MIN_POS 0
@@ -1205,7 +1205,7 @@
 //#define AUTO_BED_LEVELING_3POINT
 //#define AUTO_BED_LEVELING_LINEAR
 //#define AUTO_BED_LEVELING_BILINEAR
-//#define AUTO_BED_LEVELING_UBL
+#define AUTO_BED_LEVELING_UBL
 //#define MESH_BED_LEVELING

 /**
  * Normally G28 leaves leveling disabled on completion. Enable
  * this option to have G28 restore the prior leveling state.
  */
-//#define RESTORE_LEVELING_AFTER_G28
+#define RESTORE_LEVELING_AFTER_G28

 /**
@@ -1284,7 +1284,7 @@
   //#define MESH_EDIT_GFX_OVERLAY   // Display a graphics overlay while editing the mesh
 
   #define MESH_INSET 1              // Set Mesh bounds as an inset region of the bed
-  #define GRID_MAX_POINTS_X 10      // Don't use more than 15 points per axis, implementation limited.
+  #define GRID_MAX_POINTS_X 7      // Don't use more than 15 points per axis, implementation limited.
   #define GRID_MAX_POINTS_Y GRID_MAX_POINTS_X
 
   #define UBL_MESH_EDIT_MOVES_Z     // Sophisticated users prefer no movement of nozzle
@@ -1356,7 +1356,7 @@
 // - Move the Z probe (or nozzle) to a defined XY point before Z Homing when homing all axes (G28).
 // - Prevent Z homing when the Z probe is outside bed area.
 //
-//#define Z_SAFE_HOMING
+#define Z_SAFE_HOMING
 
 #if ENABLED(Z_SAFE_HOMING)
   #define Z_SAFE_HOMING_X_POINT ((X_BED_SIZE) / 2)    // X point for Z homing when homing all axes (G28).
@@ -1442,7 +1442,7 @@
  *   M501 - Read settings from EEPROM. (i.e., Throw away unsaved changes)
  *   M502 - Revert settings to "factory" defaults. (Follow with M500 to init the EEPROM.)
  */
-//#define EEPROM_SETTINGS     // Persistent storage with M500 and M501
+#define EEPROM_SETTINGS     // Persistent storage with M500 and M501
 //#define DISABLE_M503        // Saves ~2700 bytes of PROGMEM. Disable for release!
 #define EEPROM_CHITCHAT       // Give feedback on EEPROM commands. Disable to save PROGMEM.
 #define EEPROM_BOOT_SILENT    // Keep M503 quiet and only give errors during first load
```



## xx

```gcode
M502            ; Reset settings to configuration defaults...
M500            ; ...and Save to EEPROM. Use this on a new install.
M501            ; Read back in the saved EEPROM.  
```



M104 S190



G28 XYZ

G30

G92 Z0

G91

G0 Z-0.05 x n

M114

M851 Z-x.xx

M500



G29 P1

G29 P3

```gcode
G29 T           ; View the Z compensation values.
G29 S1          ; Save UBL mesh points to EEPROM.
G29 F 10.0      ; Set Fade Height for correction at 10.0 mm.
G29 A           ; Activate the UBL System.
M500
```



# Nov 2020 - update to Marlin 2.0.7.2

## backup

```shell
$ avrdude -p m2560 -c wiring -P /dev/ttyACM0 -U eeprom:r:201121_eeprom_backup_file.raw:r
$ avrdude -p m2560 -c wiring -P /dev/ttyACM0 -U eeprom:v:201121_eeprom_backup_file.raw:r
[...]
avrdude: verifying ...
avrdude: 4096 bytes of eeprom verified

$ avrdude -p m2560 -c wiring -P /dev/ttyACM0 -U flash:r:201121_flash_backup_file.raw:r
$ avrdude -p m2560 -c wiring -P /dev/ttyACM0 -U flash:v:201121_flash_backup_file.raw:r
[...]
avrdude: verifying ...
avrdude: 261406 bytes of flash verified
```

## Marlin

```
$ git checkout tags/2.0.7.2
```

```diff
$ git diff
diff --git a/Marlin/Configuration.h b/Marlin/Configuration.h
index db266b524b..11942a2b8f 100644
--- a/Marlin/Configuration.h
+++ b/Marlin/Configuration.h
@@ -70,7 +70,7 @@
 // @section info
 
 // Author info of this build printed to the host during boot and M115
-#define STRING_CONFIG_H_AUTHOR "(none, default config)" // Who made the changes.
+#define STRING_CONFIG_H_AUTHOR "_EdH_"
 //#define CUSTOM_VERSION_FILE Version.h // Path from the root directory (no quotes)
 
 /**
@@ -120,7 +120,7 @@
  *
  * :[2400, 9600, 19200, 38400, 57600, 115200, 250000, 500000, 1000000]
  */
-#define BAUDRATE 250000
+#define BAUDRATE 115200
 
 // Enable the Bluetooth serial interface on AT90USB devices
 //#define BLUETOOTH
@@ -131,7 +131,7 @@
 #endif
 
 // Name displayed in the LCD "Ready" message and Info menu
-//#define CUSTOM_MACHINE_NAME "3D Printer"
+#define CUSTOM_MACHINE_NAME "Prusa i3 rework 1.5 / eMotionTech"
 
 // Printer's unique ID, used by some programs to differentiate between machines.
 // Choose your own or use a service like https://www.uuidgenerator.net/version4
@@ -423,7 +423,7 @@
 #define TEMP_SENSOR_5 0
 #define TEMP_SENSOR_6 0
 #define TEMP_SENSOR_7 0
-#define TEMP_SENSOR_BED 0
+#define TEMP_SENSOR_BED 1
 #define TEMP_SENSOR_PROBE 0
 #define TEMP_SENSOR_CHAMBER 0
 
@@ -499,9 +499,9 @@
     #define DEFAULT_Ki_LIST {   1.08,   1.08 }
     #define DEFAULT_Kd_LIST { 114.00, 114.00 }
   #else
-    #define DEFAULT_Kp  22.20
-    #define DEFAULT_Ki   1.08
-    #define DEFAULT_Kd 114.00
+    #define DEFAULT_Kp  27.20
+    #define DEFAULT_Ki   1.96
+    #define DEFAULT_Kd 94.15
   #endif
 #endif // PIDTEMP
 
@@ -522,7 +522,7 @@
  * heater. If your configuration is significantly different than this and you don't understand
  * the issues involved, don't use bed PID until someone else verifies that your hardware works.
  */
-//#define PIDTEMPBED
+#define PIDTEMPBED
 
 //#define BED_LIMIT_SWITCHING
 
@@ -540,9 +540,9 @@
 
   // 120V 250W silicone heater into 4mm borosilicate (MendelMax 1.5+)
   // from FOPDT model - kp=.39 Tp=405 Tdead=66, Tc set to 79.2, aggressive factor of .15 (vs .1, 1, 10)
-  #define DEFAULT_bedKp 10.00
-  #define DEFAULT_bedKi .023
-  #define DEFAULT_bedKd 305.4
+  #define DEFAULT_bedKp 78.56
+  #define DEFAULT_bedKi 10.20
+  #define DEFAULT_bedKd 403.26
 
   // FIND YOUR OWN: "M303 E-1 C8 S90" to run autotune on the bed at 90 degreesC for 8 cycles.
 #endif // PIDTEMPBED
@@ -654,13 +654,13 @@
 #endif
 
 // Mechanical endstop with COM to ground and NC to Signal uses "false" here (most common setup).
-#define X_MIN_ENDSTOP_INVERTING false // Set to true to invert the logic of the endstop.
-#define Y_MIN_ENDSTOP_INVERTING false // Set to true to invert the logic of the endstop.
-#define Z_MIN_ENDSTOP_INVERTING false // Set to true to invert the logic of the endstop.
+#define X_MIN_ENDSTOP_INVERTING true
+#define Y_MIN_ENDSTOP_INVERTING true
+#define Z_MIN_ENDSTOP_INVERTING true
 #define X_MAX_ENDSTOP_INVERTING false // Set to true to invert the logic of the endstop.
 #define Y_MAX_ENDSTOP_INVERTING false // Set to true to invert the logic of the endstop.
 #define Z_MAX_ENDSTOP_INVERTING false // Set to true to invert the logic of the endstop.
-#define Z_MIN_PROBE_ENDSTOP_INVERTING false // Set to true to invert the logic of the probe.
+#define Z_MIN_PROBE_ENDSTOP_INVERTING true
 
 /**
  * Stepper Drivers
@@ -741,14 +741,14 @@
  * Override with M92
  *                                      X, Y, Z, E0 [, E1[, E2...]]
  */
-#define DEFAULT_AXIS_STEPS_PER_UNIT   { 80, 80, 4000, 500 }
+#define DEFAULT_AXIS_STEPS_PER_UNIT   { 80, 80, 1600, 180 }
 
 /**
  * Default Max Feed Rate (mm/s)
  * Override with M203
  *                                      X, Y, Z, E0 [, E1[, E2...]]
  */
-#define DEFAULT_MAX_FEEDRATE          { 300, 300, 5, 25 }
+#define DEFAULT_MAX_FEEDRATE          { 200, 200, 5, 35 }
 
 //#define LIMITED_MAX_FR_EDITING        // Limit edit via M203 or LCD to DEFAULT_MAX_FEEDRATE * 2
 #if ENABLED(LIMITED_MAX_FR_EDITING)
@@ -761,7 +761,7 @@
  * Override with M201
  *                                      X, Y, Z, E0 [, E1[, E2...]]
  */
-#define DEFAULT_MAX_ACCELERATION      { 3000, 3000, 100, 10000 }
+#define DEFAULT_MAX_ACCELERATION      { 2000, 2000, 100, 2000 }
 
 //#define LIMITED_MAX_ACCEL_EDITING     // Limit edit via M201 or LCD to DEFAULT_MAX_ACCELERATION * 2
 #if ENABLED(LIMITED_MAX_ACCEL_EDITING)
@@ -776,9 +776,9 @@
  *   M204 R    Retract Acceleration
  *   M204 T    Travel Acceleration
  */
-#define DEFAULT_ACCELERATION          3000    // X, Y, Z and E acceleration for printing moves
-#define DEFAULT_RETRACT_ACCELERATION  3000    // E acceleration for retracts
-#define DEFAULT_TRAVEL_ACCELERATION   3000    // X, Y, Z acceleration for travel (non printing) moves
+#define DEFAULT_ACCELERATION          2000    // X, Y, Z and E acceleration for printing moves
+#define DEFAULT_RETRACT_ACCELERATION  2000    // E acceleration for retracts
+#define DEFAULT_TRAVEL_ACCELERATION   2000    // X, Y, Z acceleration for travel (non printing) moves
 
 /**
  * Default Jerk limits (mm/s)
@@ -841,10 +841,10 @@
  * The probe replaces the Z-MIN endstop and is used for Z homing.
  * (Automatically enables USE_PROBE_FOR_Z_HOMING.)
  */
-#define Z_MIN_PROBE_USES_Z_MIN_ENDSTOP_PIN
+//#define Z_MIN_PROBE_USES_Z_MIN_ENDSTOP_PIN
 
 // Force the use of the probe for Z-axis homing
-//#define USE_PROBE_FOR_Z_HOMING
+#define USE_PROBE_FOR_Z_HOMING
 
 /**
  * Z_MIN_PROBE_PIN
@@ -861,7 +861,7 @@
  *      - normally-closed switches to GND and D32.
  *      - normally-open switches to 5V and D32.
  */
-//#define Z_MIN_PROBE_PIN 32 // Pin 32 is the RAMPS default
+#define Z_MIN_PROBE_PIN 12 // Pin 32 is the RAMPS default
 
 /**
  * Probe Type
@@ -882,7 +882,7 @@
  * A Fix-Mounted Probe either doesn't deploy or needs manual deployment.
  *   (e.g., an inductive probe or a nozzle-based probe-switch.)
  */
-//#define FIX_MOUNTED_PROBE
+#define FIX_MOUNTED_PROBE
 
 /**
  * Use the nozzle as the probe, as with a conductive
@@ -986,7 +986,7 @@
  *     |    [-]    |
  *     O-- FRONT --+
  */
-#define NOZZLE_TO_PROBE_OFFSET { 10, 10, 0 }
+#define NOZZLE_TO_PROBE_OFFSET { 0, 21, 0 }
 
 // Most probes should stay away from the edges of the bed, but
 // with NOZZLE_AS_PROBE this can be negative for a wider probing area.
@@ -1086,7 +1086,7 @@
 // @section machine
 
 // Invert the stepper direction. Change (or reverse the motor connector) if an axis goes the wrong way.
-#define INVERT_X_DIR false
+#define INVERT_X_DIR true
 #define INVERT_Y_DIR true
 #define INVERT_Z_DIR false
 
@@ -1108,7 +1108,7 @@
 
 //#define UNKNOWN_Z_NO_RAISE      // Don't raise Z (lower the bed) if Z is "unknown." For beds that fall when Z is powered off.
 
-//#define Z_HOMING_HEIGHT  4      // (mm) Minimal Z height before homing (G28) for Z clearance above the bed, clamps, ...
+#define Z_HOMING_HEIGHT  3      // (mm) Minimal Z height before homing (G28) for Z clearance above the bed, clamps, ...
                                   // Be sure to have this much clearance over your Z_MAX_POS to prevent grinding.
 
 //#define Z_AFTER_HOMING  10      // (mm) Height to move to after homing Z
@@ -1122,8 +1122,8 @@
 // @section machine
 
 // The size of the print bed
-#define X_BED_SIZE 200
-#define Y_BED_SIZE 200
+#define X_BED_SIZE 195
+#define Y_BED_SIZE 189
 
 // Travel limits (mm) after homing, corresponding to endstop positions.
 #define X_MIN_POS 0
@@ -1131,7 +1131,7 @@
 #define Z_MIN_POS 0
 #define X_MAX_POS X_BED_SIZE
 #define Y_MAX_POS Y_BED_SIZE
-#define Z_MAX_POS 200
+#define Z_MAX_POS 160
 
 /**
  * Software Endstops
@@ -1147,7 +1147,7 @@
 #if ENABLED(MIN_SOFTWARE_ENDSTOPS)
   #define MIN_SOFTWARE_ENDSTOP_X
   #define MIN_SOFTWARE_ENDSTOP_Y
-  #define MIN_SOFTWARE_ENDSTOP_Z
+//  #define MIN_SOFTWARE_ENDSTOP_Z
 #endif
 
 // Max software endstops constrain movement within maximum coordinate bounds
@@ -1235,21 +1235,21 @@
 //#define AUTO_BED_LEVELING_3POINT
 //#define AUTO_BED_LEVELING_LINEAR
 //#define AUTO_BED_LEVELING_BILINEAR
-//#define AUTO_BED_LEVELING_UBL
+#define AUTO_BED_LEVELING_UBL
 //#define MESH_BED_LEVELING
 
 /**
  * Normally G28 leaves leveling disabled on completion. Enable
  * this option to have G28 restore the prior leveling state.
  */
-//#define RESTORE_LEVELING_AFTER_G28
+#define RESTORE_LEVELING_AFTER_G28
 
 /**
  * Enable detailed logging of G28, G29, M48, etc.
  * Turn on with the command 'M111 S32'.
  * NOTE: Requires a lot of PROGMEM!
  */
-//#define DEBUG_LEVELING_FEATURE
+#define DEBUG_LEVELING_FEATURE
 
 #if ANY(MESH_BED_LEVELING, AUTO_BED_LEVELING_BILINEAR, AUTO_BED_LEVELING_UBL)
   // Gradually reduce leveling correction until a set height is reached,
@@ -1266,11 +1266,11 @@
   /**
    * Enable the G26 Mesh Validation Pattern tool.
    */
-  //#define G26_MESH_VALIDATION
+  #define G26_MESH_VALIDATION
   #if ENABLED(G26_MESH_VALIDATION)
     #define MESH_TEST_NOZZLE_SIZE    0.4  // (mm) Diameter of primary nozzle.
     #define MESH_TEST_LAYER_HEIGHT   0.2  // (mm) Default layer height for the G26 Mesh Validation Tool.
-    #define MESH_TEST_HOTEND_TEMP  205    // (°C) Default nozzle temperature for the G26 Mesh Validation Tool.
+    #define MESH_TEST_HOTEND_TEMP  190    // (°C) Default nozzle temperature for the G26 Mesh Validation Tool.
     #define MESH_TEST_BED_TEMP      60    // (°C) Default bed temperature for the G26 Mesh Validation Tool.
     #define G26_XY_FEEDRATE         20    // (mm/s) Feedrate for XY Moves for the G26 Mesh Validation Tool.
     #define G26_RETRACT_MULTIPLIER   1.0  // G26 Q (retraction) used by default between mesh test elements.
@@ -1314,7 +1314,7 @@
   //#define MESH_EDIT_GFX_OVERLAY   // Display a graphics overlay while editing the mesh
 
   #define MESH_INSET 1              // Set Mesh bounds as an inset region of the bed
-  #define GRID_MAX_POINTS_X 10      // Don't use more than 15 points per axis, implementation limited.
+  #define GRID_MAX_POINTS_X 7      // Don't use more than 15 points per axis, implementation limited.
   #define GRID_MAX_POINTS_Y GRID_MAX_POINTS_X
 
   #define UBL_MESH_EDIT_MOVES_Z     // Sophisticated users prefer no movement of nozzle
@@ -1385,7 +1385,7 @@
 // - Move the Z probe (or nozzle) to a defined XY point before Z Homing.
 // - Prevent Z homing when the Z probe is outside bed area.
 //
-//#define Z_SAFE_HOMING
+#define Z_SAFE_HOMING
 
 #if ENABLED(Z_SAFE_HOMING)
   #define Z_SAFE_HOMING_X_POINT X_CENTER  // X point for Z homing
@@ -1471,7 +1471,7 @@
  *   M501 - Read settings from EEPROM. (i.e., Throw away unsaved changes)
  *   M502 - Revert settings to "factory" defaults. (Follow with M500 to init the EEPROM.)
  */
-//#define EEPROM_SETTINGS     // Persistent storage with M500 and M501
+#define EEPROM_SETTINGS     // Persistent storage with M500 and M501
 //#define DISABLE_M503        // Saves ~2700 bytes of PROGMEM. Disable for release!
 #define EEPROM_CHITCHAT       // Give feedback on EEPROM commands. Disable to save PROGMEM.
 #define EEPROM_BOOT_SILENT    // Keep M503 quiet and only give errors during first load
```

## flash

position du chariot centré X,Y et Z ~6cm avec octoprint. arrêt octoprint

compilation et chargement avec IDE Arduino 