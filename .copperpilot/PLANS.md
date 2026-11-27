# Project Plans Index

## Active Plan
Rules for every run

Tracks on F.Cu, In2 and B.Cu only. Never In1.
In2 tracks can only exist in the logic half: the existing keepout blocks y ≥ 55 mm. So anything landing on the driver pins at y 55 to 64 mm must finish on F.Cu or B.Cu.
Every In2 track cuts the 3.3V pour, so check for copper islands after groups 5 to 8.
Each net gets at most 2 vias in total, counting its fan-out via.
Width equals the netclass exactly, and 0.3 mm clearance where space allows.
Gate for each group: zero new DRC errors, zero unconnected on its nets, and KEEP SET hashes unchanged.
If a gate fails after the attempts: leave the board as it was and report which nets and why.

By hand, not the router: USB pair (F.Cu, no vias, 125 mm limit), PH0/PH1 to Y1, and the gate and sense nets in the power stage if the audit shows problems.

Group 3 is about 86 connections, not 38. 39 of the 43 +3.3V pads each need their own connection to the In2 pour. Only the rail's four driver-side pads (U8:49, C119:1, U7:49, C80:1) are outside the pour. Group 3 is therefore split into 3a to 3d.
The 2-via rule can't hold for +3.3V. It needs about 39 fan-out vias into the In2 pour. I suggest exempting pour fan-out vias for +3.3V and keeping the 2-via cap on every other net.
Keep the +3.3V fan-out vias at y < 55 mm. From y 55.5 down, the In2 layer is the VDC pour, so a 3.3V via there would short to VDC. Watch C139:1 (y 54.5), C143:1 (53.5) and R60:2 (53.7), which sit close to that edge.
GVDD ties together U7:13, U8:13 and U2:1. Pin 13 is the DRV8301's own gate-supply output. That puts two regulator outputs in parallel over a 75 mm trunk (TP3 sits in the middle). Check the schematic intent before routing the trunk. If it's unintended, leave the trunk out and keep each GVDD island local.
The In2 keepout (y ≥ 55 mm) doesn't affect groups 1 and 2. Driver-pin stubs there finish on F.Cu or B.Cu anyway.
Route order (net list per group)

Net names are shortened. Widths (mm): Default and Sense 0.16, Gate_Drv and Power 0.2, Supply 0.3, Supply_5V 0.5, CAN 0.2.

G1: U8 local (left driver), 15

Net-(U8-…): BST_A, BST_B, BST_C (Gate_Drv 0.2); CP1, CP2 (Supply 0.3); DC_CAL, DTC, SS_TR (0.16); SO1, SO2 (Sense 0.16)
/Motor_Driver2/AVDD, /Motor_Driver2/DVDD (0.3); /Motor_Driver2/nOCTW (0.16)
/Motor_Driver2/TEMP_SENSE_2 (2 connections, near TH4 at the left edge)

G2: U7 local (right driver), 27

Net-(U7-…): BST_A, BST_B, BST_C (0.2); BST_BK, CP1, CP2 (0.3); COMP (2), DC_CAL, DTC, RT_CLK, SS_TR, VSENSE (2) (0.16); SO1, SO2 (0.16)
Net-(D6-K) (4, Supply 0.3); Net-(C73-Pad1) (0.3)
/Motor Driver/AVDD, /Motor Driver/DVDD (0.3); /Motor Driver/PWRGD, /Motor Driver/nOCTW (0.16)
/Motor Driver/TEMP_SENSE_2 (2)

G3a: MCU local and west 3.3V, 27

Net-(U9-VCAP_1), Net-(U9-VCAP_2) (0.3); Net-(U9-NRST)
BOOT0 (2), DEBUG_SWCLK, DEBUG_SWDIO, Net-(D5-A)
+3.3V fan-out vias for x < 125: R73:1, R71:1, R69:1, R59:2, R60:2, C142:1, U9:1, C145:1, SW1:1, U9:64, C139:1, U9:19, U9:32, C143:1, U9:48, C147:1, D5:1 (17)
+3.3VA: C148:1 to C149:1, and C148:1 to U9:13

G3b: 3.3V mid and east, 25

+3.3V fan-out vias: TP1:1, C22:1, C28:1, U3:5, C150:2, U10:3 (6), plus R72:1, R70:1, R68:1, R3:1, R39:2, C21:1, R40:2, R38:2, U1:8, R11:1, R12:1, R4:1, C31:1, U5:5, U6:5, C25:1 (16)
+3.3VA near U4: C29:1 to C23:1, C23:1 to U4:5, TP2:1 to C29:1

G3c: driver-pin and local rail stubs, 18 (all finish on F.Cu or B.Cu)

