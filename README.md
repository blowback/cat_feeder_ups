# UPS for SureFeed Microchip Pet Feeder Connect

The feeder takes 4 'C' cell batteries. I have 7 feeders. Alkaline batteries 
get expensive real fast and murder the planet, and NiMH cells take an age to 
charge - you spend your whole life swapping batteries in and out of the charger.

The plan: build a miniature UPS that fits inside one of the 2xC-cell battery compartments,
and use it to maintain a 26650 LiPo in the other. Each feeder will be essentially 
mains powered (via a USB adaptor) most of the time: in the event of a power outage 
it will fail over to the battery, which should be sufficient to run it for a couple 
of weeks if necessary.

The other beauty of this approach: the battery has the capacity to handle the current 
draw (inrush and steady-state) when the motor is operating.

## Power consumption analysis

NiCad C cells are nominally 1.2V, so the pack is 4.8V nominal. At 5000mAh rated 
(let's be realistic and call it 2500mAh) and a two-week runtime, we can work out the average current draw:

Energy budget per feeder:

Pack energy: 4.8V × 2.5Ah = 12 Wh realistic
Runtime: 14 days = 336 hours
Average power: 12 Wh / 336 h ≈ 36 mW average
Average current at 4.8V: ≈ 7.5 mA average

**Average** current during motor operation was measured at 250 mA (cheap-ass bench PSU) - 
the peak current is probably 5x that.

## Physical constraints

A standard C cell is:

Diameter: 26.2mm
Length: 50.0mm

Each "slot" holds 2 × C cells stacked = 100mm long × 26.2mm diameter. 

With two slots side by side we have a compartment roughly 100mm deep × ~60mm wide × ~30mm tall (the slots sit in a tray).

### The slot geometry, precisely:

26 mm diameter circular cross-section (C-cell slot)
The usable PCB width isn't 26 mm — it's the chord of that circle at whatever depth the PCB sits. 
If the PCB sits centrally, the usable rectangle inscribed in a 26 mm circle is about 
22 mm wide × 22 mm tall, but since the slot is deep (100 mm) and we're filling it lengthwise, 
we treat the cross-section as a constraint on width only. Realistically 22 mm safe width, 
with the PCB sitting in the bottom of the cylindrical channel.

Length: 90 mm available (100 mm slot minus ~10 mm for wiring clearance to the cell)
Height (component clearance above PCB): roughly 8–10 mm before we hit the slot wall curvature

So we can't afford any big electrolytics or inductors.

## LiPo backup capacity in one C-cell slot:

A single-cell LiPo fits in one C-cell bay (≤26mm diameter, ≤100mm long) — 
cylindrical 26650 LiPo cells are 26mm × 65mm, giving around 4000–5000 mAh at 3.7V nominal = ~15–18 Wh. 

That's actually more energy than the current NiCadx! 

A flat pouch LiPo of similar volume would also work and might be easier to source (e.g. a 103450 or similar).

At 36mW average draw, a 5000mAh/3.7V LiPo = 18.5 Wh → 18.5 Wh ÷ 0.036 W ≈ 514 hours ≈ 21 days of backup. Nice.

Cat biscuits will have run out long before that!

## Proposed architecture

- a LiPo charge controller with "power path" management so that we can use the battery's capacity to absorb motor current demandss
- a boost convertor to boost the LiPo up to 5v. 

## Device selection 


After many, many false starts:

 - BQ24079  for the LiPo controller
 - TPS61235P boost convertor - fixed 5.1v output  and very minimal component controller
 - SPM5020T-1R0M inductor from TDK: nice and compact: 5x2mm, 11A Isat. 

 Those packages are all quite fiddly, and we'll prolly have to go to 0402 for the discretes. 
Maybe time to experiment with having a boardhouse stuff the board!  I'm thinking JLCB...

## JLPCB substitutions

- L1: was C408536 now C695604
- U2: was unspecified! now C140287 (has no SYSOFF pin, but is same package with a NC pad so no schematic/pcb change)
- J1: was C173752 - shouldn't be in BOM (is thru-hole)
- D2: was C2286 (RED) but we want GREEN so -> C12624
- R4, R6: wrong part, need 1.5k 0402 -> C114759
- R5: wrong part, need 10k 0402 -> C60490
- R7: wrong part, need 1.18k -> C3016052
- R8: wrong part, need 1.13k -> C453521





