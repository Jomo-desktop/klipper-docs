# mmu.cfg

```CFG
###########################################################################
# Happy Hare MMU hardware pin config
#
# EDIT THIS FILE BASED ON YOUR SETUP
#
# This contains aliases for pins for MCU type unknown
#
[mcu mmu]
serial: /dev/ttyACM1 # Config guess. Run ls -l /dev/serial/by-id and set manually	# Change to `canbus_uuid: 1234567890` for CANbus setups

# Section to create alias for pins used by MMU for easier integration into Klippain and RatOS.
# The names match those referenced in the mmu_hardware.cfg file. If you get into difficulty
# you can also comment out this aliases definition and configure the pin names directly
# into mmu_hardware.cfg.
#
[board_pins mmu]
mcu: mmu # Assuming using an external / extra mcu dedicated to MMU
aliases:
    MMU_GEAR_UART=PE7,
    MMU_GEAR_STEP=PC5,
    MMU_GEAR_DIR=PB1,
    MMU_GEAR_ENABLE=PB0,
    MMU_GEAR_DIAG=PE8,
    MMU_SEL_UART=PE11,
    MMU_SEL_STEP=PE14,
    MMU_SEL_DIR=PE13,
    MMU_SEL_ENABLE=PE12,
    MMU_SEL_DIAG=PE9,
    MMU_SEL_ENDSTOP=PE2,
    MMU_SERVO=PA3,
    MMU_CUT_SERVO=PA2,
    MMU_ENCODER=PE3,
    MMU_GATE_SENSOR=PC3,
    MMU_NEOPIXEL=PE10,
    MMU_PRE_GATE_0=PC6,
    MMU_PRE_GATE_1=PD15,
    MMU_PRE_GATE_2=PD14,
    MMU_PRE_GATE_3=PD13,
    MMU_PRE_GATE_4=PD12,
    MMU_PRE_GATE_5=PD11,
    MMU_PRE_GATE_6=PD10,
    MMU_PRE_GATE_7=PD9,
```

# mmu_hardware.cfg

