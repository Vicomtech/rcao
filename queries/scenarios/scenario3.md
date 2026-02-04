## Scenario 3 (comprehension by fusion of perception)

- Short: **fused** cooperative awareness (comprehension by fusion of perception)
- Expanded: An ego-vehicle, equipped with an ADS, is driving forward. A pedestrian is ahead. The ego-vehicle LIDAR system detects the pedestrian, and also a RSU Unit detects the pedestrian with a Camera. The RSU Unit broadcasts CPM messages. The Ego-vehicle’s ADS receives the message and according to it and its own detection adapts speed.
NOTE: assuming ADS manages perception, V2X, comprehension, and decision making

// Elements of the scene
EgoVehicle1 isA ago:Vehicle
ADS1 isA rcao:Algorithm
RCA_Algo isA rcao:Algorithm
Fusion_Algo isA rcao:Algorithm
ADS1 hasPart RCA_Algo
ADS1 hasPart Fusion_Algo
LIDAR1 isA rcao:InformationSource
Fusion_Algo isA rcao:InformationSource // can be considered as a “virtual sensor”
RSU1 isA traffic:V2X_Unit
Camera1 isA rcao:InformationSource
RSU1 hasPart Camera1
EgoVehicle1 hasPart ADAS1
EgoVehicle1 hasPart LIDAR1
Pedestrian1 isA ago:Pedestrian
 
// Initial situation
DrivingAutonomously1 isA ago:Action
ADS1 runs DrivingAutonomously1
 
// Perception process of the ADS1
PerceptionProcess_ADS1 isA prov:Activity
LIDAR1 prov:isUsedBy PerceptionProcess_ADS1
ADS1 runs PerceptionProcess_ADS1
 
// Perception process of the RSU
PerceptionProcess_RSU1 isA prov:Activity
Camera1 prov:isUsedBy PerceptionProcess_RSU1
RSU1 runs PerceptionProcess_RSU1

// RSU broadcasts its perception in a message
BroadcastCPM1 isA prov:Activity
RSU1 runs BroadcastCPM1
CPM1 isA traffic:CPM_Message
CPM1 rcao:contains PA1_RSU1_of_P1
BroadcastCPM1 prov:generated CPM1
 
// Result of Perception Process of RSU
PA1_RSU1_of_P1 isA rcao:PerceptualAwareness
PA1_RSU1_of_P1 rcao:hasSubject RSU1
PA1_RSU1_of_P1 rcao:hasObject Pedestrian1
PA1_RSU1_of_P1 rcao:hasSource Camera1
PA1_RSU1_of_P1.timestamp = …
PA1_RSU1_of_P1.confidence = …
Camera1_Score isA rcao:ResilienceAttribute
Camera1 rcao:hasResilienceAttribute Camera1_Score
 
// Result of Perception Process of ADS
PA1_ADS1_of_P1 isA rcao:PerceptualAwareness
PA1_ADS1_of_P1 rcao:hasSubject ADS1
PA1_ADS1_of_P1 rcao:hasObject Pedestrian1
PA1_ADS1_of_P1 rcao:hasSource LIDAR1
PA1_ADS1_of_P1.timestamp = …
PA1_ADS1_of_P1.confidence = …
LIDAR1_Visibility_Map isA rcao:ResilienceAttribute
LIDAR1 rcao:hasResilienceAttribute LIDAR1_Visibility_Map
 
// Step 1: The Fusion_Algo creates an intermediate perception.
FusionProcess1 isA prov:Activity
Fusion_Algo runs FusionProcess1
PA_Fused_of_P1 isA rcao:PerceptualAwareness
PA_Fused_of_P1 rcao:hasSubject ADS1
PA_Fused_of_P1 rcao:hasObject Pedestrian1
PA_Fused_of_P1 rcao:hasSource Fusion_Algo // Its source is the algorithm itself
PA_Fused_of_P1 rcao:derivedFrom PA1_RSU1_of_P1
PA_Fused_of_P1 rcao:derivedFrom PA1_ADS1_of_P1

