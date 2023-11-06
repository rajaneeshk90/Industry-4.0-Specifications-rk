# Discovery/Services Workflow for Manufacturing Services

## Overview
The process of connecting Manufacturing Services Seekers (MSS) with Manufacturing Services Providers (MSP) follows a structured series of interactions within an app. These services can encompass a wide range of manufacturing capabilities, such as assembly, 3D printing, packaging, and more. 

The standard workflow for a manufacturing service transaction can be divided into the following stages:

- Discovery of Manufacturing Services
- Browsing through various Manufacturing Service Provider
- Selecting MSPs based on needs.
- Paying the MSP for services.
- Fulfilment of the Manufacturing Services.
- Post-fulfilment of Manufacturing Services

## Architecture


### Discovery of Manufacturing Services
The workflow for discovering manufacturing services is as follows:

1. #### MSS Discovers an MSP
	 To initiate the process, the MSS explores various Manufacturing Services Providers through an app. MSS may search for an MSP by name, specialty, ratings, or the specific manufacturing services they require.
1. #### MSS Browses Through Various MSP Services
	 Once potential MSPs are identified, the MSS delves into the catalogue of manufacturing services offered by these providers. During this interaction, the MSP presents their catalogue, which may include a range of manufacturing services and categories.
1. #### MSS Views a Particular Manufacturing Service of an MSP
	 The MSS further explores the details of a specific manufacturing service within the catalogue of the chosen MSP.
1. #### Choosing a Manufacturing Service
	 This stage involves the MSS selecting a manufacturing service. Several sub-interactions occur during this phase:
	 - ##### MSS Selects a Service:
	  	The MSS requests an offer from the MSP, often starting by providing necessary information through the app. This information may include manufacturing specifications and details. In some cases, the MSP may request the MSS to share manufacturing records, such as CAD files or materials specifications, which are securely accessed through consent processes.
	 - ##### MSP Processes the Manufacturing Information and Returns an Offer
	 	 Upon receiving the manufacturing information, the MSP processes it and returns an initial offer, which serves as an indication of the service feasibility. This offer may not represent the final terms of the manufacturing service.
	 - ##### MSS Accepts the Offer
	 	 The MSS accepts the offer, and if required, additional information may be requested by the MSP for finalizing the services. This information can be submitted through app.
	 - ##### Generates the Final Agreement
	 	 The MSP generates the final agreement, including detailed specifications, pricing, quality specifications and terms of the manufacturing service.
	 - ##### MSS Agrees and pays.
	 	 The MSS pays for the services, confirming the order and demonstrating their commitment to the manufacturing service.
	 - ##### MSP Confirms the order.
	 	 The MSP confirms the finalized services and updates the MSS with the latest status of the manufacturing service request.
1. #### Fulfilment of the Manufacturing Service
	 During this stage, the MSP initiates the fulfilment of the manufacturing service, such as assembly, 3D printing, or packaging. Several interactions can occur in this phase:
1. #### Status Updates
	 The MSS may request status updates on the manufacturing process, to which the MSP responds with information such as "in progress," "completed," or other relevant updates. The MSP may also independently send status updates.
1. #### Terms Update
	 The MSS can request updates to the manufacturing terms, such as changes in materials, quantities, or delivery timelines. The MSP may also independently update the terms based on new information or changes.
1. #### Cancellation of Service
	 Both the MSS and MSP have the option to request the cancellation of an ongoing manufacturing service, depending on factors like changes in requirements, or quality issues.
1. #### Real-time Tracking
	 Although less common in manufacturing services, the MSP may provide real-time tracking information for specific scenarios where the tracking of materials or products is essential for quality assurance.
1. #### Post-fulfillment of Manufacturing Services
	 In this stage, various post-fulfillment activities occur:
1. #### Rating a Service
	 The MSS typically provides feedback on the quality and performance of the manufacturing service. Ratings help evaluate and improve services and build trust between MSS and MSP.
1. #### Contacting Support
	 If necessary, the MSS may need to contact the MSP's support centre for inquiries, assistance, or issue resolution. The MSP shares contact information, such as phone numbers, email addresses, or chat URLs.