```cfg
########################################################################################################################
# Happy Hare MMU hardware config file with config for unknown MCU board
#
# EDIT THIS FILE BASED ON YOUR SETUP
#
# Notes about setup of common external mcus can be found here:
#  https://github.com/moggieuk/Happy-Hare/blob/main/doc/mcu_notes.md
# 
# Note about "touch" endstops: Happy Hare provides extremely flexible homing options using both single steppers or
# synced steppers. The "touch" option leverages stallguard and thus requires the appropriate 'diag_pin' and stallguard
# parameters set on the TMC driver section. If you have the diag_pin exposed, it is harmless to define this because
# they will only be used when explicitly needed and configured.
#
# Touch option for each stepper provides these benefits / possibilities (experimental):
#  - on extruder stepper allows for the automatic detection of the nozzle!
#  - on selector stepper allows for the automatic detection of filament stuck in the gate and subsequent recovery
#  - on gear stepper allows for the automatic detection of the extruder entrance
#
# These sound wonderful right?  They are, but there are caveats:
#  - The external EASY-BRD and ERB mcu's are terrible at detecting stallguard and often result in an "undervoltage
#    error". It is generally possible to get selector touch (TMC2209) tuned especially if you set 'stealthchop_threshold'
#    to a value greater than homing speeds and less than move speed. I.e. the stepper runs in stealthchop mode when
#    homing. [klipper experts will know that it switches the chip mode automatically to stealthchop and then back for
#    Stallguard2 support, howver the automatic switching back to spreadcycle at the end homing move seems to provoke
#    the error condition and setting 'shealthchip_threshold' appropriately avoids this condition. More than you wanted
#    to know I'm sure!
#  - I have not had much luck with touch (stallguard) on the gear stepper with EASY-BRD and ERB mcu's and you really
#    want the extra torque of spreadcycle so adjusting 'stealthchop_threshold' is not really an option
#  - Enabling on the extruder stepper is viable but you will likley have to change jumpers on your main mcu to expose
#    the DIAG pin for whichever driver the extruder stepper is connected to.
#
# In summary, "touch" homing with your MMU is an advanced option that requires patience and careful tuning.  Everything
# works with regular endstops and there are workaround options for certain homing points (like extruder entry) in
# the absence of any endstop. I'm really interested in creative setups. Ping me on Discord (moggieuk#6538)
#
# See 'mmu.cfg' for serial definition and pins aliaes
#
# HOMING CAPABLE EXTRUDER (VERY ADVANCED) -----------------------------------------------------------------------------
# With Happy Hare installed even the extruder can be homed. You will find the usual 'endstop' parameters can be added
# to your '[extruder]' section.  Useless you have some clever load cell attached to your nozzle it only really makes
# sense to configure stallguard style "touch" homing. To do this add lines similar to this to your existing
# '[extruder]' definition in printer.cfg.
#
#    [extruder]
#    endstop_pin: tmc2209_extruder:virtual_endstop
#
# Also be sure to add the appropriate stallguard config to the TMC section, e.g.
#
#    [tmc2209 extruder]
#    diag_pin: E_DIAG		# Set to MCU pin connected to TMC DIAG pin for extruder
#    driver_SGTHRS: 100		# 255 is most sensitive value, 0 is least sensitive
#
# Happy Hare will take care of the rest and add a 'mmu_ext_touch' endstop automatically
#


# FILAMENT DRIVE GEAR STEPPER  -----------------------------------------------------------------------------------------
# Note that 'toolhead' & 'mmu_gear' endstops will automatically be added if a toolhead sensor or gate sensor is defined
#
# The default values are tested with the ERCF BOM NEMA14 motor. Please adapt these values to the motor you are using
# Example : for NEMA17 motors, you'll usually use higher current
#
[tmc5160 stepper_mmu_gear]
cs_pin: mmu:MMU_GEAR_UART                                            # 选择器轴SPI_EN通信需要
spi_bus:spi1a
interpolate: True                                                    # 是否开启256微步插值
run_current: 0.800                                                   # 运行电流
hold_current: 0.2                                                    # 空闲电流
sense_resistor: 0.075                                                # 感测电阻
stealthchop_threshold: 0                                             # 静音阀值（如果不需要静音，请将数值改为0）
#
# Uncomment two lines below if you have TMC and want the ability to use filament "touch" homing with gear stepper
#diag_pin: ^mmu:MMU_GEAR_DIAG		# Set to MCU pin connected to TMC DIAG pin for gear stepper
#driver_SGTHRS: 60			# 255 is most sensitive value, 0 is least sensitive

[stepper_mmu_gear]
step_pin: mmu:MMU_GEAR_STEP
dir_pin: mmu:MMU_GEAR_DIR
enable_pin: !mmu:MMU_GEAR_ENABLE
rotation_distance: 22.56382		# Bondtech 5mm Drive Gears. Overriden by 'mmu_gear_rotation_distance' in mmu_vars.cfg
gear_ratio: 50:17			# E.g. ERCF 80:20, Tradrack 50:17
microsteps: 16 				# Recommend 16. Increase only if you "step compress" issues when syncing
full_steps_per_rotation: 200		# 200 for 1.8 degree, 400 for 0.9 degree
#
# Uncomment the two lines below to enable filament "touch" homing option with gear motor
#extra_endstop_pins: tmc2209_stepper_mmu_gear:virtual_endstop
#extra_endstop_names: mmu_gear_touch


# SELECTOR STEPPER  ----------------------------------------------------------------------------------------------------
# Consult doc if you want to setup selector for "touch" homing instead or physical endstop
#
[tmc5160 stepper_mmu_selector]
cs_pin: mmu:MMU_SEL_UART                                             # 选择器轴SPI_EN通信需要
spi_bus:spi1a
interpolate: True                                                    # 是否开启256微步插值
run_current: 0.600                                                   # 运行电流
hold_current: 0.2                                                    # 空闲电流
sense_resistor: 0.075                                                # 感测电阻
stealthchop_threshold: 0                                             # 静音阀值（如果不需要静音，请将数值改为0）
#
# Uncomment two lines below if you have TMC and want to use selector "touch" movement
#diag_pin: ^mmu:MMU_SEL_DIAG 		# Set to MCU pin connected to TMC DIAG pin for selector stepper
#driver_SGTHRS: 75			# 255 is most sensitive value, 0 is least sensitive

[stepper_mmu_selector]
step_pin: mmu:MMU_SEL_STEP
dir_pin: mmu:MMU_SEL_DIR
enable_pin: !mmu:MMU_SEL_ENABLE
rotation_distance: 40
microsteps: 16 				# Don't need high fidelity
full_steps_per_rotation: 200		# 200 for 1.8 degree, 400 for 0.9 degree
endstop_pin: ^mmu:MMU_SEL_ENDSTOP	# Selector microswitch
endstop_name: mmu_sel_home
#
# Uncomment two lines below to give option of selector "touch" movement
#extra_endstop_pins: tmc2209_stepper_mmu_selector:virtual_endstop
#extra_endstop_names: mmu_sel_touch


# SELECTOR SERVO -------------------------------------------------------------------------------------------------------
# Basic servo PWM setup. If these values are changed then the angles defined for different positions will also change
#
[mmu_servo mmu_servo]
pin: mmu:MMU_SERVO
maximum_servo_angle: 90
minimum_pulse_width: 0.00085 
maximum_pulse_width: 0.00215


# OPTIONAL GANTRY SERVO FOR FILAMETRIX FILAMENT CUTTER ----------------------------------------------------------------
# Basic servo PWM setup. If these values are changed then the angles defined for different positions will also change
#
# Uncomment only if you have gantry servo
#
#[mmu_servo mmu_gantry_servo]
#pin: 
#maximum_servo_angle:180
#minimum_pulse_width: 0.00075
#maximum_pulse_width: 0.00225
#initial_angle: 180


# ENCODER -------------------------------------------------------------------------------------------------------------
# Encoder measures distance, monitors for runout and clogging and constantly calculates % flow rate
# Note that the encoder_resolution set if is purely a default to get started.  It is set through calibration
# and stored in mmu_vars.cfg
#
[mmu_encoder mmu_encoder]
encoder_pin: ^mmu:MMU_ENCODER		# EASY-BRD: ^PA6, Flytech ERB: ^gpio22
encoder_resolution: 1.0			# This is just a starter value. Overriden by `mmu_encoder_resolution` in mmm_vars.cfg
desired_headroom: 2.0			# The clog/runout headroom that MMU attempts to maintain (closest point to triggering runout)
average_samples: 1			# The "damping" effect of last measurement (higher value means slower clog_length reduction)
flowrate_samples: 20			# How many extruder "movements" on the encoder to measure over for flowrate calc


# FILAMENT SENSORS -----------------------------------------------------------------------------------------------------
# Define the pins for optional sensors in the filament path. All but the pre-gate sensors will be automatically setup as
# both endstops (for homing) and sensors for visibility purposes.
#
# 'pre_gate_switch_pin_X' .. 'mmu_pre_gate_X` sensor detects filament at entry to MMU. X=gate number (0..N)
# 'gate_switch_pin'       .. 'mmu_gate' sensor detects filament at the gate of the MMU
# 'toolhead_switch_pin'   .. 'toolhead' sensor detects filament after extruder entry
# 'extruder_switch_pin'   .. 'extruder' sensor detects filament just before the extruder entry
#
# Sync motor feedback will typically have a tension switch (more important) or both tension and compression
# 'sync_feedback_tension_pin'     .. pin for switch activated when filament is under tension
# 'sync_feedback_compression_pin' .. pin for switch activated when filament is under compression
#
# Simply define pins for any sensor you want to enable, if pin is not set (or the alias is empty) it will be ignored.
# You can also just comment out what you are not using
#
[mmu_sensors]
#pre_gate_switch_pin_0: ^mmu:MMU_PRE_GATE_0
#pre_gate_switch_pin_1: ^mmu:MMU_PRE_GATE_1
#pre_gate_switch_pin_2: ^mmu:MMU_PRE_GATE_2
#pre_gate_switch_pin_3: ^mmu:MMU_PRE_GATE_3
#pre_gate_switch_pin_4: ^mmu:MMU_PRE_GATE_4
#pre_gate_switch_pin_5: ^mmu:MMU_PRE_GATE_5
#pre_gate_switch_pin_6: ^mmu:MMU_PRE_GATE_6
#pre_gate_switch_pin_7: ^mmu:MMU_PRE_GATE_7

