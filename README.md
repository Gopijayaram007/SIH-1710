# Smart India Hackathon Workshop
# Date:18-09-2026
## Register Number:212225240042
## Name:Gopi jayaram E
## Problem Title
SIH 1710: Enhancing Navigation for Railway Station Facilities and Locations
## Problem Description
Background: Railway stations are complex environments with numerous facilities and locations such as ticket counters, platforms, restrooms, food courts, and waiting areas. Passengers often face difficulties in navigating these spaces, especially in large or unfamiliar stations. Efficient and user-friendly navigation systems are crucial for improving passenger experience, reducing congestion, and ensuring timely travel connections. Description: The problem involves developing a comprehensive navigation solution for railway stations that assists passengers in locating various facilities and destinations within the station premises. This includes creating detailed maps, providing real-time directions, and integrating features such as accessibility options for individuals with disabilities. The solution should be intuitive, easy to use, and accessible via multiple platforms, including mobile devices and digital kiosks. Key challenges include updating navigation information in real-time, ensuring accuracy, and accommodating the diverse needs of all passengers. Expected Solution: The expected solution is a multi-platform navigation system that provides detailed, real-time directions to all facilities and locations within a railway station. This system should include: A mobile application with 3D interactive maps and step-by-step navigation. Digital kiosks located throughout the station with touch-screen interfaces. Voice-guided navigation for visually impaired passengers. Regular updates to reflect changes in station layout and facility locations. Integration with existing railway apps and services for seamless user experience. The solution should enhance the overall passenger experience by reducing confusion, saving time, and improving accessibility within the station.

## Problem Creater's Organization
Ministry of Railway

# Smart India Hackathon Workshop

## SIH 1710: Enhancing Navigation for Railway Station Facilities and Locations

**Organization:** Ministry of Railways

Railway stations are complex environments with platforms, ticket counters, restrooms, food courts, waiting areas, and accessibility requirements. This repository documents two complementary solution directions for making station navigation easier, more inclusive, and easier to maintain.

## Idea 1: BLE Beacon + Offline-First Indoor Positioning

### Proposed Solution / Architecture

A Bluetooth Low Energy (BLE) beacon-based indoor positioning system layered with an offline-first mobile app and cloud sync for station updates.

