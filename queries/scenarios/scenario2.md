# Scenario 2 (comprehension by shared perception)
## Description
Short: simple cooperative awareness (comprehension by shared perception)
Expanded: An ego-vehicle, equipped with an ADS, is driving forward. A pedestrian is ahead. A RSU Unit detects the pedestrian with a Camera. The RSU Unit broadcasts CPM messages. The Ego-vehicle’s ADS receives the message and adapts speed.
NOTE: assuming ADS manages perception, V2X, comprehension, and decision making


## RCAO Instances:

// Elements of the scene
EgoVehicle1 isA ago:Vehicle
ADS1 isA rcao:Algorithm
RCA_Algo isA rcao:Algorithm
ADS1 hasPart RCA_Algo
RSU1 isA traffic:V2X_Unit
Camera1 isA rcao:InformationSource
RSU1 hasPart Camera1
EgoVehicle1 hasPart ADS1
EgoVehicle1 hasPart LIDAR1
Pedestrian1 isA ago:Pedestrian
 
// Initial situation
DrivingAutonomously1 isA ago:Action
ADS1 runs DrivingAutonomously1
 
// Perception process of the RSU
PerceptionProcess1 isA prov:Activity
Camera1 prov:isUsedBy PerceptionProcess1
RSU1 runs PerceptionProcess1

// RSU broadcasts its perception 
BroadcastCPM1 isA prov:Activity 
RSU1 runs BroadcastCPM1 
CPM1 isA traffic:CPM_Message 
CPM1 rcao:contains PA1_RSU1_of_P1 
BroadcastCPM1 prov:generated CPM1
 
// Result of Perception Process
PA1_RSU1_of_P1 isA rcao:PerceptualAwareness
PA1_RSU1_of_P1 rcao:hasSubject RSU1
PA1_RSU1_of_P1 rcao:hasObject Pedestrian1
PA1_RSU1_of_P1 rcao:hasSource Camera1
PA1_RSU1_of_P1.timestamp = …
PA1_RSU1_of_P1.confidence = …
Camera1_Score isA rcao:ResilienceAttribute
Camera1 rcao:hasResilienceAttribute Camera1_Score
 
// Comprehension Process (which reads the received perception)
ComprehensionProcess1 isA prov:Activity
CPM1 prov:isUsedBy ComprehensionProcess1 // The process now explicitly uses the message
ADS1 runs ComprehensionProcess1
Risk_Collision_Pedestrian1 isA ago:Context
 
CA1_ADS1_of_RCP1 isA rcao:ComprehensionAwareness
CA1_ADS1_of_RCP1 rcao:hasSubject RCA_Algo
CA1_ADS1_of_RCP1 rcao:hasObject Risk_Collision_Pedestrian1
CA1_ADS1_of_RCP1 rcao:derivedFrom PA1_RSU1_of_P1
CA1_ADS1_of_RCP1.description = “Camera in RSU ahead detected a pedestrian; the confidence of the perception process is good. According to the vehicle movement, there is risk of collision.”
 
// Decision making
// … omitting internal ADS1 rules or reasoning to make decision
AdaptSpeed1 isA ago:Action
ADS1 runs AdaptSpeed1
AdaptSpeed1 rcao:justifiedBy CA1_ADS1_of_RCP1

### Adjustment rcao-v0.1.ttl

- Scenario 2 needs a RSU isA traffic:V2_Unit which is currently missing in previous rcao version. The ontology needs a class for infrastructure **agents** that act as subject of perception (*infrastructure -> infrastructureElement*).
- Scenario 2 needs V2X messaging CPM1 isA traffic:CPM_Message. No class exists to represent the data package trasmitted between **agents**. It requires `prov:Entity`subclass for message to support the `prov:used` relation in the receiving process.
- Scenario 2 needs a property to encapsulate CPU message to abstract Awareness State it carries. It needs to model that the CPM CPM1 contains the State PA1_RSU1_of_P1.
- Scenario 2 requires to distinguish internal processing and external communication in queries. Generic `prov:Activity`exists, but no specific class for transmission

### Proposed amendments to rcao-v0.01.ttl 

- New Classes:
1. Infrastructure -> infrastructureElement
2. V2XMessage
3. BroadcastActivity
- New Properties:
1. rcao:encapsulates to link the V2XMessage to AwarenessState