#gate_switch_pin: ^mmu:MMU_GATE_SENSOR
#extruder_switch_pin: 
toolhead_switch_pin: sb2040:gpio28 

#sync_feedback_tension_pin: 
#sync_feedback_compression_pin: 


## MMU OPTIONAL NEOPIXEL LED SUPPORT ------------------------------------------------------------------------------------
## Define the led connection, type and length
##
## (comment out this section if you don't have leds)
#[neopixel mmu_leds]
#pin: mmu:MMU_NEOPIXEL
#chain_count: 9			# Number gates x1 or x2 + 1 (if you want status)
#color_order: GRBW		# Set based on your particular neopixel specification
#
#
## MMU LED EFFECT SEGMENTS ----------------------------------------------------------------------------------------------
## Define neopixel LEDs for your MMU. The chain_count must be large enough for your desired ranges:
##   exit   .. this set of LEDs, one for every gate, usually would be mounted at the exit point of the gate
##   entry  .. this set of LEDs, one for every gate, could be mounted at the entry point of filament into the MMU/buffer
##   status .. this single LED represents the status of the currently selected filament
##
## Note that all sets are optional. You can opt simple to have just the 'exit' set for example. The advantage to having
## both entry and exit LEDs is, for example, so that 'entry' can display gate status while 'exit' displays the color
## 
## The effects requires the installation of Julian Schill's awesome LED effect module:
##   https://github.com/julianschill/klipper-led_effect
## LED's are indexed in the chain from 1..N. Thus to set up LED's on 'exit' and a single 'status' LED on a 4 gate MMU:
##   exit_range:   1-4
##   status_index: 5
## In this example no 'entry' set is configured.
##
## Note the range order is important and depends on your wiring. Thus 1-4 and 4-1 both represent the same LED range
## but mapped to increasing or decreasing gates respectively
##
## Note that Happy Hare provides a convenience wrapper [mmu_led_effect] that not only creates an effect on each of the
## [mmu_leds] specified segments but also each individual LED for atomic control. See mmu_leds.cfg for examples
##
## (comment out this section if you don't have leds)
#[mmu_leds]
#num_gates: 9
#led_strip: neopixel:mmu_leds
#exit_range: 1-9
#entry_range: 9
#status_index: 9
#frame_rate: 24
#
```

