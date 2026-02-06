Scenario Complex 4.1 (adding HMI to Driver)

HMI1 (rcao:InformationSource) - for the driver, the HMI is another source of information
The ADS recognizes a traffic sign, displays it on the HMI, and the driver becomes aware of it.
Scenario: The ego-vehicle’s camera detects an 80 kph speed limit sign.The ADS processes this and displays a “80” symbol on the dashboard HMI. The driver sees the symbol and understands the current speed limit.


// ===================================================================
// Part 1: The New Elements (Corrected with universal relations)
// ===================================================================

// The driver and their components ("parts")
HumanDriver1 isA ago:Human
Brain1 isA rcao:Algorithm
Eyes1 isA rcao:InformationSource
HumanDriver1 hasPart Brain1       // CORRECTED: Universal relationship
HumanDriver1 hasPart Eyes1         // CORRECTED: Universal relationship

// The vehicle and its components ("parts")
EgoVehicle1 hasPart ADS1
EgoVehicle1 hasPart HMI1          // CORRECTED: HMI is a part of the vehicle
HMI1 isA rcao:InformationSource

// Positional relationship remains
HumanDriver1 isIn EgoVehicle1

// The traffic sign is an object in the world
TrafficSign_80kph isA ago:Object
TrafficSign_80kph.type = "Speed Limit"
TrafficSign_80kph.value = "80"

// ===================================================================
// Part 2: The Information Flow (The "Story")
// (The rest of the logic remains unchanged as it was already correct)
// ===================================================================

// --- Step A: Machine Perception (Vehicle sees the sign) ---
PerceptionProcess_ADS1 isA prov:Activity
FrontCamera1 prov:isUsedBy PerceptionProcess_ADS1
ADS1 runs PerceptionProcess_ADS1

PA1_ADS1_of_TS1 isA rcao:PerceptualAwareness
PA1_ADS1_of_TS1 rcao:hasSubject ADS1
PA1_ADS1_of_TS1 rcao:hasObject TrafficSign_80kph
PA1_ADS1_of_TS1 rcao:hasSource FrontCamera1
PA1_ADS1_of_TS1.confidence = 0.99

// --- Step B: Machine-to-Human Communication (ADS displays the info) ---
DisplayOnHMI1 isA ago:Action
ADS1 runs DisplayOnHMI1
DisplayOnHMI1 rcao:usesSource PA1_ADS1_of_TS1
DisplayOnHMI1 rcao:outputsTo HMI1

// --- Step C: Human Perception (Driver sees the HMI) ---
PerceptionProcess_Driver1 isA prov:Activity
Eyes1 prov:isUsedBy PerceptionProcess_Driver1
HMI1 prov:isUsedBy PerceptionProcess_Driver1
HumanDriver1 runs PerceptionProcess_Driver1

PA2_Driver1_of_TS1 isA rcao:PerceptualAwareness
PA2_Driver1_of_TS1 rcao:hasSubject HumanDriver1
PA2_Driver1_of_TS1 rcao:hasObject TrafficSign_80kph
PA2_Driver1_of_TS1 rcao:hasSource HMI1

// --- Step D: Human Comprehension (Driver understands the meaning) ---
ComprehensionProcess_Driver1 isA prov:Activity
Brain1 runs ComprehnsionProcess_Driver1
SpeedLimit_80_Active isA ago:Context

CA2_Driver1_of_SL1 isA rcao:ComprehensionAwareness
CA2_Driver1_of_SL1 rcao:hasSubject Brain1
CA2_Driver1_of_SL1 rcao:hasObject SpeedLimit_80_Active
CA2_Driver1_of_SL1 rcao:derivedFrom PA2_Driver1_of_TS1
CA2_Driver1_of_SL1.description = "I saw the '80' sign on the dashboard; the speed limit is now 80 kph."

PA2_Driver1_of_TS1 isA rcao:PerceptualAwareness
PA2_Driver1_of_TS1 rcao:hasSubject HumanDriver1
PA2_Driver1_of_TS1 rcao:hasObject TrafficSign_80kph // They are aware of the sign...
PA2_Driver1_of_TS1 rcao:hasSource HMI1 // ...because they saw it on the HMI.
PA2_Driver1_of_TS1.confidence = 1.0 // Assumed perfect perception of the HMI display

// --- Step D: Human Comprehension (Driver understands the meaning) ---
// The driver's brain processes the perception and forms a high-level understanding.

ComprehensionProcess_Driver1 isA prov:Activity
Brain1 runs ComprehensionProcess_Driver1
SpeedLimit_80_Active isA ago:Context // The meaning/context the driver understands

CA2_Driver1_of_SL1 isA rcao:ComprehensionAwareness
CA2_Driver1_of_SL1 rcao:hasSubject Brain1 // The brain is the subject
CA2_Driver1_of_SL1 rcao:hasObject SpeedLimit_80_Active
CA2_Driver1_of_SL1 rcao:derivedFrom PA2_Driver1_of_TS1 // Derived from seeing the HMI
CA2_Driver1_of_SL1.description = "I saw the '80' sign on the dashboard; the speed limit is now 80 kph."


## Ammedments and notes from rcao-v0.4.ttl
- Current version lacks structure to represent Indirect Perception (perception mediated by a device) + HMI as dual-core entity as Pyshical Part of the vehicle and Information Source
- Step B: no standard PROV property links a Action tot the device it updates. It can be defined as the activity itself but at the moment i created rcao:outputsTo to model the flow from Algorithm to interface HMI
- No standard PROV property links an Action to the device it updates. I define rcao:outputsTo to model the flow from Algorithm to Interface.