![BLE beacon hardware](https://images.unsplash.com/photo-1558618666-fcd25c85cd64?auto=format&fit=crop&w=1400&q=80)

![Railway station interior](https://images.unsplash.com/photo-1474487548417-781cb71495f3?auto=format&fit=crop&w=1400&q=80)

- BLE beacons installed at fixed points such as platforms, entrances, counters, and restrooms broadcast unique IDs.
- The mobile app estimates the passenger's position from RSSI signals received from three or more nearby beacons.
- A pre-downloaded, vector-based map for each station is stored locally, so navigation works without a live internet connection.
- A lightweight admin panel and API let station staff update facility locations. Changes sync to devices when they reconnect.
- Kiosks use the same core map and routing engine on fixed hardware, with wired beacons where higher accuracy is needed.
- A voice navigation layer provides turn-by-turn cues for accessibility mode using platform-native text-to-speech.

```text
[BLE Beacons] --RSSI--> [Mobile App: Positioning Engine]
										|
							[Local Vector Map + Router]
										|
						  [TTS/Voice Layer] <--> [UI: 3D Map View]
										|
						  [Sync Service] <--> [Cloud Admin API/DB]
```

### Use Cases

- Passenger finds the shortest walking route from an entry gate to a platform.
- Visually impaired passenger receives voice-guided, step-by-step directions.
- Station staff updates a facility location, such as a relocated food stall, and pushes the change to devices.
- Passenger uses a station kiosk when phone battery or mobile data is unavailable.
- The app recalculates the route when the passenger deviates from the planned path.

### Technology Stack

- **Mobile app:** Flutter for a shared Android and iOS codebase
- **Positioning:** BLE beacons with RSSI trilateration using Kontakt.io, Estimote, or a custom implementation
- **Mapping and routing:** Custom vector map format with Dijkstra or A* pathfinding
- **Backend:** Node.js with Express or FastAPI
- **Database:** PostgreSQL for facility and location data, with Redis for sync caching
- **Kiosk:** Electron or a native touchscreen build reusing the map engine
- **Voice:** Android TTS and iOS `AVSpeechSynthesizer`
- **Sync:** REST API with delta updates so only changed facility data is transferred

### Dependencies

- Physical BLE beacon installation and procurement approval
- Accurate initial floor-plan digitization for each station
- Kiosk hardware deployment
- Network and power infrastructure at kiosk locations

## Idea 2: Computer Vision + QR/AR-Based Wayfinding

### Proposed Solution / Architecture

An augmented reality (AR) and QR-code-anchored navigation system that uses the passenger's phone camera and existing signage for positioning, without requiring dedicated beacon hardware.

![AR navigation on a smartphone](https://images.unsplash.com/photo-1516321318423-f06f85e504b3?auto=format&fit=crop&w=1400&q=80)

- QR codes placed at key junctions act as known reference points.
- After scanning a QR code, the app anchors the passenger's position in a pre-built 3D station map.
- ARCore or ARKit projects directional arrows onto the live camera feed for point-and-walk navigation.
- Between QR scans, accelerometer and gyroscope data provide dead reckoning to maintain an estimated position.
- The backend serves 3D map data, facility metadata, and optional real-time crowd or congestion data from station CCTV analytics.
- Kiosks show a simplified 2D version of the same map with touch-to-select destinations.
- Voice mode synchronizes audio cues with the route so visually impaired passengers can navigate without interpreting the AR display.

![QR code wayfinding anchor](https://images.unsplash.com/photo-1595079676339-1534801ad6cf?auto=format&fit=crop&w=1400&q=80)

```text
[QR Anchor Scan] --> [Position Lock] --> [IMU Dead Reckoning]
										|
							  [3D Map + Route Engine]
								  /                 \
					  [AR Overlay UI]      [Voice Cue Engine]
										|
					  [Backend: Map Data + Crowd Feed API]
```

### Use Cases

- Passenger scans a QR code at the entrance and follows AR arrows to a platform.
- Congestion-aware routing suggests an alternate path when a corridor is overcrowded.
- Passenger without app access uses a kiosk's simplified touch map.
- Visually impaired passenger relies on voice cues without needing AR or a camera.
- Facility managers update a QR anchor's linked location data instantly from a web dashboard.

### Technology Stack

- **Mobile app:** React Native, or native Android/iOS when AR performance is critical
- **AR:** ARCore for Android and ARKit for iOS
- **Positioning:** QR anchor scans fused with IMU dead reckoning
- **Mapping and routing:** Custom 3D map format with graph-based A* pathfinding
- **Crowd analytics (optional):** OpenCV or YOLO using approved CCTV feeds
- **Backend:** Django or FastAPI
- **Database:** PostgreSQL with PostGIS for spatial queries
- **Kiosk:** Web-based touchscreen app built with React and the shared backend map API
- **Voice:** Platform-native text-to-speech

### Dependencies

- QR signage placement and maintenance across stations
- Approved access to CCTV feeds for optional crowd analytics
- Accurate 3D map modeling for each station
- Sufficient phone camera quality, especially in low-light conditions

## Comparison

| Factor | BLE beacon positioning | QR/AR wayfinding |
| --- | --- | --- |
| Hardware deployment | Requires beacon installation and maintenance | Uses QR signage and passenger phones |
| Offline navigation | Strong, with pre-downloaded maps | Works after an anchor scan, with local map data |
| Positioning continuity | Continuous signal-based estimation | Periodic QR anchors plus IMU dead reckoning |
| Map experience | 2D/3D map with reliable route guidance | Camera-based AR arrows with a 2D/3D map fallback |
| Best fit | High-traffic stations needing continuous positioning | Fast pilots where new hardware is difficult to install |
