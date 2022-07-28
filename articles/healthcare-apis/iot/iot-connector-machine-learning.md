---
title: MedTech service and Azure Machine Learning Service - Azure Health Data Services
description: In this article, you'll learn how to use the MedTech service and the Azure Machine Learning Service
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 03/25/2022
ms.author: jasteppe
---

# MedTech service and Azure Machine Learning Service

In this article, we'll explore using the MedTech service and Azure Machine Learning Service.

## MedTech service and Azure Machine Learning Service reference architecture

MedTech service enables IoT devices seamless integration with Fast Healthcare Interoperability Resources (FHIR&#174;) services. This reference architecture is designed to accelerate adoption of Internet of Medical Things (IoMT) projects. This solution uses Azure Databricks for the Machine Learning (ML) compute. However, Azure ML Services with Kubernetes or a partner ML solution could fit into the Machine Learning Scoring Environment.

The four line colors show the different parts of the data journey.

- **Blue** = IoT data to FHIR service.
- **Green** = data path for scoring IoT data
- **Red** = Hot path for data to inform clinicians of patient risk. The goal of the hot path is to be as close to real-time as possible.
- **Orange** = Warm path for data. Still supporting clinicians in patient care. Data requests are typically triggered manually or on a refresh schedule.

:::image type="content" source="media/iot-concepts/iot-connector-machine-learning.png" alt-text="Screenshot of the MedTech service and Machine Learning Service reference architecture." lightbox="media/iot-concepts/iot-connector-machine-learning.png":::

**Data ingest – Steps 1 through 5**

1. Data from IoT device or via device gateway sent to Azure IoT Hub/Azure IoT Edge.
2. Data from Azure IoT Edge sent to Azure IoT Hub.
3. Copy of raw IoT device data sent to a secure storage environment for device administration.
4. PHI IoMT payload moves from Azure IoT Hub to the MedTech service. Multiple Azure services are represented by 1 MedTech service icon.
5. Three parts to number 5: 
 a. MedTech service request Patient resource from FHIR service. 
 b. FHIR service sends Patient resource back to the MedTech service. 
 c. IoT Patient Observation is record in FHIR service.

**Machine Learning and AI Data Route – Steps 6 through 11**

6. Normalized ungrouped data stream sent to Azure Function (ML Input).
7. Azure Function (ML Input) requests Patient resource to merge with IoMT payload.
8. IoMT payload with PHI is sent to an event hub for distribution to Machine Learning compute and storage.
9. PHI IoMT payload is sent to Azure Data Lake Storage Gen 2 for scoring observation over longer time windows.
10. PHI IoMT payload is sent to Azure Databricks for windowing, data fitting, and data scoring.
11. The Azure Databricks requests more patient data from data lake as needed. a. Azure Databricks also sends a copy of the scored data to the data lake.

**Notification and Care Coordination – Steps 12 - 18**

**Hot path**

12. Azure Databricks sends a payload to an Azure Function (ML Output).
13. RiskAssessment and/or Flag resource submitted to FHIR service. a. For each observation window, a RiskAssessment resource will be submitted to the FHIR service. b. For observation windows where the risk assessment is outside the acceptable range a Flag resource should also be submitted to the FHIR service.
14. Scored data sent to data repository for routing to appropriate care team. Azure SQL Server is the data repository used in this design because of its native interaction with Power BI.
15. Power BI Dashboard is updated with Risk Assessment output in under 15 minutes.

**Warm path**

16. Power BI refreshes dashboard on data refresh schedule. Typically, longer than 15 minutes between refreshes.
17. Populate Care Team app with current data.
18. Care Coordination through Microsoft Teams for Healthcare Patient App.

## Next steps

In this article, you've learned about the MedTech service and Machine Learning service integration. For an overview of the MedTech service, see

>[!div class="nextstepaction"]
>[MedTech service overview](iot-connector-overview.md)

(FHIR&#174;) is a registered trademark of [HL7](https://hl7.org/fhir/) and is used with the permission of HL7.
