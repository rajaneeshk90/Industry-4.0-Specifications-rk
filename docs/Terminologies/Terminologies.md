Terminologies
Name
Meaning
Type
Examples
3D Model
A digital file that represents a three-dimensional object's geometry and structure, which can be used for 3D printing.
String
STL, OBJ, AMF, or 3MF
Object Description
Detailed description of the 3D printed object
String
"Custom phone case design"
Material Type
Type of material to be used for 3D printing
String
PLA, ABS, PETG, TPU, nylon, resin, metal, wood-filled, Silicon and Carbon Fiber
Material Properties
Specific properties or requirements for the material
String
"High strength", "Translucent", "Black"
Brand Preference
Preference for a specific material brand (if any)
String
"MakerBot", "Hatchbox"
Layer Height
Desired layer height or resolution
Double
"Fine (0.1mm)", "Medium (0.2mm)", "Coarse (0.3mm)"
Infill Density
Percentage of internal structure's fill density in percentage.
Double
20, 50, 80,100
Infill Pattern
Preferred infill pattern (e.g., grid, honeycomb, triangular)
String
"Grid", "Honeycomb", "Triangular"
Wall Thickness
Number of perimeter shells and thickness in millimetres
Object
{"Shells": 2, "Thickness": 1.5}
Print Orientation
Preferred orientation of the object for printing
String
"Upright", "Flat", "45-degree angle"
Support Structures
Indicates whether support structures are needed
Boolean
true, false
Support Density
Density of support structures if required
Double
10, 30, 50
Support Type
Support type (e.g., tree, linear), density, and overhang angle
Object
{"Type": "Tree", "Density": 30, "OverhangAngle": 45}
Print Quantity
Number of identical objects to be printed
Integer
1, 10, 100
Print Speed
Printing speed settings for various aspects
Object
{"General": 60, "FirstLayer": 20, "OuterShell": 40}
Object Dimensions
Exact dimensions (height, width, depth) of the object
Object
{"Height": 50, "Width": 30, "Depth": 5}
Tolerance
Allowable variation in object's dimensions
Double
0.1, 0.5, 1.0 mm.
"Snug fit for parts", "0.2mm clearance"
Surface Finish
Desired surface finish of the 3D printed object
String
"Smooth", "Textured", "Glossy"
Colour and Finish
Colour requirements and post-processing finishes
String
"Red glossy finish", "Matte black"
Filament/Resin Diameter
Diameter of the filament or resin used
Double
1.75mm, 2.85mm
Temperature Settings
Recommended nozzle temperature, bed temperature, and chamber temperature
Object
{"Nozzle": 210, "Bed": 60, "Chamber": 40}
Quality Standards
Standards or certifications the part must meet
String
"ISO 9001:2015", "ASTM D638-14"
Delivery Date
Deadline for the delivery of the printed objects
Date
{"DeliveryDate": "2024-02-28"
Packaging Requirements
Special packaging or handling instructions
String
"Packaging": "Bubble wrap", "Handle with care", "Secure packaging"
Budget
Budget constraints for the 3D printing project
Double
100.00, 500.00, 1000.00
Project Specifics
Specific details about the project and its purpose
String
"Prototype for automotive component", "Functional art"
Post Processing
Post-processing requirements (e.g., sanding, painting)
String
"Sanded edges", "UV-cured coating"
Special Features
Unique features or requirements for the object
String
"Articulated joints", "Textured surface" "Print with high infill" "Emboss logo on surface"
Print Speed
Printing speed settings for various aspects
Object
{"General": 60, "FirstLayer": 20, "OuterShell": 40}
Additional Services
Request additional services (e.g., 3D scanning, modeling, testing)
String
"3D scanning required", "Material strengt




Discovery/Services Workflow for Manufacturing Services

Overview
The process of connecting Manufacturing Services Seekers (MSS) with Manufacturing Services Providers (MSP) follows a structured series of interactions within an app. These services can encompass a wide range of manufacturing capabilities, such as assembly, 3D printing, packaging, and more. The standard workflow for a manufacturing service transaction can be divided into the following stages:

Discovery of Manufacturing Services
Browsing through various Manufacturing Service Provider
Selecting MSPs based on needs.
Paying the MSP for services.
Fulfilment of the Manufacturing Services.
Post-fulfilment of Manufacturing Services

Architecture


Discovery of Manufacturing Services
The workflow for discovering manufacturing services is as follows:

MSS Discovers an MSP
To initiate the process, the MSS explores various Manufacturing Services Providers through an app. MSS may search for an MSP by name, specialty, ratings, or the specific manufacturing services they require.

MSS Browses Through Various MSP Services
Once potential MSPs are identified, the MSS delves into the catalogue of manufacturing services offered by these providers. During this interaction, the MSP presents their catalogue, which may include a range of manufacturing services and categories.

MSS Views a Particular Manufacturing Service of an MSP
The MSS further explores the details of a specific manufacturing service within the catalogue of the chosen MSP.

Choosing a Manufacturing Service
This stage involves the MSS selecting a manufacturing service. Several sub-interactions occur during this phase:
MSS Selects a Service
The MSS requests an offer from the MSP, often starting by providing necessary information through the app. This information may include manufacturing specifications and details. In some cases, the MSP may request the MSS to share manufacturing records, such as CAD files or materials specifications, which are securely accessed through consent processes.

MSP Processes the Manufacturing Information and Returns an Offer
Upon receiving the manufacturing information, the MSP processes it and returns an initial offer, which serves as an indication of the service feasibility. This offer may not represent the final terms of the manufacturing service.

MSS Accepts the Offer
The MSS accepts the offer, and if required, additional information may be requested by the MSP for finalizing the services. This information can be submitted through app.

Generates the Final Agreement
The MSP generates the final agreement, including detailed specifications, pricing, quality specifications and terms of the manufacturing service.

MSS Agrees and pays.
The MSS pays for the services, confirming the order and demonstrating their commitment to the manufacturing service.

MSP Confirms the order.
The MSP confirms the finalized services and updates the MSS with the latest status of the manufacturing service request.

Fulfilment of the Manufacturing Service

During this stage, the MSP initiates the fulfilment of the manufacturing service, such as assembly, 3D printing, or packaging. Several interactions can occur in this phase:

Status Updates
The MSS may request status updates on the manufacturing process, to which the MSP responds with information such as "in progress," "completed," or other relevant updates. The MSP may also independently send status updates.

Terms Update
The MSS can request updates to the manufacturing terms, such as changes in materials, quantities, or delivery timelines. The MSP may also independently update the terms based on new information or changes.

Cancellation of Service
Both the MSS and MSP have the option to request the cancellation of an ongoing manufacturing service, depending on factors like changes in requirements, or quality issues.

Real-time Tracking
Although less common in manufacturing services, the MSP may provide real-time tracking information for specific scenarios where the tracking of materials or products is essential for quality assurance.

Post-fulfillment of Manufacturing Services

In this stage, various post-fulfillment activities occur:
Rating a Service
The MSS typically provides feedback on the quality and performance of the manufacturing service. Ratings help evaluate and improve services and build trust between MSS and MSP.

Contacting Support
If necessary, the MSS may need to contact the MSP's support centre for inquiries, assistance, or issue resolution. The MSP shares contact information, such as phone numbers, email addresses, or chat URLs.
