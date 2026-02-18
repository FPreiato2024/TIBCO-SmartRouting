# TIBCO-SmartRouting
Provide a generic, flexible REST API for transforming and dispatching JSON payloads between systems.

## Overview

**SmartRouting** is a generic, configuration-driven integration service that accelerates data exchange between systems via HTTP/HTTPS.
It exposes a REST API that accepts JSON payloads and, via configuration tables, routes, transforms, and delivers them to the appropriate target systems.
The solution decouples source and target systems through a **Common Data Model**, reducing integration complexity and improving scalability.

## Features

- Generic REST API for all integrations  
- Dynamic routing based on configuration  
- JSON-to-JSON transformation using JOLT  
- Support for multiple target systems  
- Synchronous and asynchronous processing  
- Scalable and reusable architecture  


##  How It Works

1. **Receive Request**
   - JSON payload  
   - Parameters: `sourceSystem`, `documentType`  

2. **Validation**
   - Verify if the source system is allowed to send the document type  

3. **Routing**
   - Retrieve target systems from configuration  

4. **Transformation**
   - Source JSON → Common Data Model JSON
   - Common Data Model JSON → Target JSON  

5. **Dispatch**
   - Send to target systems via HTTP/HTTPS  


## Configuration

Routing and transformations are fully configuration-driven.
Database tables can be implemented with the sql statementes provided with the source code.

**Adding a new integration requires configuration only, not code changes.**

### SourceSystem table

The source system table stores the Jolt transformation to be applied to the source JSON payload, for each pair of document type and source system.

| Parameter        | Description                                      |
|-----------------|--------------------------------------------------|
| `DocumentType`  | Type of documement sent by the source system       |
| `SystemName` | Name of the system that is sending the document                  |
| `JoltSpecification` | JSON transformation to be applied after receiving the payload                   |
| `Enabled` | Flag to enable or disable a certain pair of document type-source system                   |

### Documents table

This table contains all the target systems by document type.

| Parameter        | Description                                      |
|-----------------|--------------------------------------------------|
| `DocumentType`  | Type of documement sent by the source system       |
| `SystemName` | Name of the system to which the document must be sent                   |
| `Endpoint` | Endpoint of the system to which the document must be sent                   |
| `JoltSpecification` | JSON transformation to be applied before sending the payload                   |
| `Timeout` | Timeout in seconds while contacting the destination service                   |
| `Retry` | Number of retry in case of issue before throwing an error                   |
| `Enabled` | Flag to enable or disable the data sending                   |


### SystemInfo table

The SystemInfo table stores connection parameter of the target system that needs to be reached.
It is related to the Documents table, since each SystemName in the Documents table must have its connection parameter in this table.

| Parameter        | Description                                      |
|-----------------|--------------------------------------------------|
| `SystemName` | Name of the system that is sending the document                  |
| `Protocol` | Protocol of the target service (HTTP/HTTPS)                   |
| `Hostname` | Hostname of the target service                   |
| `Port` | Port of the target service                   |
| `Username` | Credentials of the target service                   |
| `Password` | Credentials of the target service                   |
| `Timeout` | Timeout in seconds while contacting the destination service. This will be applied to all the endpoints for this system. IT can be overwrite using the "Timeout" column of the Documents table.                   |
| `Retry` | Number of retry in case of issue before throwing an error. This will be applied to all the endpoints for this system. IT can be overwrite using the "Timeout" column of the Documents table.                    |


## Optional Query Parameters

| Parameter        | Description                                      |
|-----------------|--------------------------------------------------|
| `targetSystem`  | Send data to a specific target system only       |
| `fireAndForget` | Enable asynchronous processing                   |

### Details

- **targetSystem**
  - Useful for retries or resubmissions  

- **fireAndForget**
  - Immediate acknowledgement returned  
  - Dispatch handled via JMS queue  


## Use Cases

- Multi-system data distribution  
- API-based integrations  
- Partner and external system connectivity  
- Data normalization between heterogeneous systems  
- Event-driven architectures  


## Benefits

- Reduces point-to-point integrations  
- Improves maintainability  
- Accelerates onboarding of new systems  
- Decouples systems via Common Data Model  
- Supports scalable and secure integration patterns  

---

## Usage

1. For the **developer** - Configure the database tables based on the need.

2. Call the **SmartRouting REST API**  

3. Send as input:
   - JSON payload  
   - `sourceSystem`  
   - `documentType`  

4. Optionally:
   - `targetSystem`  
   - `fireAndForget`  
