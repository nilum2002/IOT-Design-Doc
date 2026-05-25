# Water Level Measurement Method 

Since the hospital prefers not to install any sensors inside the tank
due to the periodic cleaning process, the possible way to install
sensors is either on the top lid of the tank or externally at the
discharge outlet point.

## Configurations of the Water Supply Tank

   **Parameter**    **Value**
  --------------- -------------
      Volume       2000 Liters
     Diameter         50 cm
      Height         10.16 m

  : Water Supply Tank Specifications

## Method : Inferential Software Flow Integration Routine

To satisfy the hospital's strict guidelines preventing any physical
instrumentation from entering the 2000-liter purified water supply tank,
a real-time Inferential Software Flow Integration Routine is deployed
within the central controller.

This model tracks the exact volume of water entering and leaving the
vessel by intercepting high-frequency square-wave pulse signals from two
inline [SEN-HZ21WA miniature nylon Hall Effect turbine flow
sensors](https://www.duino.lk/product/ht30-water-flow-sensor-ac/)
installed completely external to the tank housing.\
This product costs approximately **LKR 3000**.

### Hardware Specifications {#hardware-specifications .unnumbered}

  **Hardware Parameter**     **Specification Value**
  -------------------------- --------------------------------------------------------
  Model Number               SEN-HZ21WA Miniature Water Sensor
  Material Construction      Nylon PA66 Polymer with Stainless Steel Impeller Shaft
  Connection Profile         $1/2$ inch G-Thread / DN15
  Operational Flow Range     $1.0$ to $30.0$ L/min
  Excitation Voltage Input   5V DC
  Sensor Mechanics           Magnetic impeller with isolated Hall Effect sensor
  Signal Output Type         5V square-wave digital pulse train

  : Flow Sensor Specifications

Every $\Delta t = 1.0$ second, the controller samples the background
registers and calculates the discrete fluid parameters.

The current net volume of water inside the tank is updated using the
following discrete Riemann summation framework.

$$V_{\text{current(new)}} =
V_{\text{current(old)}} + \Delta V_{\text{net}}$$

$$\Delta V_{\text{net}} =
(Q_{\text{in}} - Q_{\text{out}})\times \Delta t$$

The instantaneous flow velocity parameters are derived in liters per
minute.

$$Q_{\text{in}} =
\left(
\frac{\text{Inflow Pulse Count}}
{\text{K-Factor}}
\right)\times 60$$

$$Q_{\text{out}} =
\left(
\frac{\text{Outflow Pulse Count}}
{\text{K-Factor}}
\right)\times 60$$

The incremental volumetric deviation becomes:

$$\Delta V_{\text{net}} =
\frac{\text{Inflow Pulses} - \text{Outflow Pulses}}
{450}$$

This value is continuously added to the global storage variable to track
active storage levels.

### Converting Net Volume to Linear Height {#converting-net-volume-to-linear-height .unnumbered}

To drive the continuous digital readout gauge on the dashboard, the
controller converts the running volume variable into a linear height
profile.

Given the cylindrical geometric properties of the 2000L tank:

$$r = 0.25m$$

$$A = \pi r^2$$

$$A = \pi(0.25)^2 \approx 0.19635m^2$$

The real-time height profile is calculated as:

$$V_{m^3} =
\frac{V_{\text{current}}}{1000}$$

$$H =
\frac{V_{m^3}}{A}$$

This dynamic variable continuously updates the HMI graphical dashboard,
allowing hospital maintenance crews to clean and manually scrub the
internal tank structure without risking physical damage to
instrumentation.

# Concentrated Chemical Level Estimation Method

To adhere to the strict constraint preventing the installation of level
sensors inside the 10-liter concentrated chemical container, a highly
reliable, non-intrusive Dual-Verification Framework is deployed.

## Method: Dual-Verification (Mass-Computational) Level Tracking for Concentrated Chemical Estimation

The system is configured around a standard 10-liter cylindrical or
rectangular chemical supply vessel. The target fluid handled is
industrial-grade Isopropyl Alcohol (IPA), which exhibits physical
properties distinct from water.

### Chemical Properties and Container Specifications {#chemical-properties-and-container-specifications .unnumbered}

- **Fluid Density ($\rho_{\text{chem}}$):** At room temperature
  ($27^\circ C$ - $32^\circ C$), the density of Isopropyl Alcohol is:

  $$\rho_{\text{chem}} = 785 \text{ kg/m}^3$$

  which is equivalent to:

  $$0.785 \text{ g/mL} = 0.785 \text{ kg/L}$$

- **Maximum Fluid Mass:**

  A completely full 10-liter chemical payload exerts a net liquid mass
  of:

  $$M_{\text{fluid}}
      =
      \text{Volume} \times \rho_{\text{chem}}$$

  $$M_{\text{fluid}}
      =
      10\text{ L} \times 0.785\text{ kg/L}
      =
      7.85\text{ kg}$$

- **Container Tare Weight:**

  The empty plastic chemical canister exhibits a static dead weight:

  $$M_{\text{tare}} \approx 0.35\text{ kg} - 0.50\text{ kg}$$

### 1. Primary Physical Verification: External Load Cell Matrix {#primary-physical-verification-external-load-cell-matrix .unnumbered}

An industrial strain-gauge load cell platform is installed directly
beneath the canister assembly, isolating the electrical components
entirely from chemical contact.

When a fresh container is positioned on the platform, the system filters
out the weight of the platform frame and the container's dry plastic
shell ($M_{\text{tare}}$).

The controller samples the raw live gross weight ($M_{\text{current}}$)
and computes the true remaining physical volume ($V_{\text{mass}}$) via
mass balance:

$$V_{\text{mass}} (\text{Liters})
=
\frac{M_{\text{current}} - M_{\text{tare}}}
{\rho_{\text{chem}}}$$

Because the fluid density is significantly lighter than water, this
equation prevents a $21.5\%$ overestimation error that would occur if
water-density constants were mistakenly applied to the software logic.

### 2. Secondary Verification: Computational Flow Integration Model {#secondary-verification-computational-flow-integration-model .unnumbered}

Operating in parallel with the weight sensor, an inferential software
loop serves as a digital twin redundancy check.

As the chemical dosing pump draws fluid from the container, it passes
through an inline micro-flow meter.

Because microcontrollers execute code within sequential loops at finite
clock edges, the theoretical continuous integral for fluid displacement
is represented as:

$$V_{\text{dispensed}}
=
\int_{0}^{t} Q_{\text{chem}}(t)\,dt$$

$$\Delta t = 1.0\text{ second}$$

The running algorithmic volume register updates dynamically every clock
cycle:

$$V_{\text{algorithm(new)}}
=
V_{\text{algorithm(old)}} - \Delta V$$

$$\Delta V
=
Q_{\text{chem}} \times \Delta t$$

Where:

- $Q_{\text{chem}}$ = Real-time volumetric flow rate extracted from the
  pulse frequency of the micro-flow meter ($\text{Liters/Second}$)

- $\Delta V$ = Absolute incremental volume drawn during the 1-second
  clock step ($\text{Liters}$)

# Hardware Specifications and Comparative Market Analysis

This section presents a systematic evaluation of all hardware components
required for the automated chemical dosing system. For each subsystem,
three commercially available product options sourced from the Sri Lankan
market and Foreign market are compared across cost, specifications, and
suitability for the application. The recommended product for each
category is highlighted in [green]{style="background-color: chosen"}.

## Water Level Measurement - Inferential Flow Sensors

The inferential software flow integration routine requires two inline
Hall Effect turbine flow sensors installed externally on the inlet and
outlet pipes of the 2000-liter water supply tank. This approach
eliminates any physical instrumentation inside the tank, fully
satisfying the hospital's strict cleaning protocol constraint. The total
cost for two sensors is approximately **LKR 6,000**.

**Comparative Analysis:**

  [**Product**]{style="color: white"}    [**Supplier**]{style="color: white"}                                         [**Est. Cost**]{style="color: white"}   [**Key Specifications**]{style="color: white"}                               [**Verdict**]{style="color: white"}
  -------------------------------------- ---------------------------------------------------------------------------- --------------------------------------- ---------------------------------------------------------------------------- --------------------------------------------------------------------------------------
  SEN-HZ21WA (HT30) Hall Effect sensor   [duino.lk](https://www.duino.lk/product/ht30-water-flow-sensor-ac/)          $\sim$LKR 3,000                         1--30 L/min, 5V DC, 450 pulses/L, Nylon PA66 + stainless steel shaft, DN15   [[**Chosen**]{style="color: badgegreen"}]{style="background-color: lightgreen"}
  G½ inch Hall sensor                    [duino.lk](https://www.duino.lk/product/g0-5-inch-water-flow-hall-sensor/)   $\sim$LKR 900                           1--30 L/min, 5V DC, lower-cost plastic body                                  [[**Budget alt.**]{style="color: badgeamber"}]{style="background-color: lightamber"}
  HT30 (AC-rated variant)                [duino.lk](https://www.duino.lk/product/ht30-water-flow-sensor-ac/)          $\sim$LKR 3,000                         Same model as chosen option; identical specifications                        [[**Duplicate**]{style="color: badgeblue"}]{style="background-color: lightblue"}

  : Water Flow Sensor Comparison (Inferential Level Method)

**Justification:** The SEN-HZ21WA is selected for its stainless steel
impeller shaft, which provides superior corrosion resistance compared to
fully plastic alternatives. At LKR 3,000 per unit, it is significantly
cheaper than the commented-out ultrasonic option (LKR 65,500) and the
industrial load cell option (LKR 17,479), while fully meeting the
no-sensor-inside-tank constraint.

## Chemical Level Estimation - Load Cell

The dual-verification framework uses a strain-gauge load cell platform
mounted beneath the 10-liter IPA canister. The maximum payload is
approximately 8.35 kg (7.85 kg fluid + 0.50 kg container). The load cell
must accommodate this weight with adequate safety margin.

**Comparative Analysis:**

  [**Product**]{style="color: white"}   [**Supplier**]{style="color: white"}                                        [**Est. Cost**]{style="color: white"}   [**Key Specifications**]{style="color: white"}                    [**Verdict**]{style="color: white"}
  ------------------------------------- --------------------------------------------------------------------------- --------------------------------------- ----------------------------------------------------------------- -----------------------------------------------------------------------------------
  CZL-601 60 kg S-type load cell        [tronic.lk](https://tronic.lk/product/czl-601-load-cell-60kg-130x30x22mm)   $\sim$LKR 2,500                         60 kg capacity, S-type aluminium, HX711 compatible                [[**Suitable**]{style="color: badgegreen"}]{style="background-color: lightgreen"}
  20 kg load cell                       [duino.lk](https://www.duino.lk/product/20kg-load-cell-weighing-sensor/)    $\sim$LKR 1,200                         20 kg max marginal for 8.35 kg payload; minimal overload margin   [[**Risky**]{style="color: badgered"}]{style="background-color: lightred"}
  50 kg body load cell                  [duino.lk](https://www.duino.lk/product/50kg-body-load-cell/)               $\sim$LKR 1,800                         50 kg max, ample overload headroom, HX711 compatible              [[**Best fit**]{style="color: badgegreen"}]{style="background-color: lightgreen"}

  : Load Cell Comparison (Chemical Canister Weighing)

**Justification:** The 50 kg load cell provides a 6$\times$ safety
margin over the maximum payload of 8.35 kg, ensuring reliable operation
without risk of overload. The 20 kg option is technically borderline and
unsuitable for a hospital environment where reliability is critical. The
60 kg CZL-601 functions correctly but reduces measurement resolution
since the active range (0--8.35 kg) is a smaller fraction of its full
scale.

## Dispensing Tank Level Sensors

Each 750 mL dispensing tank requires a single low-level binary sensor to
detect when the chemical level falls below the refill threshold. The
sensor is fitted externally to avoid chemical contact, and only on/off
detection is required --- continuous level measurement is not needed for
this subsystem.

**Comparative Analysis:**

  [**Product**]{style="color: white"}      [**Supplier**]{style="color: white"}                                                                                     [**Est. Cost**]{style="color: white"}   [**Key Specifications**]{style="color: white"}                                 [**Verdict**]{style="color: white"}
  ---------------------------------------- ------------------------------------------------------------------------------------------------------------------------ --------------------------------------- ------------------------------------------------------------------------------ ------------------------------------------------------------------------------------
  JSN-SR04T waterproof ultrasonic sensor   [lk-tronics.com](https://lk-tronics.com/product/jsn-sr04-waterproof-ultrasonic-distance-measuring-transducer-sensors/)   $\sim$LKR 950                           IP67 rated, 20--450 cm range, chemical-splash resistant, sealed probe          [[**BestFit**]{style="color: badgegreen"}]{style="background-color: lightgreen"}
  HC-SR04 standard ultrasonic              [tronic.lk](https://tronic.lk/product/hc-sr04-4pin-ultrasonic-sensor-module)                                             $\sim$LKR 350                           2--400 cm range, NOT waterproof, exposed PCB --- chemical exposure risk        [[**Not Suitable**]{style="color: badgered"}]{style="background-color: lightred"}
  DFRobot gravity liquid sensor            [dfrobot.com](https://www.dfrobot.com/product-2059.html)                                                                 $\sim$LKR 58, 000                       Non-contact through-wall capacitive detection, corrosion-proof, fully sealed   [[**Budget alt.**]{style="color: badgeblue"}]{style="background-color: lightblue"}

  : Dispensing Tank Level Sensor Comparison

**Justification:** The JSN-SR04T is the optimal choice. Its IP67
waterproof rating protects against chemical splashes in the dispensing
environment, unlike the bare HC-SR04. The DFRobot capacitive sensor
offers superior non-contact through-wall sensing and is the preferred
option if the dispensing tanks are sealed plastic containers, at a
higher cost premium.

## Flow Meters - Chemical Dosing Line

The chemical dosing pump flow rate is controlled via a PWM signal but
does not guarantee accurate flow. An inline flow meter on the chemical
line measures the actual dispensed volume in real time, forming the
secondary verification channel of the dual-verification framework.

**Comparative Analysis:**

  [**Product**]{style="color: white"}   [**Supplier**]{style="color: white"}                                                           [**Est. Cost**]{style="color: white"}   [**Key Specifications**]{style="color: white"}                               [**Verdict**]{style="color: white"}
  ------------------------------------- ---------------------------------------------------------------------------------------------- --------------------------------------- ---------------------------------------------------------------------------- --------------------------------------------------------------------------------------------
  YF-S201 Hall effect flow sensor       [lk-tronics.com](https://lk-tronics.com/product/yf-s201-water-flow-sensor/)                    $\sim$LKR 650                           1--30 L/min, 5V, Hall pulse output, ABS plastic body                         [[**Check IPA compat.**]{style="color: badgeamber"}]{style="background-color: lightamber"}
  Magnetic plastic flow sensor          [duino.lk](https://www.duino.lk/product/magnetic-plastic-water-flow-sensor/)                   $\sim$LKR 600                           Low flow range, food-grade plastic body                                      [[**Verify compat.**]{style="color: badgeamber"}]{style="background-color: lightamber"}
  YF-S201C G½ Hall effect sensor        [duino.lk](https://www.duino.lk/product/yf-s201c-g1-2-hall-effect-liquid-water-flow-sensor/)   $\sim$LKR 700                           G½ thread fitting, Hall pulse output, slightly more robust pipe connection   [[**Good fit**]{style="color: badgegreen"}]{style="background-color: lightgreen"}

  : Chemical Line Flow Meter Comparison

**Important Note:** IPA (Isopropyl Alcohol) can degrade standard NBR
rubber O-rings and ABS seals over time. Before finalising any flow
sensor for the chemical line, the impeller shaft material, O-ring
compound, and housing material must be verified for IPA compatibility.
PTFE or Viton-lined sensors are the recommended alternative for
long-term deployments.

## Control Valves - Dispensing Tank Inlets

Each dispensing tank requires a solenoid-controlled valve to regulate
chemical inflow. The system may serve tens of dispensing tanks
distributed throughout the hospital premises, making per-unit cost a
significant factor.

**Comparative Analysis:**

  [**Product**]{style="color: white"}    [**Supplier**]{style="color: white"}                                                 [**Est. Cost**]{style="color: white"}   [**Key Specifications**]{style="color: white"}                                [**Verdict**]{style="color: white"}
  -------------------------------------- ------------------------------------------------------------------------------------ --------------------------------------- ----------------------------------------------------------------------------- ----------------------------------------------------------------------------------------
  Tuya Zigbee smart gas valve            [daraz.lk](https://www.daraz.lk)                                                     $\sim$LKR 7,769                         Zigbee wireless, smart-home integration, timer function, ½--1 inch            [[**Overkill / costly**]{style="color: badgered"}]{style="background-color: lightred"}
  FPD270A ½ inch solenoid valve (220V)   [duino.lk](https://www.duino.lk/product/fpd270a-1-2-220v-electric-solenoid-valve/)   $\sim$LKR 900                           220V AC, ½ inch, normally closed, direct relay control from MCU               [[**Best value**]{style="color: badgegreen"}]{style="background-color: lightgreen"}
  4 mm pneumatic flow control valve      [duino.lk](https://www.duino.lk/product/4mm-pneumatic-air-flow-control-valve/)       $\sim$LKR 300                           Pneumatic, 4 mm tube fitting --- designed for air/gas, not liquid pipelines   [[**Wrong type**]{style="color: badgered"}]{style="background-color: lightred"}

  : Control Valve Comparison (Dispensing Tank Inlets)

**Justification:** With potentially tens of dispensing tanks, the
FPD270A at LKR 900 per valve is the clear choice. It is driven through a
relay module directly from the MCU, requires no wireless protocol stack,
and is purpose-built for liquid pipelines. The Tuya Zigbee valve is
designed for smart-home irrigation and gas shutoff applications and
would add unnecessary cost and protocol complexity to the IoT
architecture.

## Variable Rate Pump - Chemical Dosing

The chemical dosing pump must deliver IPA at a controllable flow rate
via PWM, with the actual flow measured by the inline flow meter. Since
IPA is a chemical solvent, fluid compatibility with the pump internals
is the primary selection criterion.

**Comparative Analysis:**

  [**Product**]{style="color: white"}   [**Supplier**]{style="color: white"}                                                                                                         [**Est. Cost**]{style="color: white"}   [**Key Specifications**]{style="color: white"}                                                [**Verdict**]{style="color: white"}
  ------------------------------------- -------------------------------------------------------------------------------------------------------------------------------------------- --------------------------------------- --------------------------------------------------------------------------------------------- -----------------------------------------------------------------------------------------------
  100 W submersible water pump          [duino.lk](https://www.duino.lk/product/100w-submersible-water-pump-4-5m/)                                                                   $\sim$LKR 3,500                         PWM-controllable, 4.5 m head --- designed for water, chemical compatibility unverified        [[**$\triangle$ Compat. risk**]{style="color: badgered"}]{style="background-color: lightred"}
  Peristaltic liquid dosing pump        [duino.lk](https://www.duino.lk/product/peristaltic-liquid-pump-dosing-pump/)                                                                $\sim$LKR 2,800                         Fluid isolated inside silicone/rubber tube; pump body never contacts IPA; PWM speed control   [[**Best for chemicals**]{style="color: badgegreen"}]{style="background-color: lightgreen"}
  220V 1000 L/h submersible pump        [duino.lk](https://www.duino.lk/product/220v-25w-1000l-h-1-8m-submersible-water-pump-for-fish-tank-fountains-hydroponics-ponds-aquariums/)   $\sim$LKR 1,800                         1000 L/h flow rate --- far exceeds dosing requirement for 750 mL dispensing tanks             [[**Flow rate too high**]{style="color: badgered"}]{style="background-color: lightred"}

  : Chemical Dosing Pump Comparison

**Justification:** The peristaltic dosing pump is the architecturally
correct choice for this application. In a peristaltic design, the pumped
fluid travels entirely inside a replaceable tube, meaning the IPA never
contacts the pump motor, housing, or impeller assembly. This eliminates
chemical compatibility concerns entirely. PWM speed control is also more
linear for peristaltic pumps than centrifugal designs, improving dosing
accuracy.

## Summary Cost Comparison

Table [1](#tab:cost_summary){reference-type="ref"
reference="tab:cost_summary"} consolidates the recommended products and
their estimated unit costs.

::: {#tab:cost_summary}
  [**Subsystem**]{style="color: white"}   [**Selected Product**]{style="color: white"}   [**Unit Cost (LKR)**]{style="color: white"}   [**Qty (min.)**]{style="color: white"}
  --------------------------------------- ---------------------------------------------- --------------------------------------------- ----------------------------------------
  Water level --- flow sensor             SEN-HZ21WA (HT30)                              $\sim$`<!-- -->`{=html}3,000                  2
  Chemical level --- load cell            50 kg body load cell                           $\sim$`<!-- -->`{=html}1,800                  1
  Dispensing tank sensor                  JSN-SR04T ultrasonic                           $\sim$`<!-- -->`{=html}950                    $n$ tanks
  Chemical line flow meter                YF-S201C G½                                    $\sim$`<!-- -->`{=html}700                    1
  Control valve (per tank)                FPD270A solenoid                               $\sim$`<!-- -->`{=html}900                    $n$ tanks
  Dosing pump                             Peristaltic pump                               $\sim$`<!-- -->`{=html}2,800                  1

  : Recommended Component Cost Summary
:::

The minimum fixed hardware cost (excluding
dispensing-tank-count-dependent components) is approximately **LKR
14,300**. Each additional dispensing tank requires one JSN-SR04T sensor
($\sim$LKR 950) and one FPD270A solenoid valve ($\sim$LKR 900),
totalling **LKR 1,850 per tank**.
