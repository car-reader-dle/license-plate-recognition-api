# CAR-READER-DLE — Local, Offline License Plate Recognition API
CAR-READER Deep Learning Edition

**Local ALPR/ANPR software for Windows, Linux and Raspberry Pi — designed for parking systems, access control, RTSP cameras, multi-lane installations and OEM integration.**

[Website](https://car-reader-dle.com/) · [Online Demo](https://car-reader-dle.com/demo) · [Contact](https://car-reader-dle.com/#contact)

---

## Overview

CAR-READER-DLE is a commercial, locally operated Automatic License Plate Recognition solution for integrating license plate detection and recognition into existing applications, camera systems and embedded products.

The software processes images and camera streams on the customer’s own hardware. No cloud connection is required for recognition.

CAR-READER-DLE is intended for:

* parking and car park management
* gates and barrier systems
* access control
* entrances and exits
* logistics and yard management
* vehicle registration workflows
* visitor and fleet management
* security and camera systems
* industrial automation
* software vendors and OEM products

The Runtime is accessed through a REST API and can be integrated from virtually any programming language capable of sending HTTP requests.

Supported integration environments include:

* Python
* C#
* JavaScript and TypeScript
* Java
* Delphi / Pascal
* C and C++
* PHP
* PowerShell
* low-code and automation platforms

---

## What makes CAR-READER-DLE different?

License plate recognition is not only about reading a sequence of characters.

A useful result must also reconstruct the human-readable and country-specific structure of the plate.

CAR-READER-DLE combines character recognition with structural analysis, regional prefix logic, separator reconstruction, multi-line processing and configurable country profiles.

### Regional prefix and separator reconstruction

OCR systems usually produce a continuous character sequence.

For example:

```text
MAB1234
```

A downstream parking or access-control application may instead require a structured result such as:

```text
M-AB1234
```

CAR-READER-DLE reconstructs relevant structural separators and blocks, including:

* city or municipality prefixes
* county and regional prefixes
* national prefix structures
* international plate blocks
* country-specific separator positions
* multi-line plate structures
* variable-length regional identifiers

This makes the output easier to display, validate, search and compare with customer databases.

The reconstruction is not limited to one fixed separator position. It can account for different prefix lengths and international plate layouts.

---

## Core features

### Offline and on-premises processing

Recognition runs locally on the customer’s system.

Benefits include:

* no mandatory cloud connection
* predictable integration architecture
* local control over images and recognition results
* suitability for privacy-sensitive installations
* operation in isolated networks
* no per-image cloud transmission requirement

### REST API

The Runtime provides an HTTP-based API for:

* single-image recognition
* batch processing
* multi-plate recognition
* RTSP camera connections
* live-frame analysis
* region-of-interest configuration
* camera and lane management
* Runtime health and capabilities
* diagnostics and integration checks

### Single-image recognition

Upload an image and receive a structured JSON response containing fields such as:

* recognized text
* formatted plate text
* confidence
* detected profile
* line structure
* separator positions
* processing information

### Multiple license plates in one image

CAR-READER-DLE can process images containing more than one license plate.

Typical applications include:

* parking areas
* overview cameras
* access zones
* loading and logistics areas
* vehicle queues
* entrance and exit overview images

Each detected plate can be returned as a separate result.

### RTSP camera integration

Network cameras can be connected through RTSP.

The Runtime supports workflows for:

* live preview
* camera registration
* camera-specific IDs
* multiple simultaneous cameras
* individual camera status
* reconnect and disconnect handling
* frame-based recognition
* event-driven analysis

### ROI — Region of Interest

A Region of Interest can restrict processing to the relevant part of the camera image.

Examples include:

* a single lane
* a gate area
* the space in front of a barrier
* a defined vehicle approach zone
* a parking entrance or exit

ROI processing can reduce unnecessary image analysis and improve the efficiency of live-camera installations.

### Triggered recognition

Full recognition does not need to run on every video frame.

Recognition can be triggered by:

* movement inside the ROI
* a vehicle entering a defined area
* preliminary license plate detection
* an external application
* a loop detector or sensor
* a gate or access-control event
* a manual API request

This event-oriented approach enables efficient live operation on standard CPU systems.

### Multi-camera and multi-lane processing

Multiple cameras and lanes can share one Runtime instance.

Each camera or lane can receive a stable identifier such as:

```text
lane-01
lane-02
entrance-north
exit-south
```

The Runtime uses bounded worker and queue management to process requests in parallel while preventing unlimited backlog growth.

For live-video workflows, additional frames from a lane that is already being processed can be discarded deliberately. This avoids filling the queue with outdated frames from the same camera.

### Country and recognition profiles

Profiles allow the integration to select the expected plate structure.

Available profiles may include:

* automatic profile selection
* international Latin-character plates
* German plates
* Austrian plates
* Swiss plates
* Dutch plates
* two-line plates

The precise set of profiles depends on the licensed product configuration.

### Multi-line license plates

CAR-READER-DLE can process plates whose characters are arranged across multiple lines.

The response can include:

* detected layout
* line count
* individual line texts
* reconstructed combined representation

### Annotation output

Recognized plates can optionally be returned as annotated images.

Annotations may include:

* plate frame
* recognized characters
* confidence display
* individual character boxes
* numbered markers for multi-plate results

### Batch processing

A directory of images located on the Runtime computer can be processed as a batch.

This is useful for:

* quality checks
* archive processing
* migration projects
* test datasets
* customer-specific acceptance testing

### Configurable performance

The Runtime supports configurable parallel inference workers, CPU thread limits and bounded queues.

This makes it possible to adapt the installation to:

* compact office computers
* industrial PCs
* high-performance workstations
* edge computers
* Raspberry Pi systems
* multi-camera servers

---

## Supported platforms

CAR-READER-DLE is available for selected configurations of:

* Windows x64
* Linux x64
* Raspberry Pi 5 ARM64

Availability and performance depend on the licensed package and target hardware.

---

## Typical architecture

```text
Camera, image or application
            |
            v
     CAR-READER-DLE Runtime
            |
            v
      Structured REST API
            |
            v
Parking, access-control, ERP,
security or OEM application
```

The customer application remains responsible for business logic such as:

* opening a barrier
* checking an allowlist
* assigning a parking session
* recording entry and exit times
* associating a plate with a visitor
* calculating parking fees
* creating alerts or reports

CAR-READER-DLE supplies the recognition result and related metadata.

---

## Quick API example

### Request

```bash
curl -X POST \
  "http://127.0.0.1:5099/api/single" \
  -F "image=@vehicle.jpg" \
  -F "plate_profile=auto" \
  -F "lane_id=entrance-01"
```

### Example response

```json
{
  "ok": true,
  "text": "MAB1234",
  "formatted_text": "M-AB1234",
  "confidence": 0.9931,
  "plate_layout": "single_line",
  "line_count": 1,
  "separator_positions": [1],
  "country_profile": "INTERNATIONAL_LATIN",
  "lane_id": "entrance-01"
}
```

`ok: true` indicates that the request was processed successfully. Applications should still check whether a plate was found and whether the returned result meets their project-specific confidence requirements.

---

## Multi-plate example

A multi-plate request can return a collection of recognized plates:

```json
{
  "ok": true,
  "multi_plate_enabled": true,
  "plate_count": 3,
  "plates": [
    {
      "formatted_text": "M-AB1234",
      "confidence": 0.9931
    },
    {
      "formatted_text": "B-CD5678",
      "confidence": 0.9897
    },
    {
      "formatted_text": "HH-EF901",
      "confidence": 0.9874
    }
  ]
}
```

This mode is suitable for parking-area images, vehicle queues and overview cameras.

---

## Security and privacy

Recommended deployment practices include:

* bind the API only to required interfaces
* protect remote API access with a token
* use a firewall or reverse proxy for network access
* use HTTPS when requests leave the local computer
* avoid placing credentials directly in source code
* restrict access to Runtime configuration
* process only images that may legally be used
* define retention and deletion rules for exported images
* avoid sending production camera credentials to public demo systems

CAR-READER-DLE can operate without transmitting recognition images to an external cloud service.

The customer remains responsible for the lawful operation of cameras, retention periods, access rights and the processing of vehicle or license plate data.

---

## Online demo

A public image-upload demonstration is available here:

[Open the CAR-READER-DLE demo](https://car-reader-dle.com/demo)

The demo is intended for product evaluation only.

Uploaded images should be processed temporarily and deleted automatically after the recognition request. Do not upload confidential images or images for which you do not have the required rights.

---

## Commercial product and licensing

CAR-READER-DLE is proprietary commercial software.

This repository does not grant a license to the Runtime, recognition models or protected product components.

Licensing options may include:

* project licenses
* device-based licenses
* workstation or server licenses
* OEM integration
* evaluation licenses
* pilot installations
* reseller or integration partnerships

Please contact the CAR-READER-DLE team for availability, supported platforms and project requirements.

---

## Who is this for?

CAR-READER-DLE is particularly suitable for:

* parking-system manufacturers
* access-control integrators
* barrier and gate manufacturers
* security-software providers
* camera-system integrators
* logistics and yard-management providers
* industrial automation companies
* visitor-management platforms
* fleet-management systems
* software vendors requiring embedded ALPR/ANPR functionality

---

## Evaluation and project planning

For a technical evaluation, the following information is helpful:

* target operating system
* processor and available memory
* expected plate countries and formats
* typical and maximum event frequency
* required response time
* single-plate or multi-plate use case
* RTSP and ROI requirements
* integration language or platform
* privacy and network requirements

A realistic pilot should use images and camera positions that represent the intended production environment.

---

## Documentation

* [User Manual](https://car-reader-dle.com/downloads/files/CAR-READER-DLE_Benutzerhandbuch_EN.pdf)
* [API Documentation](https://car-reader-dle.com/downloads/files/CAR-READER-DLE_API-Documentation_EN.pdf)
* [Online Demo](https://car-reader-dle.com/demo)
* [Product Website](https://car-reader-dle.com/)

---

## Contact

For product questions, technical evaluation, OEM integration or partnership enquiries:

* Website: `https://car-reader-dle.com/`
* Email: `info@car-reader-dle.com`
* Demo: `https://car-reader-dle.com/demo`

---

Copyright © CAR-READER-DLE. All rights reserved.