// Step 2: The RCA_Algo uses this intermediate perception for final comprehension.
CA1_ADS1_of_RCP1 isA rcao:ComprehensionAwareness
CA1_ADS1_of_RCP1 rcao:hasSubject RCA_Algo // The RCA_Algo is the final subject
CA1_ADS1_of_RCP1 rcao:derivedFrom PA_Fused_of_P1

// Comprehension Process (which reads the received perception and the ADS perception) is a Fusion process
FusionProcess1 isA prov:Activity // could be a subclass of ComprehensionProcess

PA1_ADS1_of_P1 prov:isUsedBy FusionProcess1 // The process explicitly uses the two sources of information
CPM1 prov:isUsedBy FusionProcess1
ADS1 runs ComprehensionProcess1
Risk_Collision_Pedestrian1 isA ago:Context
 
CA1_ADS1_of_RCP1 isA rcao:ComprehensionAwareness
CA1_ADS1_of_RCP1 rcao:hasSubject ADS1
CA1_ADS1_of_RCP1 rcao:hasObject Risk_Collision_Pedestrian1
CA1_ADS1_of_RCP1 rcao:derivedFrom PA1_RSU1_of_P1 // useful for data lineage
CA1_ADS1_of_RCP1 rcao:derivedFrom PA1_ADS1_of_P1

// Adding a property to formally state the fusion strategy
CA1_ADS1_of_RCP1.fusion_strategy = “agreement_consolidation”
CA1_ADS1_of_RCP1.description = “Camera in RSU ahead detected a pedestrian; the confidence of the perception process is good. LIDAR1 also detected a pedestrian, and its visibility map is good. Both perceptions agree, so the presence of the Pedestrian is consolidated. According to the vehicle movement, there is risk of collision.”
 
// Decision making
// … omitting internal ADS1 rules or reasoning to make decision
AdaptSpeed1 isA ago:Action
ADS1 runs AdaptSpeed1
AdaptSpeed1 rcao:justifiedBy CA1_ADS1_of_RCP1


### Adjustment from last version rcao-v0.2.ttl

- rcao-v0.2.ttl lacks formal semantics to express ´virtual sensors´ (algorithms acting as source), fusion process activity and consolidation strategies.
- rcao-v0.2.ttl lacks a class to semantically express the ingestion of multiple StateAwareness
- rcao-v0.2.ttl do not contain any specific property for algorithm metadata to document this data property


