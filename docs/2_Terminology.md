
# Terminology

Below is a glossary of terms frequently used in the Industry4.0 ecosystem. These terms have been chosen specifically around four specific activities that are performed during discovery namely

## Glossary of the tersm used

| Name                    | Meaning                                                                                                                | Type    | Examples                                                                                   |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------- | ------- | ------------------------------------------------------------------------------------------ |
| 3D Model                | A digital file that represents a three-dimensional object's geometry and structure, which can be used for 3D printing. | String  | STL, OBJ, AMF, or 3MF                                                                      |
| Object Description      | Detailed description of the 3D printed object                                                                          | String  | "Custom phone case design"                                                                 |
| Material Type           | Type of material to be used for 3D printing                                                                            | String  | PLA, ABS, PETG, TPU, nylon, resin, metal, wood-filled, Silicon and Carbon Fiber            |
| Material Properties     | Specific properties or requirements for the material                                                                   | String  | "High strength", "Translucent", "Black"                                                    |
| Brand Preference        | Preference for a specific material brand (if any)                                                                      | String  | "MakerBot", "Hatchbox"                                                                     |
| Layer Height            | Desired layer height or resolution                                                                                     | Double  | "Fine (0.1mm)", "Medium (0.2mm)", "Coarse (0.3mm)"                                         |
| Infill Density          | Percentage of internal structure's fill density in percentage.                                                         | Double  | 20, 50, 80,100                                                                             |
| Infill Pattern          | Preferred infill pattern (e.g., grid, honeycomb, triangular)                                                           | String  | "Grid", "Honeycomb", "Triangular"                                                          |
| Wall Thickness          | Number of perimeter shells and thickness in millimetres                                                                | Object  | {"Shells": 2, "Thickness": 1.5}                                                            |
| Print Orientation       | Preferred orientation of the object for printing                                                                       | String  | "Upright", "Flat", "45-degree angle"                                                       |
| Support Structures      | Indicates whether support structures are needed                                                                        | Boolean | true, false                                                                                |
| Support Density         | Density of support structures if required                                                                              | Double  | 10, 30, 50                                                                                 |
| Support Type            | Support type (e.g., tree, linear), density, and overhang angle                                                         | Object  | {"Type": "Tree", "Density": 30, "OverhangAngle": 45}                                       |
| Print Quantity          | Number of identical objects to be printed                                                                              | Integer | 1, 10, 100                                                                                 |
| Print Speed             | Printing speed settings for various aspects                                                                            | Object  | {"General": 60, "FirstLayer": 20, "OuterShell": 40}                                        |
| Object Dimensions       | Exact dimensions (height, width, depth) of the object                                                                  | Object  | {"Height": 50, "Width": 30, "Depth": 5}                                                    |
| Tolerance               | Allowable variation in object's dimensions                                                                             | Double  | 0.1, 0.5, 1.0 mm. | "Snug fit for parts", "0.2mm clearance"                                 |
| Surface Finish          | Desired surface finish of the 3D printed object                                                                        | String  | "Smooth", "Textured", "Glossy"                                                             |
| Colour and Finish       | Colour requirements and post-processing finishes                                                                       | String  | "Red glossy finish", "Matte black"                                                         |
| Filament/Resin Diameter | Diameter of the filament or resin used                                                                                 | Double  | 1.75mm, 2.85mm                                                                             |
| Temperature Settings    | Recommended nozzle temperature, bed temperature, and chamber temperature                                               | Object  | {"Nozzle": 210, "Bed": 60, "Chamber": 40}                                                  |
| Quality Standards       | Standards or certifications the part must meet                                                                         | String  | "ISO 9001:2015", "ASTM D638-14"                                                            |
| Delivery Date           | Deadline for the delivery of the printed objects                                                                       | Date    | {"DeliveryDate": "2024-02-28"                                                              |
| Packaging Requirements  | Special packaging or handling instructions                                                                             | String  | "Packaging": "Bubble wrap", "Handle with care", "Secure packaging"                         |
| Budget                  | Budget constraints for the 3D printing project                                                                         | Double  | 100.00, 500.00, 1000.00                                                                    |
| Project Specifics       | Specific details about the project and its purpose                                                                     | String  | "Prototype for automotive component", "Functional art"                                     |
| Post Processing         | Post-processing requirements (e.g., sanding, painting)                                                                 | String  | "Sanded edges", "UV-cured coating"                                                         |
| Special Features        | Unique features or requirements for the object                                                                         | String  | "Articulated joints", "Textured surface" "Print with high infill" "Emboss logo on surface" |
| Print Speed             | Printing speed settings for various aspects                                                                            | Object  | {"General": 60, "FirstLayer": 20, "OuterShell": 40}                                        |
| Additional Services     | Request additional services (e.g., 3D scanning, modeling, testing)                                                     | String  | "3D scanning required", "Material strengt                                                  |