+3.3V: U8:49 to C119:1, C119:1 to the pour (via the R60:2 fan-out); U7:49 to C80:1, C80:1 to the pour (via the R38:2/R40:2 fan-out)
+3.3VA: C121:1 to U8:24, C82:1 to U7:24
GVDD: C103:1 to U8:13, C57:1 to U7:13, C4:1 to U2:1, U2:1 to D3:2
+5V (0.5): U3:3 to U3:1, U4:3 to U4:1, C12:1 to U3:3, C13:1 to U4:3, C12:1 to TP5:1, TP5:1 to C13:1, R25:2 to L1:2, D7:1 to U11:5

G3d: long rail trunks, 16 (route along the y ≈ 22–24 band on F.Cu or B.Cu)

+5V: U3:1 to J10:1, J10:1 to J12:2, J12:2 to D7:1, TP5:1 to J11:2, J11:2 to R25:2
+3.3VA (8): C121:1 to C148:1 (50 mm), U8:24 to TH5:1, TH5:1 to TH4:1, C29:1 to TH3:1, U4:5 to TH2:1, TH2:1 to U7:24, C82:1 to TH1:1, U9:13 to TP2:1
GVDD (3): C57:1 to C4:1, C103:1 to TP3:1, TP3:1 to U7:13. Only run these if you confirm the paralleling is intended.

G4: OVP (right edge), 29

OVP_ACTIVE (5), OVP_ACTIVE_DLY (3), OVP_VMID (2), OVP_VREF (2), OVP_VSENSE (4), nOVP_ACTIVE (2)
U3_HI_DRV, U3_LI_DRV (2 each); Net-(U2-HO), Net-(U2-LO) (0.2); Net-(D3-K) (2, 0.3); /Power Management/HS (0.2)
Short legs only for OVP_IN_HI (R16:1 to U5:1) and OVP_IN_LI (R17:1 to U6:1)

G5: motor 1 control (left), 25

M1_AH, M1_AL, M1_BH, M1_BL, M1_CH, M1_CL (U8:17 to 22 to U9:37, 23, 38, 26, 39, 27)
SPI_M1_nCS (U8:8 to U9:3)
M1_SO1 (3), M1_SO2 (3), M1_TEMP (3)
EN_GATE (4), nFAULT (5)

G6: motor 0 control, SPI and aux (right), 27

M0_AH, M0_AL, M0_BH, M0_BL, M0_CH, M0_CL (U7:17 to 22 to U9:41, 34, 42, 35, 43, 36)
SPI_M0_nCS
M0_SO1 (3), M0_SO2 (3), M0_TEMP (3), AUX_TEMP (3)
SPI_SCK, SPI_MOSI, SPI_MISO (2 each)
Long legs of OVP_IN_HI and OVP_IN_LI (to U9:30 and U9:29)

G7: encoders and GPIO, 26

ENC0_A, ENC0_B, ENC0_Z, ENC1_A, ENC1_B, ENC1_Z (3 each)
GPIO_1 to GPIO_8 (1 each)

G8: CAN and USB support, 21

USB_CC1, USB_CC2 (1 each), USB_VBUS_PROT (3), USB_VBUS_RAW (5)
CAN_D, CAN_R (1 each), CAN_H, CAN_L (3 each; CAN class 0.2)
VBUS_S (3)
By hand, not the router
USB pair: USB_DN and USB_DP (U9:44/45 to R63/R64 and TP11/TP12, about 92 mm) and USB_J4_DN and USB_J4_DP (J5, U11, R63/R64). Keep to F.Cu with no vias, a 125 mm limit and 0.155 mm width, and route clear of Y1.
Crystal: Net-(U9-PH0) and Net-(U9-PH1) (Y1, C140, C141, U9:5/6).
Power-stage pours: SH_A, SH_B, SH_C, SL_B, and /Motor_Driver2/SL_C, on both driver sheets, about 10 connections. These are pour-to-pin stitches. Check them in the DRC audit rather than autorouting.
Checks after each group
After G5 to G8, check for copper islands in the 3.3V In2 pour, since every In2 track cuts it.
G1 and G2 are well under 30 connections, so they each fit in one run.



## Completed Plans
- **[V9_7 Brake Terminal Block, Upper-East Chopper Relocation, and Hybrid Hardware VDC OVP](./plans/v9-7-brake-terminal-and-hw-ovp.plan.md)** (`status: done`)
- **[V9.0 DirectFET L8 Upgrade, 180x115 mm Board Expansion, and Power-Stage Copper Rework](./plans/v9-0-directfet-l8-rework.plan.md)** (`status: done`)
- **[V8 Schematic STM32 Pinout Restoration, PCB Parity, and ERC Cleanup](./plans/v8-schematic-parity-and-erc-cleanup.plan.md)** (`status: done`)
- **[Restore Removed Capacitors, Expand Board to 150x98.5 mm, and Place Large High-Power Copper Zones](./plans/pre-order-layout-and-thermistor-fixes.plan.md)** (`status: done`)