### Turtle Instance
```ttl
@prefix : <http://www.example.org/rcao-inst#> .
@prefix owl: <http://www.w3.org/2002/07/owl#> .
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix xml: <http://www.w3.org/XML/1998/namespace> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix ago: <http://vcd.vicomtech.org/ontology/automotive#> .
@prefix prov: <http://www.w3.org/ns/prov#> .
@prefix rcao: <http://www.example.org/rcao#> .
@base <http://www.example.org/rcao-inst> .

#################################################################
#    1. AGENTS AND INFRASTRUCTURE
#################################################################

### The Ego Vehicle
:EgoVehicle1 rdf:type ago:Vehicle ;
    ago:hasPart :ADS1, :LIDAR1 .

### The Automated Driving System (Composite Agent)
:ADS1 rdf:type rcao:Algorithm ;
    ago:hasPart :RCA_Algo, :Fusion_Algo .

### The Sub-Algorithms (Agents)
:RCA_Algo rdf:type rcao:Algorithm .

### The Fusion Algorithm as a Virtual Sensor [1 - amendment]
# Amendment: Dual inheritance allows it to be an Agent (runs process) and Source (provenance)
:Fusion_Algo rdf:type rcao:VirtualSensorAlgorithm ;
    rdfs:label "Bayesian Fusion Module v2.1" .

### The Road Side Unit (Infrastructure Agent)
:RSU1 rdf:type rcao:RoadSideUnit ;
    ago:hasPart :Camera1 .

### The Target Entity
:Pedestrian1 rdf:type ago:Pedestrian .

#################################################################
#    2. SENSORS AND RESILIENCE ATTRIBUTES
#################################################################

### Local Sensor
:LIDAR1 rdf:type rcao:InformationSource ;
    rcao:hasSourceResilience :LIDAR1_Visibility_Map .

:LIDAR1_Visibility_Map rdf:type rcao:Completeness ;
    rdfs:label "Ego-Lidar Occlusion Map" .

### Remote Sensor
:Camera1 rdf:type rcao:InformationSource ;
    rcao:hasSourceResilience :Camera1_Score .

:Camera1_Score rdf:type rcao:ResilienceAttribute ;
    rcao:hasTrustScore "0.85"^^xsd:float .

#################################################################
#    3. PERCEPTION PHASE (Parallel Execution)
#################################################################

### --- RSU Perception (Remote) ---
:PerceptionProcess_RSU1 rdf:type prov:Activity ;
    prov:used :Camera1 ;
    rcao:runsProcess :RSU1 . # RSU runs the perception

:PA1_RSU1_of_P1 rdf:type rcao:PerceptualAwareness ;
    rcao:hasSubject :RSU1 ;
    rcao:hasObject :Pedestrian1 ;
    rcao:hasSource :Camera1 ;
    rcao:hasConfidence "0.88"^^xsd:float ;
    prov:wasGeneratedBy :PerceptionProcess_RSU1 .

### --- V2X Transmission ---
:BroadcastCPM1 rdf:type rcao:BroadcastActivity ;
    rcao:runsProcess :RSU1 ;
    prov:used :PA1_RSU1_of_P1 .

:CPM1 rdf:type rcao:V2XMessage ;
    prov:wasGeneratedBy :BroadcastCPM1 ;
    rcao:encapsulates :PA1_RSU1_of_P1 .

### --- ADS Perception (Local) ---
:PerceptionProcess_ADS1 rdf:type rcao:PerceptionProcess ;
    prov:used :LIDAR1 ;
    rcao:runsProcess :ADS1 .

:PA1_ADS1_of_P1 rdf:type rcao:PerceptualAwareness ;
    rcao:hasSubject :ADS1 ;
    rcao:hasObject :Pedestrian1 ;
    rcao:hasSource :LIDAR1 ;
    rcao:hasConfidence "0.92"^^xsd:float ;
    prov:wasGeneratedBy :PerceptionProcess_ADS1 .

#################################################################
#    4. FUSION PHASE (The Core of Scenario 3)
#################################################################

### Fusion Process
# Amendment: Explicit FusionProcess class
:FusionProcess1 rdf:type rcao:FusionProcess ;
    rcao:runsProcess :Fusion_Algo ;
    # Consumes the Message and the Local State
    prov:used :CPM1 ;
    rcao:fusesState :PA1_ADS1_of_P1 .

### Intermediate Fused Perception
:PA_Fused_of_P1 rdf:type rcao:PerceptualAwareness ;
    rcao:hasSubject :ADS1 ;
    rcao:hasObject :Pedestrian1 ;
    # CRITICAL: The Source is now the Virtual Sensor (Algorithm)
    rcao:hasSource :Fusion_Algo ; 
    # PROVENANCE: Traceability to both roots
    rcao:derivedFrom :PA1_RSU1_of_P1 ; 
    rcao:derivedFrom :PA1_ADS1_of_P1 ;
    rcao:hasConfidence "0.98"^^xsd:float ; # Increased confidence due to fusion
    prov:wasGeneratedBy :FusionProcess1 .

#################################################################
#    5. COMPREHENSION AND DECISION
#################################################################

### Context Definition
:Risk_Collision_Pedestrian1 rdf:type ago:Context .

### Final Comprehension State
:CA1_ADS1_of_RCP1 rdf:type rcao:ComprehensionAwareness ;
    rcao:hasSubject :RCA_Algo ; # Granular subject assignment
    rcao:hasObject :Risk_Collision_Pedestrian1 ;
    # Lineage traces back to the Fused State
    rcao:derivedFrom :PA_Fused_of_P1 ;
    # Amendment: Fusion Strategy Metadata
    rcao:hasFusionStrategy "agreement_consolidation"^^xsd:string ;
    prov:wasGeneratedBy :FusionProcess1 . # Or a specific ComprehensionProcess

### Justified Action
:AdaptSpeed1 rdf:type ago:Action ;
    rcao:runsProcess :ADS1 ;
    rcao:justifiedBy :CA1_ADS1_of_RCP1 .
```ttl


