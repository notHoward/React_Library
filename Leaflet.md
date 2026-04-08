# React Leaflet - Complete Beginner's Guide

A comprehensive from-scratch guide to building maps with React Leaflet.

## Table of Contents

1. [What is React Leaflet](#what-is-react-leaflet)
2. [Core Concepts](#core-concepts)
3. [Installation](#installation)
4. [Basic Setup](#basic-setup)
5. [MapContainer - The Map Component](#mapcontainer---the-map-component)
6. [TileLayer - Map Backgrounds](#tilelayer---map-backgrounds)
7. [Markers - Adding Points](#markers---adding-points)
8. [Popup and Tooltip](#popup-and-tooltip)
9. [Event Handlers](#event-handlers)
10. [Dynamic Markers from Data](#dynamic-markers-from-data)
11. [Marker Clustering](#marker-clustering)
12. [Vector Layers - Circles, Rectangles, Polygons](#vector-layers)
13. [Using the Map Instance](#using-the-map-instance)
14. [Complete Examples](#complete-examples)
15. [API Reference](#api-reference)

---

## What is React Leaflet

React Leaflet is a library that brings **interactive maps** to your React applications using Leaflet (a popular open-source mapping library).

**What React Leaflet does:**

- Displays interactive maps with zooming and panning
- Adds markers, popups, and tooltips to maps
- Draws shapes (circles, rectangles, polygons) on maps
- Handles map events (click, zoom, drag)
- Works with various map tile providers

**The React Leaflet Flow:**

```
Component → MapContainer → TileLayer (background) → Markers/Shapes → User Interaction
```

**Why React Leaflet:**

```jsx
// Without React Leaflet (plain Leaflet + React is messy)
useEffect(() => {
  const map = L.map("map").setView([51.505, -0.09], 13);
  L.tileLayer("...").addTo(map);
  return () => map.remove();
}, []);

// With React Leaflet - declarative and clean!
<MapContainer center={[51.505, -0.09]} zoom={13}>
  <TileLayer url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png" />
  <Marker position={[51.505, -0.09]}>
    <Popup>Hello!</Popup>
  </Marker>
</MapContainer>;
```

## Core Concepts

### The 5 Main Components

```jsx
MapContainer; // 1. Container that holds the entire map
TileLayer; // 2. Background map tiles (streets, satellite, etc.)
Marker; // 3. Points of interest on the map
Popup; // 4. Information box that appears when clicking a marker
Tooltip; // 5. Small text that appears when hovering
```

## Installation

### Step 1: Install Packages

```bash
npm install leaflet react-leaflet
```

### Step 2: Import CSS (Important!)

```jsx
// In your main.jsx, App.jsx, or component file
import "leaflet/dist/leaflet.css";

// Without this CSS, markers will show as broken images!
```

### Step 3: Fix Marker Icons (Webpack/Vite Issue)

```jsx
// Add this after imports to fix default marker icons
import L from "leaflet";
import icon from "leaflet/dist/images/marker-icon.png";
import iconShadow from "leaflet/dist/images/marker-shadow.png";

let DefaultIcon = L.icon({
  iconUrl: icon,
  shadowUrl: iconShadow,
  iconSize: [25, 41],
  iconAnchor: [12, 41],
});

L.Marker.prototype.options.icon = DefaultIcon;
```

## Basic Setup

### The Simplest Map

```jsx
import { MapContainer, TileLayer, Marker, Popup } from "react-leaflet";
import "leaflet/dist/leaflet.css";

function SimpleMap() {
  // Center coordinates [latitude, longitude]
  const position = [51.505, -0.09]; // London

  return (
    <MapContainer
      center={position} // Starting position
      zoom={13} // Zoom level (0-18)
      style={{ height: "100vh", width: "100%" }} // IMPORTANT: must set height!
    >
      {/* Background tiles */}
      <TileLayer
        url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png"
        attribution='&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
      />

      {/* Marker */}
      <Marker position={position}>
        <Popup>Hello! I'm a popup.</Popup>
      </Marker>
    </MapContainer>
  );
}
```

### Important Note About Height

```jsx
// ❌ WRONG - Map won't appear!
<MapContainer center={[51.505, -0.09]} zoom={13}>
  {/* No height set */}
</MapContainer>

// ✅ CORRECT - Map needs explicit height
<MapContainer
  center={[51.505, -0.09]}
  zoom={13}
  style={{ height: '500px', width: '100%' }}
  // OR
  className="full-height-map"  // CSS: .full-height-map { height: 100vh; }
>
```

---

## MapContainer - The Map Component

### All MapContainer Props

```jsx
import { MapContainer } from "react-leaflet";

<MapContainer
  // REQUIRED
  center={[51.505, -0.09]} // [latitude, longitude] starting position
  zoom={13} // Zoom level (0 = world, 18 = buildings)
  // OPTIONAL - Sizing
  style={{ height: "100vh", width: "100%" }} // CSS styles
  className="map" // CSS class
  id="my-map" // HTML id
  // OPTIONAL - View Controls
  minZoom={3} // Minimum zoom level (default: 0)
  maxZoom={19} // Maximum zoom level (default: 18)
  zoomSnap={1} // Zoom steps (default: 1)
  wheelPxPerZoomLevel={120} // Mouse wheel sensitivity
  // OPTIONAL - Boundaries
  maxBounds={[
    [-90, -180],
    [90, 180],
  ]} // Restrict panning
  maxBoundsViscosity={1.0} // How strongly bounds are enforced
  // OPTIONAL - Interaction
  scrollWheelZoom={true} // Enable/disable wheel zoom
  doubleClickZoom={true} // Enable/disable double-click zoom
  dragging={true} // Enable/disable panning
  touchZoom={true} // Enable/disable touch zoom
  zoomControl={true} // Show zoom buttons
  // OPTIONAL - Other
  attributionControl={true} // Show attribution
  fadeAnimation={true} // Animate tile loading
  // EVENTS
  whenReady={(event) => console.log("Map ready", event.target)}
>
  {/* Children components */}
</MapContainer>;
```

### Creating a Reusable Map Component

```jsx
function MyMap({ center, zoom, markers, height = "500px" }) {
  return (
    <MapContainer
      center={center}
      zoom={zoom}
      style={{ height, width: "100%" }}
      scrollWheelZoom={true}
    >
      <TileLayer
        url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png"
        attribution="&copy; OpenStreetMap contributors"
      />

      {markers.map((marker) => (
        <Marker key={marker.id} position={marker.position}>
          <Popup>{marker.name}</Popup>
        </Marker>
      ))}
    </MapContainer>
  );
}
```

---

## TileLayer - Map Backgrounds

TileLayer provides the actual map imagery (streets, satellite, terrain, etc.).

### Basic TileLayer

```jsx
import { TileLayer } from "react-leaflet";

// OpenStreetMap (free, no API key)
<TileLayer
  url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png"
  attribution='&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
/>;

// URL pattern explained:
// {s} = subdomain (a, b, c) - load balancing
// {z} = zoom level
// {x} = tile x coordinate
// {y} = tile y coordinate
```

### Different Map Styles

```jsx
// OpenStreetMap (standard streets)
<TileLayer url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png" />

// OpenStreetMap (hot style)
<TileLayer url="https://{s}.tile.openstreetmap.fr/hot/{z}/{x}/{y}.png" />

// CartoDB Voyager (clean, light)
<TileLayer url="https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}{r}.png" />

// CartoDB Dark Matter (dark theme)
<TileLayer url="https://{s}.basemaps.cartocdn.com/dark_all/{z}/{x}/{y}{r}.png" />

// Stadia Maps Alidade Smooth (nice for data visualization)
<TileLayer url="https://tiles.stadiamaps.com/tiles/alidade_smooth/{z}/{x}/{y}{r}.png" />

// Stadia Maps Alidade Smooth Dark
<TileLayer url="https://tiles.stadiamaps.com/tiles/alidade_smooth_dark/{z}/{x}/{y}{r}.png" />

// Satellite (requires API key usually)
<TileLayer url="https://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}" />
```

### TileLayer with Custom Options

```jsx
<TileLayer
  url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png"
  attribution="&copy; OpenStreetMap"
  subdomains={["a", "b", "c"]} // Subdomains to use
  minZoom={1} // Min zoom for this layer
  maxZoom={19} // Max zoom for this layer
  opacity={0.8} // Transparency (0-1)
  zIndex={1} // Stacking order
  detectRetina={true} // Use retina tiles if available
  crossOrigin={true} // CORS support
/>
```

---

## Markers - Adding Points

Markers are used to show points of interest on the map.

### Basic Marker

```jsx
import { Marker } from "react-leaflet";

function MapWithMarker() {
  const position = [51.505, -0.09];

  return (
    <MapContainer center={position} zoom={13}>
      <TileLayer url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png" />

      {/* Simple marker */}
      <Marker position={position} />

      {/* Marker with popup */}
      <Marker position={position}>
        <Popup>Hello world!</Popup>
      </Marker>
    </MapContainer>
  );
}
```

### Custom Marker Icons

```jsx
import L from "leaflet";

// Create custom icon
const customIcon = L.icon({
  iconUrl: "/path/to/custom-icon.png",
  iconSize: [32, 32], // Size of the icon
  iconAnchor: [16, 32], // Point of the icon that corresponds to marker position
  popupAnchor: [0, -32], // Point where popup opens relative to iconAnchor
  shadowUrl: "/path/to/shadow.png",
  shadowSize: [32, 32],
});

// Use custom icon
<Marker position={[51.505, -0.09]} icon={customIcon}>
  <Popup>Custom icon marker</Popup>
</Marker>;

// Different color markers (using Leaflet's extra markers)
// Install: npm install leaflet-color-markers
import "leaflet-color-markers/css/leaflet-color-markers.css";
import L from "leaflet";

const redIcon = L.icon({
  iconUrl:
    "https://cdn.rawgit.com/pointhi/leaflet-color-markers/master/img/marker-icon-2x-red.png",
  shadowUrl:
    "https://cdnjs.cloudflare.com/ajax/libs/leaflet/0.7.7/images/marker-shadow.png",
  iconSize: [25, 41],
  iconAnchor: [12, 41],
  popupAnchor: [1, -34],
  shadowSize: [41, 41],
});

<Marker position={[51.505, -0.09]} icon={redIcon}>
  <Popup>Red marker!</Popup>
</Marker>;
```

### Draggable Markers

```jsx
import { useState } from "react";
import { Marker, Popup } from "react-leaflet";

function DraggableMarker() {
  const [position, setPosition] = useState([51.505, -0.09]);

  const eventHandlers = {
    dragend: (event) => {
      const marker = event.target;
      const newPosition = marker.getLatLng();
      setPosition([newPosition.lat, newPosition.lng]);
      console.log("New position:", newPosition);
    },
  };

  return (
    <Marker position={position} draggable={true} eventHandlers={eventHandlers}>
      <Popup>
        Latitude: {position[0]}
        <br />
        Longitude: {position[1]}
      </Popup>
    </Marker>
  );
}
```

---

## Popup and Tooltip

### Popup Component

```jsx
import { Marker, Popup } from 'react-leaflet';

// Basic popup
<Marker position={[51.505, -0.09]}>
  <Popup>Simple text popup</Popup>
</Marker>

// Popup with HTML content
<Marker position={[51.505, -0.09]}>
  <Popup>
    <div>
      <h3>Title</h3>
      <p>This is a paragraph</p>
      <img src="/image.jpg" alt="Description" />
      <a href="/link">Click here</a>
    </div>
  </Popup>
</Marker>

// Popup with custom options
<Marker position={[51.505, -0.09]}>
  <Popup
    minWidth={200}      // Minimum width
    maxWidth={300}      // Maximum width
    maxHeight={200}     // Maximum height (scrolls if content exceeds)
    autoPan={true}      // Auto-pan map to keep popup in view
    closeButton={true}  // Show close button
    autoClose={true}    // Close when another popup opens
    className="custom-popup"
  >
    Content here
  </Popup>
</Marker>
```

### Tooltip Component

```jsx
import { Marker, Tooltip } from 'react-leaflet';

// Basic tooltip (appears on hover)
<Marker position={[51.505, -0.09]}>
  <Tooltip>Hover text</Tooltip>
</Marker>

// Tooltip with options
<Marker position={[51.505, -0.09]}>
  <Tooltip
    permanent={false}    // Show permanently (true) or on hover (false)
    direction="top"      // 'top', 'bottom', 'left', 'right', 'center'
    offset={[0, -20]}    // Offset from marker [x, y]
    opacity={0.9}        // Transparency
    sticky={true}        // Follow mouse movement
  >
    Tooltip content
  </Tooltip>
</Marker>

// Popup and Tooltip together
<Marker position={[51.505, -0.09]}>
  <Tooltip>Hover me</Tooltip>
  <Popup>Click me</Popup>
</Marker>
```

---

## Event Handlers

### Marker Event Handlers

```jsx
function InteractiveMarker() {
  const handleClick = (event) => {
    console.log("Marker clicked!", event.latlng);
    alert("Marker clicked!");
  };

  const handleMouseOver = () => {
    console.log("Mouse entered marker");
  };

  const handleMouseOut = () => {
    console.log("Mouse left marker");
  };

  return (
    <Marker
      position={[51.505, -0.09]}
      eventHandlers={{
        click: handleClick,
        mouseover: handleMouseOver,
        mouseout: handleMouseOut,
        dragstart: (e) => console.log("Drag started"),
        drag: (e) => console.log("Dragging..."),
        dragend: (e) => console.log("Drag ended"),
        add: (e) => console.log("Marker added to map"),
        remove: (e) => console.log("Marker removed from map"),
        popupopen: (e) => console.log("Popup opened"),
        popupclose: (e) => console.log("Popup closed"),
      }}
    >
      <Popup>Interactive marker</Popup>
    </Marker>
  );
}
```

### Map Event Handlers

```jsx
import { useMapEvents } from "react-leaflet";

function MapEvents() {
  const map = useMapEvents({
    click: (e) => {
      console.log("Map clicked at:", e.latlng);
    },
    dblclick: (e) => {
      console.log("Map double-clicked");
    },
    moveend: () => {
      const center = map.getCenter();
      console.log("Map moved to:", center);
    },
    zoomend: () => {
      const zoom = map.getZoom();
      console.log("Zoom level:", zoom);
    },
    load: () => {
      console.log("Map loaded");
    },
    resize: () => {
      console.log("Map resized");
    },
  });

  return null;
}

// Usage
<MapContainer center={[51.505, -0.09]} zoom={13}>
  <TileLayer url="..." />
  <MapEvents />
</MapContainer>;
```

---

## Dynamic Markers from Data

### Rendering Markers from an Array

```jsx
function ArcadeMap() {
  // Sample data: array of locations
  const locations = [
    {
      id: 1,
      name: "Arcade 1",
      position: [51.505, -0.09],
      address: "123 Main St",
    },
    {
      id: 2,
      name: "Arcade 2",
      position: [51.51, -0.1],
      address: "456 Oak Ave",
    },
    {
      id: 3,
      name: "Arcade 3",
      position: [51.5, -0.08],
      address: "789 Pine Rd",
    },
  ];

  return (
    <MapContainer
      center={[51.505, -0.09]}
      zoom={13}
      style={{ height: "500px" }}
    >
      <TileLayer url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png" />

      {locations.map((location) => (
        <Marker key={location.id} position={location.position}>
          <Popup>
            <div>
              <h3>{location.name}</h3>
              <p>{location.address}</p>
            </div>
          </Popup>
        </Marker>
      ))}
    </MapContainer>
  );
}
```

### Loading Data from API

```jsx
import { useState, useEffect } from "react";
import { MapContainer, TileLayer, Marker, Popup } from "react-leaflet";

function ApiMap() {
  const [locations, setLocations] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetch("/api/locations")
      .then((res) => res.json())
      .then((data) => {
        setLocations(data);
        setLoading(false);
      });
  }, []);

  if (loading) return <div>Loading map data...</div>;

  return (
    <MapContainer
      center={[51.505, -0.09]}
      zoom={13}
      style={{ height: "500px" }}
    >
      <TileLayer url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png" />

      {locations.map((location) => (
        <Marker key={location.id} position={[location.lat, location.lng]}>
          <Popup>{location.name}</Popup>
        </Marker>
      ))}
    </MapContainer>
  );
}
```

### Filtering Markers

```jsx
function FilterableMap() {
  const [filter, setFilter] = useState("all");
  const [selectedCategory, setSelectedCategory] = useState(null);

  const allLocations = [
    { id: 1, name: "Coffee Shop", category: "cafe", position: [51.505, -0.09] },
    { id: 2, name: "Restaurant", category: "food", position: [51.51, -0.1] },
    { id: 3, name: "Park", category: "outdoor", position: [51.5, -0.08] },
  ];

  const filteredLocations = allLocations.filter((location) => {
    if (filter === "all") return true;
    return location.category === filter;
  });

  return (
    <div>
      <div>
        <button onClick={() => setFilter("all")}>All</button>
        <button onClick={() => setFilter("cafe")}>Cafes</button>
        <button onClick={() => setFilter("food")}>Restaurants</button>
        <button onClick={() => setFilter("outdoor")}>Outdoor</button>
      </div>

      <MapContainer
        center={[51.505, -0.09]}
        zoom={13}
        style={{ height: "500px" }}
      >
        <TileLayer url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png" />

        {filteredLocations.map((location) => (
          <Marker
            key={location.id}
            position={location.position}
            eventHandlers={{
              click: () => setSelectedCategory(location),
            }}
          >
            <Popup>{location.name}</Popup>
          </Marker>
        ))}
      </MapContainer>

      {selectedCategory && <div>Selected: {selectedCategory.name}</div>}
    </div>
  );
}
```

---

## Marker Clustering

When you have many markers close together, clustering groups them for better performance and appearance.

### Installation

```bash
npm install react-leaflet-cluster
```

### Basic Clustering

```jsx
import MarkerClusterGroup from "react-leaflet-cluster";
import { MapContainer, TileLayer, Marker, Popup } from "react-leaflet";

function ClusteredMarkers() {
  // Generate many random markers
  const markers = Array.from({ length: 200 }, (_, i) => ({
    id: i,
    position: [
      51.505 + (Math.random() - 0.5) * 0.2,
      -0.09 + (Math.random() - 0.5) * 0.2,
    ],
    name: `Location ${i}`,
  }));

  return (
    <MapContainer
      center={[51.505, -0.09]}
      zoom={13}
      style={{ height: "500px" }}
    >
      <TileLayer url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png" />

      {/* Cluster group wraps all markers */}
      <MarkerClusterGroup>
        {markers.map((marker) => (
          <Marker key={marker.id} position={marker.position}>
            <Popup>{marker.name}</Popup>
          </Marker>
        ))}
      </MarkerClusterGroup>
    </MapContainer>
  );
}
```

### Custom Cluster Styling

```jsx
import MarkerClusterGroup from "react-leaflet-cluster";
import L from "leaflet";

// Custom cluster icon function
const createClusterCustomIcon = (cluster) => {
  return L.divIcon({
    html: `<span>${cluster.getChildCount()}</span>`,
    className: "custom-marker-cluster",
    iconSize: L.point(40, 40),
  });
};

function CustomClusteredMarkers() {
  return (
    <MarkerClusterGroup
      iconCreateFunction={createClusterCustomIcon}
      showCoverageOnHover={true} // Show area on hover
      zoomToBoundsOnClick={true} // Zoom to cluster on click
      spiderfyOnMaxZoom={true} // Spread markers when zoomed
      removeOutsideVisibleBounds={true} // Improve performance
      animate={true} // Animate clustering
      maxClusterRadius={80} // Maximum radius for cluster
    >
      {/* Markers here */}
    </MarkerClusterGroup>
  );
}
```

### Cluster with Custom CSS

```css
/* In your CSS file */
.custom-marker-cluster {
  background: #3b82f6;
  border-radius: 50%;
  color: white;
  font-weight: bold;
  display: flex;
  align-items: center;
  justify-content: center;
  border: 2px solid white;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
}

.custom-marker-cluster span {
  font-size: 14px;
}
```

---

## Vector Layers

### Circles

```jsx
import { Circle, MapContainer, TileLayer } from "react-leaflet";

function CircleExample() {
  return (
    <MapContainer
      center={[51.505, -0.09]}
      zoom={13}
      style={{ height: "500px" }}
    >
      <TileLayer url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png" />

      <Circle
        center={[51.505, -0.09]}
        radius={500} // Radius in meters
        pathOptions={{
          color: "red", // Border color
          fillColor: "blue", // Fill color
          fillOpacity: 0.5, // Fill opacity
          weight: 2, // Border width
          opacity: 1, // Border opacity
        }}
        eventHandlers={{
          click: () => console.log("Circle clicked"),
          mouseover: () => console.log("Mouse over circle"),
        }}
      />
    </MapContainer>
  );
}
```

### Rectangles

```jsx
import { Rectangle, MapContainer, TileLayer } from "react-leaflet";

function RectangleExample() {
  // Rectangle bounds: [[south, west], [north, east]]
  const bounds = [
    [51.49, -0.08], // Southwest corner
    [51.52, -0.06], // Northeast corner
  ];

  return (
    <MapContainer
      center={[51.505, -0.07]}
      zoom={13}
      style={{ height: "500px" }}
    >
      <TileLayer url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png" />

      <Rectangle
        bounds={bounds}
        pathOptions={{
          color: "green",
          fillColor: "lightgreen",
          fillOpacity: 0.4,
          weight: 2,
        }}
      />
    </MapContainer>
  );
}
```

### Polygons

```jsx
import { Polygon, MapContainer, TileLayer } from "react-leaflet";

function PolygonExample() {
  // Polygon vertices in order
  const polygonPositions = [
    [51.515, -0.09],
    [51.51, -0.08],
    [51.51, -0.1],
    [51.515, -0.09], // Back to start to close polygon
  ];

  return (
    <MapContainer center={[51.51, -0.09]} zoom={14} style={{ height: "500px" }}>
      <TileLayer url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png" />

      <Polygon
        positions={polygonPositions}
        pathOptions={{
          color: "purple",
          fillColor: "lavender",
          fillOpacity: 0.5,
          weight: 2,
        }}
      />
    </MapContainer>
  );
}
```

### Multiple Shapes

```jsx
function MapWithShapes() {
  return (
    <MapContainer
      center={[51.505, -0.09]}
      zoom={13}
      style={{ height: "500px" }}
    >
      <TileLayer url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png" />

      {/* Circle */}
      <Circle
        center={[51.505, -0.09]}
        radius={300}
        pathOptions={{ color: "red" }}
      >
        <Tooltip>Circle area</Tooltip>
      </Circle>

      {/* Rectangle */}
      <Rectangle
        bounds={[
          [51.5, -0.1],
          [51.51, -0.08],
        ]}
        pathOptions={{ color: "blue" }}
      >
        <Tooltip>Rectangle area</Tooltip>
      </Rectangle>

      {/* Polygon */}
      <Polygon
        positions={[
          [51.51, -0.12],
          [51.515, -0.1],
          [51.505, -0.11],
        ]}
        pathOptions={{ color: "green" }}
      >
        <Tooltip>Polygon area</Tooltip>
      </Polygon>
    </MapContainer>
  );
}
```

---

## Using the Map Instance

### Accessing the Map with useMap

```jsx
import { useMap } from "react-leaflet";

function MapController() {
  const map = useMap();

  const flyToLocation = () => {
    map.flyTo([51.505, -0.09], 15, {
      duration: 2, // Animation duration in seconds
    });
  };

  const zoomIn = () => {
    map.zoomIn();
  };

  const zoomOut = () => {
    map.zoomOut();
  };

  const setView = () => {
    map.setView([48.8566, 2.3522], 12);
  };

  const fitBounds = () => {
    const bounds = [
      [51.49, -0.1],
      [51.52, -0.06],
    ];
    map.fitBounds(bounds);
  };

  return (
    <div style={{ position: "absolute", top: 10, right: 10, zIndex: 1000 }}>
      <button onClick={flyToLocation}>Fly to London</button>
      <button onClick={zoomIn}>Zoom In</button>
      <button onClick={zoomOut}>Zoom Out</button>
      <button onClick={setView}>Go to Paris</button>
      <button onClick={fitBounds}>Fit Bounds</button>
    </div>
  );
}

// Usage
<MapContainer center={[51.505, -0.09]} zoom={13}>
  <TileLayer url="..." />
  <MapController />
</MapContainer>;
```

### Getting Current Map State

```jsx
function MapInfo() {
  const map = useMap();
  const [center, setCenter] = useState([0, 0]);
  const [zoom, setZoom] = useState(0);

  useEffect(() => {
    const updateInfo = () => {
      const newCenter = map.getCenter();
      setCenter([newCenter.lat, newCenter.lng]);
      setZoom(map.getZoom());
    };

    map.on("moveend", updateInfo);
    updateInfo();

    return () => {
      map.off("moveend", updateInfo);
    };
  }, [map]);

  return (
    <div
      style={{
        position: "absolute",
        bottom: 10,
        left: 10,
        background: "white",
        padding: "10px",
        zIndex: 1000,
      }}
    >
      <div>
        Center: {center[0].toFixed(4)}, {center[1].toFixed(4)}
      </div>
      <div>Zoom: {zoom}</div>
    </div>
  );
}
```

### Getting User Location

```jsx
import { useMap } from "react-leaflet";
import { useState } from "react";

function LocateUser() {
  const map = useMap();
  const [locating, setLocating] = useState(false);
  const [error, setError] = useState(null);

  const getLocation = () => {
    setLocating(true);
    setError(null);

    if (!navigator.geolocation) {
      setError("Geolocation not supported");
      setLocating(false);
      return;
    }

    navigator.geolocation.getCurrentPosition(
      (position) => {
        const { latitude, longitude } = position.coords;
        map.flyTo([latitude, longitude], 15);

        // Add a marker at user location
        L.marker([latitude, longitude])
          .addTo(map)
          .bindPopup("You are here!")
          .openPopup();

        setLocating(false);
      },
      (err) => {
        setError(err.message);
        setLocating(false);
      },
    );
  };

  return (
    <button
      onClick={getLocation}
      disabled={locating}
      style={{ position: "absolute", top: 10, right: 10, zIndex: 1000 }}
    >
      {locating ? "Locating..." : "Find My Location"}
    </button>
  );
}

// Usage
<MapContainer center={[51.505, -0.09]} zoom={13}>
  <TileLayer url="..." />
  <LocateUser />
</MapContainer>;
```

---

## Complete Examples

### Example 1: Store Locator

```jsx
import { useState, useEffect } from "react";
import { MapContainer, TileLayer, Marker, Popup, useMap } from "react-leaflet";
import "leaflet/dist/leaflet.css";

// Fix marker icons
import L from "leaflet";
import icon from "leaflet/dist/images/marker-icon.png";
import iconShadow from "leaflet/dist/images/marker-shadow.png";

let DefaultIcon = L.icon({
  iconUrl: icon,
  shadowUrl: iconShadow,
  iconSize: [25, 41],
  iconAnchor: [12, 41],
});
L.Marker.prototype.options.icon = DefaultIcon;

function ChangeView({ center, zoom }) {
  const map = useMap();
  useEffect(() => {
    map.setView(center, zoom);
  }, [center, zoom]);
  return null;
}

function StoreLocator() {
  const [searchTerm, setSearchTerm] = useState("");
  const [userLocation, setUserLocation] = useState(null);
  const [mapCenter, setMapCenter] = useState([51.505, -0.09]);
  const [zoom, setZoom] = useState(13);

  // Sample store data
  const stores = [
    {
      id: 1,
      name: "Store 1",
      position: [51.505, -0.09],
      category: "electronics",
    },
    { id: 2, name: "Store 2", position: [51.51, -0.1], category: "clothing" },
    {
      id: 3,
      name: "Store 3",
      position: [51.5, -0.08],
      category: "electronics",
    },
  ];

  const filteredStores = stores.filter((store) =>
    store.name.toLowerCase().includes(searchTerm.toLowerCase()),
  );

  const getUserLocation = () => {
    if (navigator.geolocation) {
      navigator.geolocation.getCurrentPosition((position) => {
        const { latitude, longitude } = position.coords;
        setUserLocation([latitude, longitude]);
        setMapCenter([latitude, longitude]);
        setZoom(14);
      });
    }
  };

  return (
    <div style={{ display: "flex", height: "100vh" }}>
      {/* Sidebar */}
      <div
        style={{
          width: "300px",
          padding: "20px",
          background: "#f5f5f5",
          overflow: "auto",
        }}
      >
        <h2>Store Locator</h2>

        <input
          type="text"
          placeholder="Search stores..."
          value={searchTerm}
          onChange={(e) => setSearchTerm(e.target.value)}
          style={{ width: "100%", padding: "8px", marginBottom: "10px" }}
        />

        <button
          onClick={getUserLocation}
          style={{ width: "100%", padding: "8px", marginBottom: "20px" }}
        >
          Use My Location
        </button>

        <div>
          <h3>Nearby Stores ({filteredStores.length})</h3>
          {filteredStores.map((store) => (
            <div
              key={store.id}
              style={{
                padding: "10px",
                marginBottom: "10px",
                background: "white",
                borderRadius: "4px",
                cursor: "pointer",
              }}
              onClick={() => {
                setMapCenter(store.position);
                setZoom(15);
              }}
            >
              <strong>{store.name}</strong>
              <div>Category: {store.category}</div>
            </div>
          ))}
        </div>
      </div>

      {/* Map */}
      <div style={{ flex: 1 }}>
        <MapContainer
          center={mapCenter}
          zoom={zoom}
          style={{ height: "100%", width: "100%" }}
        >
          <TileLayer url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png" />
          <ChangeView center={mapCenter} zoom={zoom} />

          {filteredStores.map((store) => (
            <Marker key={store.id} position={store.position}>
              <Popup>
                <div>
                  <h3>{store.name}</h3>
                  <p>Category: {store.category}</p>
                </div>
              </Popup>
            </Marker>
          ))}

          {userLocation && (
            <Marker position={userLocation}>
              <Popup>Your Location</Popup>
            </Marker>
          )}
        </MapContainer>
      </div>
    </div>
  );
}

export default StoreLocator;
```

### Example 2: Map with Drawing Tools

```jsx
import { useState, useRef } from "react";
import {
  MapContainer,
  TileLayer,
  Circle,
  Rectangle,
  Polygon,
  useMapEvents,
} from "react-leaflet";
import "leaflet/dist/leaflet.css";

// Fix marker icons (same as above)
import L from "leaflet";
import icon from "leaflet/dist/images/marker-icon.png";
import iconShadow from "leaflet/dist/images/marker-shadow.png";

let DefaultIcon = L.icon({
  iconUrl: icon,
  shadowUrl: iconShadow,
  iconSize: [25, 41],
  iconAnchor: [12, 41],
});
L.Marker.prototype.options.icon = DefaultIcon;

function DrawingControls({ onShapeAdd }) {
  const [drawingMode, setDrawingMode] = useState(null);
  const [points, setPoints] = useState([]);

  useMapEvents({
    click: (e) => {
      if (drawingMode === "polygon") {
        setPoints([...points, [e.latlng.lat, e.latlng.lng]]);
      }
    },
    dblclick: () => {
      if (drawingMode === "polygon" && points.length > 2) {
        onShapeAdd({ type: "polygon", points: [...points, points[0]] });
        setPoints([]);
        setDrawingMode(null);
      }
    },
  });

  const addCircle = () => {
    const center = [51.505, -0.09];
    onShapeAdd({ type: "circle", center, radius: 500 });
  };

  const addRectangle = () => {
    const bounds = [
      [51.49, -0.1],
      [51.52, -0.06],
    ];
    onShapeAdd({ type: "rectangle", bounds });
  };

  return (
    <div
      style={{
        position: "absolute",
        top: 10,
        left: 10,
        zIndex: 1000,
        background: "white",
        padding: "10px",
        borderRadius: "4px",
      }}
    >
      <button onClick={addCircle}>Add Circle</button>
      <button onClick={addRectangle}>Add Rectangle</button>
      <button onClick={() => setDrawingMode("polygon")}>
        {drawingMode === "polygon"
          ? "Drawing Polygon (double-click to finish)"
          : "Draw Polygon"}
      </button>
      {drawingMode === "polygon" && points.length > 0 && (
        <div>Points: {points.length} (double-click to finish)</div>
      )}
    </div>
  );
}

function MapWithDrawing() {
  const [shapes, setShapes] = useState([]);

  const addShape = (shape) => {
    setShapes([...shapes, { ...shape, id: Date.now() }]);
  };

  return (
    <MapContainer
      center={[51.505, -0.09]}
      zoom={13}
      style={{ height: "100vh", width: "100%" }}
    >
      <TileLayer url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png" />

      <DrawingControls onShapeAdd={addShape} />

      {shapes.map((shape) => {
        if (shape.type === "circle") {
          return (
            <Circle
              key={shape.id}
              center={shape.center}
              radius={shape.radius}
              pathOptions={{ color: "blue", fillOpacity: 0.3 }}
            />
          );
        }
        if (shape.type === "rectangle") {
          return (
            <Rectangle
              key={shape.id}
              bounds={shape.bounds}
              pathOptions={{ color: "green", fillOpacity: 0.3 }}
            />
          );
        }
        if (shape.type === "polygon") {
          return (
            <Polygon
              key={shape.id}
              positions={shape.points}
              pathOptions={{ color: "red", fillOpacity: 0.3 }}
            />
          );
        }
        return null;
      })}
    </MapContainer>
  );
}

export default MapWithDrawing;
```

---

## API Reference

### Core Components

| Component    | Purpose          | Required Props             |
| ------------ | ---------------- | -------------------------- |
| MapContainer | Map container    | center, zoom, style/height |
| TileLayer    | Background tiles | url                        |
| Marker       | Point on map     | position                   |
| Popup        | Click info       | (children)                 |
| Tooltip      | Hover info       | (children)                 |
| Circle       | Circle shape     | center, radius             |
| Rectangle    | Rectangle shape  | bounds                     |
| Polygon      | Polygon shape    | positions                  |

### Hooks

| Hook         | Purpose                | Returns               |
| ------------ | ---------------------- | --------------------- |
| useMap       | Access map instance    | Leaflet map object    |
| useMapEvents | Listen to map events   | Event handlers object |
| useMapEvent  | Listen to single event | -                     |

### Common Map Methods

```jsx
const map = useMap();

map.setView([lat, lng], zoom); // Set view
map.flyTo([lat, lng], zoom); // Animated fly to
map.zoomIn(); // Zoom in
map.zoomOut(); // Zoom out
map.getCenter(); // Get current center
map.getZoom(); // Get current zoom
map.fitBounds([
  [lat1, lng1],
  [lat2, lng2],
]); // Fit to bounds
```

## Troubleshooting

Problem 1: Map not showing

Solutions:

- Check height is set on MapContainer
- Verify Leaflet CSS is imported
- Ensure TileLayer has valid URL

Problem 2: Markers showing as broken images

Solutions:

- Import Leaflet CSS
- Fix marker icon paths (see installation section)

Problem 3: Map flickers or re-renders too much

Solutions:

- Move event handlers outside component when possible
- Use useMemo for marker arrays
- Avoid recreating large marker lists on each render

## Useful Resources

- React Leaflet Docs: https://react-leaflet.js.org/
- Leaflet Docs: https://leafletjs.com/
- Available Tile Providers: https://leaflet-extras.github.io/leaflet-providers/preview/

---

Created for beginners learning React Leaflet
