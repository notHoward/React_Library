# Geolocation in React - Complete Beginner's Guide

A comprehensive from-scratch guide to getting and tracking user location in React.

## Table of Contents

1. [What is Geolocation](#what-is-geolocation)
2. [Browser Geolocation API](#browser-geolocation-api)
3. [Basic Setup](#basic-setup)
4. [Getting User Location (One Time)](#getting-user-location-one-time)
5. [Tracking User Location (Continuous)](#tracking-user-location-continuous)
6. [Error Handling](#error-handling)
7. [Custom Geolocation Hook](#custom-geolocation-hook)
8. [Geolocation with React Leaflet](#geolocation-with-react-leaflet)
9. [Geolocation with Google Maps](#geolocation-with-google-maps)
10. [Distance Calculation](#distance-calculation)
11. [Reverse Geocoding (Address from Coordinates)](#reverse-geocoding)
12. [Complete Examples](#complete-examples)
13. [API Reference](#api-reference)

---

## What is Geolocation

Geolocation is the ability to get the user's physical location (latitude and longitude) using their browser.

**What Geolocation can do:**

- Get user's current location (one time)
- Track user's movement continuously
- Calculate distances between locations
- Show nearby places (stores, restaurants, etc.)
- Create location-based features

**Browser Support:**

```
✅ Chrome (all versions)
✅ Firefox (all versions)
✅ Safari (all versions)
✅ Edge (all versions)
✅ Mobile browsers (iOS, Android)
⚠️ HTTPS required (or localhost)
```

**How it works:**

```
User visits website → Browser asks permission → User allows → App gets coordinates
```

---

## Browser Geolocation API

### The Core Methods

```javascript
// 1. Get current position (one time)
navigator.geolocation.getCurrentPosition(success, error, options);

// 2. Watch position (continuous tracking)
navigator.geolocation.watchPosition(success, error, options);

// 3. Stop watching
navigator.geolocation.clearWatch(watchId);

// Success callback receives:
{
  coords: {
    latitude: 51.505,      // Latitude in degrees
    longitude: -0.09,      // Longitude in degrees
    accuracy: 10,          // Accuracy in meters
    altitude: null,        // Altitude in meters
    altitudeAccuracy: null, // Altitude accuracy
    heading: null,         // Direction in degrees
    speed: null            // Speed in m/s
  },
  timestamp: 1634567890123  // When position was taken
}

// Error callback receives:
{
  code: 1,                  // Error code
  message: "User denied"    // Error message
}
```

### Error Codes

```javascript
// Error code meanings:
PERMISSION_DENIED = 1; // User denied permission
POSITION_UNAVAILABLE = 2; // Position couldn't be determined
TIMEOUT = 3; // Request timed out
```

### Options Object

```javascript
const options = {
  enableHighAccuracy: true, // Use GPS if available (slower, more battery)
  timeout: 5000, // Maximum time to wait (ms)
  maximumAge: 0, // Accept cached position (ms), 0 = always fresh
};
```

---

## Basic Setup

### Simple One-Time Location

```jsx
import { useState } from "react";

function SimpleGeolocation() {
  const [location, setLocation] = useState(null);
  const [error, setError] = useState(null);
  const [loading, setLoading] = useState(false);

  const getLocation = () => {
    setLoading(true);
    setError(null);

    // Check if browser supports geolocation
    if (!navigator.geolocation) {
      setError("Geolocation is not supported by your browser");
      setLoading(false);
      return;
    }

    // Get current position
    navigator.geolocation.getCurrentPosition(
      // Success callback
      (position) => {
        const { latitude, longitude } = position.coords;
        setLocation({ latitude, longitude });
        setLoading(false);
        console.log("Location:", latitude, longitude);
      },
      // Error callback
      (error) => {
        setError(error.message);
        setLoading(false);
        console.error("Error:", error);
      },
      // Options
      {
        enableHighAccuracy: true,
        timeout: 5000,
        maximumAge: 0,
      },
    );
  };

  return (
    <div>
      <button onClick={getLocation} disabled={loading}>
        {loading ? "Getting location..." : "Get My Location"}
      </button>

      {location && (
        <div>
          <h3>Your Location:</h3>
          <p>Latitude: {location.latitude}</p>
          <p>Longitude: {location.longitude}</p>
        </div>
      )}

      {error && <div style={{ color: "red" }}>Error: {error}</div>}
    </div>
  );
}
```

---

## Getting User Location (One Time)

### Complete Example with All Options

```jsx
import { useState } from "react";

function GetLocation() {
  const [location, setLocation] = useState(null);
  const [error, setError] = useState(null);
  const [loading, setLoading] = useState(false);
  const [accuracy, setAccuracy] = useState(null);

  const getLocation = () => {
    setLoading(true);
    setError(null);

    if (!navigator.geolocation) {
      setError("Geolocation not supported");
      setLoading(false);
      return;
    }

    // Options for better accuracy
    const options = {
      enableHighAccuracy: true, // Use GPS
      timeout: 10000, // Wait 10 seconds max
      maximumAge: 0, // Don't use cached position
    };

    navigator.geolocation.getCurrentPosition(
      (position) => {
        const { latitude, longitude, accuracy } = position.coords;

        setLocation({
          lat: latitude,
          lng: longitude,
          accuracy: accuracy,
        });

        setAccuracy(accuracy);
        setLoading(false);

        // Store in localStorage for later use
        localStorage.setItem(
          "userLocation",
          JSON.stringify({
            lat: latitude,
            lng: longitude,
            timestamp: Date.now(),
          }),
        );
      },
      (error) => {
        let message = "";
        switch (error.code) {
          case error.PERMISSION_DENIED:
            message = "You denied permission to access your location";
            break;
          case error.POSITION_UNAVAILABLE:
            message = "Location information is unavailable";
            break;
          case error.TIMEOUT:
            message = "Request timed out";
            break;
          default:
            message = "An unknown error occurred";
        }
        setError(message);
        setLoading(false);
      },
      options,
    );
  };

  // Check for cached location on mount
  const cachedLocation = localStorage.getItem("userLocation");

  return (
    <div style={{ padding: "20px" }}>
      <h2>Get Your Location</h2>

      <button
        onClick={getLocation}
        disabled={loading}
        style={{
          padding: "10px 20px",
          fontSize: "16px",
          cursor: "pointer",
          backgroundColor: "#007bff",
          color: "white",
          border: "none",
          borderRadius: "4px",
        }}
      >
        {loading ? "Getting Location..." : "Get Current Location"}
      </button>

      {location && (
        <div style={{ marginTop: "20px" }}>
          <h3>📍 Your Location</h3>
          <p>
            <strong>Latitude:</strong> {location.lat}
          </p>
          <p>
            <strong>Longitude:</strong> {location.lng}
          </p>
          <p>
            <strong>Accuracy:</strong> ±{Math.round(location.accuracy)} meters
          </p>

          {/* Link to open in Google Maps */}
          <a
            href={`https://www.google.com/maps?q=${location.lat},${location.lng}`}
            target="_blank"
            rel="noopener noreferrer"
          >
            Open in Google Maps
          </a>
        </div>
      )}

      {cachedLocation && !location && (
        <div style={{ marginTop: "20px" }}>
          <h3>Cached Location</h3>
          <pre>{cachedLocation}</pre>
        </div>
      )}

      {error && (
        <div style={{ marginTop: "20px", color: "red" }}>
          <strong>Error:</strong> {error}
        </div>
      )}
    </div>
  );
}
```

---

## Tracking User Location (Continuous)

### Real-time Position Tracking

```jsx
import { useState, useEffect } from "react";

function TrackLocation() {
  const [position, setPosition] = useState(null);
  const [error, setError] = useState(null);
  const [isTracking, setIsTracking] = useState(false);
  const [watchId, setWatchId] = useState(null);
  const [history, setHistory] = useState([]);

  const startTracking = () => {
    if (!navigator.geolocation) {
      setError("Geolocation not supported");
      return;
    }

    const options = {
      enableHighAccuracy: true,
      timeout: 5000,
      maximumAge: 0,
    };

    const id = navigator.geolocation.watchPosition(
      (position) => {
        const { latitude, longitude, accuracy, speed, heading } =
          position.coords;

        const newPosition = {
          lat: latitude,
          lng: longitude,
          accuracy: accuracy,
          speed: speed ? (speed * 3.6).toFixed(1) : null, // Convert to km/h
          heading: heading,
          timestamp: new Date().toLocaleTimeString(),
        };

        setPosition(newPosition);

        // Add to history
        setHistory((prev) => [...prev, newPosition]);

        console.log("Position updated:", newPosition);
      },
      (error) => {
        let message = "";
        switch (error.code) {
          case error.PERMISSION_DENIED:
            message = "Permission denied";
            break;
          case error.POSITION_UNAVAILABLE:
            message = "Position unavailable";
            break;
          case error.TIMEOUT:
            message = "Timeout";
            break;
          default:
            message = "Unknown error";
        }
        setError(message);
      },
      options,
    );

    setWatchId(id);
    setIsTracking(true);
  };

  const stopTracking = () => {
    if (watchId) {
      navigator.geolocation.clearWatch(watchId);
      setWatchId(null);
      setIsTracking(false);
    }
  };

  // Clean up on unmount
  useEffect(() => {
    return () => {
      if (watchId) {
        navigator.geolocation.clearWatch(watchId);
      }
    };
  }, [watchId]);

  const clearHistory = () => {
    setHistory([]);
  };

  return (
    <div style={{ padding: "20px" }}>
      <h2>Real-time Location Tracking</h2>

      <div>
        {!isTracking ? (
          <button
            onClick={startTracking}
            style={{
              padding: "10px 20px",
              backgroundColor: "#28a745",
              color: "white",
              border: "none",
              borderRadius: "4px",
              cursor: "pointer",
            }}
          >
            Start Tracking
          </button>
        ) : (
          <button
            onClick={stopTracking}
            style={{
              padding: "10px 20px",
              backgroundColor: "#dc3545",
              color: "white",
              border: "none",
              borderRadius: "4px",
              cursor: "pointer",
            }}
          >
            Stop Tracking
          </button>
        )}

        <button
          onClick={clearHistory}
          style={{
            marginLeft: "10px",
            padding: "10px 20px",
            backgroundColor: "#6c757d",
            color: "white",
            border: "none",
            borderRadius: "4px",
            cursor: "pointer",
          }}
        >
          Clear History
        </button>
      </div>

      {error && (
        <div style={{ marginTop: "20px", color: "red" }}>Error: {error}</div>
      )}

      {position && (
        <div style={{ marginTop: "20px" }}>
          <h3>📍 Current Position</h3>
          <div
            style={{
              background: "#f0f0f0",
              padding: "15px",
              borderRadius: "8px",
              marginBottom: "20px",
            }}
          >
            <p>
              <strong>Latitude:</strong> {position.lat}
            </p>
            <p>
              <strong>Longitude:</strong> {position.lng}
            </p>
            <p>
              <strong>Accuracy:</strong> ±{Math.round(position.accuracy)} meters
            </p>
            {position.speed && (
              <p>
                <strong>Speed:</strong> {position.speed} km/h
              </p>
            )}
            {position.heading && (
              <p>
                <strong>Heading:</strong> {position.heading}°
              </p>
            )}
            <p>
              <strong>Time:</strong> {position.timestamp}
            </p>
          </div>

          <h3>📍 Location History ({history.length} points)</h3>
          <div
            style={{
              maxHeight: "300px",
              overflow: "auto",
              background: "#f8f9fa",
              padding: "10px",
              borderRadius: "8px",
            }}
          >
            {history
              .slice()
              .reverse()
              .map((loc, index) => (
                <div
                  key={index}
                  style={{
                    padding: "8px",
                    borderBottom: "1px solid #ddd",
                    fontSize: "12px",
                  }}
                >
                  {loc.timestamp} - Lat: {loc.lat.toFixed(6)}, Lng:{" "}
                  {loc.lng.toFixed(6)}
                  {loc.speed && `, Speed: ${loc.speed} km/h`}
                </div>
              ))}
          </div>
        </div>
      )}
    </div>
  );
}
```

---

## Error Handling

### Comprehensive Error Handler

```jsx
import { useState } from "react";

function GeolocationWithErrors() {
  const [location, setLocation] = useState(null);
  const [error, setError] = useState(null);
  const [errorDetails, setErrorDetails] = useState(null);

  const getLocation = () => {
    if (!navigator.geolocation) {
      setError("Browser Not Supported");
      setErrorDetails({
        type: "unsupported",
        message:
          "Your browser does not support geolocation. Please update your browser.",
      });
      return;
    }

    navigator.geolocation.getCurrentPosition(
      (position) => {
        setLocation(position.coords);
        setError(null);
        setErrorDetails(null);
      },
      (err) => {
        // Detailed error handling
        switch (err.code) {
          case err.PERMISSION_DENIED:
            setError("Permission Denied");
            setErrorDetails({
              type: "permission",
              message:
                "You denied location access. Please enable location in browser settings.",
              action: "Enable in browser settings",
              showSettings: true,
            });
            break;

          case err.POSITION_UNAVAILABLE:
            setError("Position Unavailable");
            setErrorDetails({
              type: "unavailable",
              message:
                "Could not determine your position. Check your GPS/WiFi connection.",
              action: "Check your connection",
              showRetry: true,
            });
            break;

          case err.TIMEOUT:
            setError("Request Timeout");
            setErrorDetails({
              type: "timeout",
              message:
                "Location request timed out. Try again in a better coverage area.",
              action: "Try again",
              showRetry: true,
            });
            break;

          default:
            setError("Unknown Error");
            setErrorDetails({
              type: "unknown",
              message: err.message || "An unknown error occurred",
              action: "Refresh and try again",
              showRetry: true,
            });
        }
      },
      {
        enableHighAccuracy: true,
        timeout: 10000,
        maximumAge: 0,
      },
    );
  };

  return (
    <div style={{ padding: "20px" }}>
      <h2>Geolocation with Error Handling</h2>

      <button onClick={getLocation}>Get Location</button>

      {location && (
        <div style={{ color: "green", marginTop: "20px" }}>
          ✅ Location obtained!
          <pre>{JSON.stringify(location, null, 2)}</pre>
        </div>
      )}

      {error && (
        <div
          style={{
            marginTop: "20px",
            padding: "15px",
            backgroundColor: "#fee",
            border: "1px solid #fcc",
            borderRadius: "8px",
          }}
        >
          <h3 style={{ color: "red", margin: "0 0 10px 0" }}>❌ {error}</h3>
          <p>{errorDetails?.message}</p>

          {errorDetails?.showRetry && (
            <button onClick={getLocation} style={{ marginTop: "10px" }}>
              Try Again
            </button>
          )}

          {errorDetails?.showSettings && (
            <button
              onClick={() => {
                alert("Please enable location in your browser settings");
              }}
              style={{ marginTop: "10px", marginLeft: "10px" }}
            >
              How to enable
            </button>
          )}
        </div>
      )}
    </div>
  );
}
```

---

## Custom Geolocation Hook

### Reusable useGeolocation Hook

```jsx
// hooks/useGeolocation.js
import { useState, useEffect, useCallback } from "react";

export function useGeolocation(options = {}) {
  const [location, setLocation] = useState(null);
  const [error, setError] = useState(null);
  const [loading, setLoading] = useState(false);
  const [watchId, setWatchId] = useState(null);
  const [isTracking, setIsTracking] = useState(false);

  const defaultOptions = {
    enableHighAccuracy: true,
    timeout: 10000,
    maximumAge: 0,
  };

  const mergedOptions = { ...defaultOptions, ...options };

  // Get current position (one time)
  const getCurrentPosition = useCallback(() => {
    setLoading(true);
    setError(null);

    if (!navigator.geolocation) {
      setError("Geolocation not supported");
      setLoading(false);
      return Promise.reject("Geolocation not supported");
    }

    return new Promise((resolve, reject) => {
      navigator.geolocation.getCurrentPosition(
        (position) => {
          const { latitude, longitude, accuracy, speed, heading } =
            position.coords;
          const locationData = {
            latitude,
            longitude,
            accuracy,
            speed,
            heading,
          };
          setLocation(locationData);
          setLoading(false);
          resolve(locationData);
        },
        (error) => {
          setError(error.message);
          setLoading(false);
          reject(error);
        },
        mergedOptions,
      );
    });
  }, [mergedOptions]);

  // Start watching position (continuous)
  const startWatching = useCallback(() => {
    if (!navigator.geolocation) {
      setError("Geolocation not supported");
      return;
    }

    const id = navigator.geolocation.watchPosition(
      (position) => {
        const { latitude, longitude, accuracy, speed, heading } =
          position.coords;
        setLocation({ latitude, longitude, accuracy, speed, heading });
        setError(null);
      },
      (error) => {
        setError(error.message);
      },
      mergedOptions,
    );

    setWatchId(id);
    setIsTracking(true);
  }, [mergedOptions]);

  // Stop watching
  const stopWatching = useCallback(() => {
    if (watchId) {
      navigator.geolocation.clearWatch(watchId);
      setWatchId(null);
      setIsTracking(false);
    }
  }, [watchId]);

  // Clean up on unmount
  useEffect(() => {
    return () => {
      if (watchId) {
        navigator.geolocation.clearWatch(watchId);
      }
    };
  }, [watchId]);

  return {
    location,
    error,
    loading,
    isTracking,
    getCurrentPosition,
    startWatching,
    stopWatching,
  };
}

// Usage in component
function MyComponent() {
  const {
    location,
    error,
    loading,
    getCurrentPosition,
    startWatching,
    stopWatching,
    isTracking,
  } = useGeolocation({ enableHighAccuracy: true });

  return (
    <div>
      <button onClick={getCurrentPosition} disabled={loading}>
        Get Location
      </button>

      <button onClick={startWatching}>Start Tracking</button>

      <button onClick={stopWatching}>Stop Tracking</button>

      {loading && <div>Getting location...</div>}
      {error && <div>Error: {error}</div>}
      {location && (
        <div>
          <div>Lat: {location.latitude}</div>
          <div>Lng: {location.longitude}</div>
          {isTracking && <div>📍 Tracking active</div>}
        </div>
      )}
    </div>
  );
}
```

---

## Geolocation with React Leaflet

### Complete Map with User Location

```jsx
import { useState, useEffect } from "react";
import {
  MapContainer,
  TileLayer,
  Marker,
  Popup,
  Circle,
  useMap,
} from "react-leaflet";
import "leaflet/dist/leaflet.css";
import L from "leaflet";

// Fix marker icons
import icon from "leaflet/dist/images/marker-icon.png";
import iconShadow from "leaflet/dist/images/marker-shadow.png";

let DefaultIcon = L.icon({
  iconUrl: icon,
  shadowUrl: iconShadow,
  iconSize: [25, 41],
  iconAnchor: [12, 41],
});
L.Marker.prototype.options.icon = DefaultIcon;

// Custom hook for geolocation
function useGeolocation() {
  const [position, setPosition] = useState(null);
  const [error, setError] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    if (!navigator.geolocation) {
      setError("Geolocation not supported");
      setLoading(false);
      return;
    }

    navigator.geolocation.getCurrentPosition(
      (pos) => {
        setPosition({
          lat: pos.coords.latitude,
          lng: pos.coords.longitude,
          accuracy: pos.coords.accuracy,
        });
        setLoading(false);
      },
      (err) => {
        setError(err.message);
        setLoading(false);
      },
      { enableHighAccuracy: true },
    );
  }, []);

  return { position, error, loading };
}

// Component to center map on user location
function CenterMapOnUser({ position }) {
  const map = useMap();

  useEffect(() => {
    if (position) {
      map.flyTo([position.lat, position.lng], 15, {
        duration: 1.5,
      });
    }
  }, [map, position]);

  return null;
}

function LocationMap() {
  const { position, error, loading } = useGeolocation();
  const [showAccuracy, setShowAccuracy] = useState(true);

  if (loading) {
    return <div style={{ padding: "20px" }}>Getting your location...</div>;
  }

  if (error) {
    return (
      <div style={{ padding: "20px" }}>
        <h3>Error getting location</h3>
        <p>{error}</p>
        <p>Please enable location access and refresh the page.</p>
      </div>
    );
  }

  return (
    <div style={{ height: "100vh" }}>
      <MapContainer
        center={[position.lat, position.lng]}
        zoom={15}
        style={{ height: "100%", width: "100%" }}
      >
        <TileLayer
          url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png"
          attribution="&copy; OpenStreetMap contributors"
        />

        <CenterMapOnUser position={position} />

        {/* User location marker */}
        <Marker position={[position.lat, position.lng]}>
          <Popup>
            <div>
              <strong>You are here!</strong>
              <br />
              Accuracy: ±{Math.round(position.accuracy)} meters
              <br />
              Lat: {position.lat.toFixed(6)}
              <br />
              Lng: {position.lng.toFixed(6)}
            </div>
          </Popup>
        </Marker>

        {/* Accuracy circle */}
        {showAccuracy && (
          <Circle
            center={[position.lat, position.lng]}
            radius={position.accuracy}
            pathOptions={{
              color: "#007bff",
              fillColor: "#007bff",
              fillOpacity: 0.1,
              weight: 2,
            }}
          />
        )}
      </MapContainer>

      {/* Controls */}
      <div
        style={{
          position: "absolute",
          bottom: "20px",
          right: "20px",
          zIndex: 1000,
          background: "white",
          padding: "10px",
          borderRadius: "8px",
          boxShadow: "0 2px 4px rgba(0,0,0,0.2)",
        }}
      >
        <label>
          <input
            type="checkbox"
            checked={showAccuracy}
            onChange={(e) => setShowAccuracy(e.target.checked)}
          />
          Show accuracy circle
        </label>
      </div>
    </div>
  );
}

export default LocationMap;
```

### Live Tracking with Leaflet

```jsx
import { useState, useEffect, useRef } from "react";
import {
  MapContainer,
  TileLayer,
  Marker,
  Polyline,
  useMap,
} from "react-leaflet";
import "leaflet/dist/leaflet.css";
import L from "leaflet";

// Fix marker icons (same as above)

function LiveTrackingMap() {
  const [currentPosition, setCurrentPosition] = useState(null);
  const [path, setPath] = useState([]);
  const [isTracking, setIsTracking] = useState(false);
  const [watchId, setWatchId] = useState(null);
  const mapRef = useRef(null);

  const startTracking = () => {
    if (!navigator.geolocation) {
      alert("Geolocation not supported");
      return;
    }

    const id = navigator.geolocation.watchPosition(
      (position) => {
        const newPos = {
          lat: position.coords.latitude,
          lng: position.coords.longitude,
          accuracy: position.coords.accuracy,
          timestamp: new Date(),
        };

        setCurrentPosition(newPos);
        setPath((prev) => [...prev, [newPos.lat, newPos.lng]]);

        // Auto-center map
        if (mapRef.current) {
          mapRef.current.flyTo([newPos.lat, newPos.lng], 16);
        }
      },
      (error) => {
        console.error("Tracking error:", error);
        alert(`Error: ${error.message}`);
      },
      {
        enableHighAccuracy: true,
        maximumAge: 0,
        timeout: 5000,
      },
    );

    setWatchId(id);
    setIsTracking(true);
  };

  const stopTracking = () => {
    if (watchId) {
      navigator.geolocation.clearWatch(watchId);
      setWatchId(null);
      setIsTracking(false);
    }
  };

  const clearPath = () => {
    setPath([]);
  };

  // Cleanup
  useEffect(() => {
    return () => {
      if (watchId) {
        navigator.geolocation.clearWatch(watchId);
      }
    };
  }, [watchId]);

  return (
    <div style={{ height: "100vh" }}>
      <MapContainer
        center={[51.505, -0.09]}
        zoom={13}
        style={{ height: "100%", width: "100%" }}
        ref={mapRef}
      >
        <TileLayer url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png" />

        {currentPosition && (
          <Marker position={[currentPosition.lat, currentPosition.lng]}>
            <Popup>Current Location</Popup>
          </Marker>
        )}

        {path.length > 1 && (
          <Polyline
            positions={path}
            pathOptions={{
              color: "#007bff",
              weight: 3,
              opacity: 0.7,
            }}
          />
        )}
      </MapContainer>

      {/* Controls */}
      <div
        style={{
          position: "absolute",
          top: "20px",
          right: "20px",
          zIndex: 1000,
          background: "white",
          padding: "15px",
          borderRadius: "8px",
          boxShadow: "0 2px 4px rgba(0,0,0,0.2)",
          minWidth: "200px",
        }}
      >
        <h3>Live Tracking</h3>

        {!isTracking ? (
          <button
            onClick={startTracking}
            style={{ width: "100%", padding: "8px" }}
          >
            Start Tracking
          </button>
        ) : (
          <>
            <button
              onClick={stopTracking}
              style={{ width: "100%", padding: "8px", marginBottom: "8px" }}
            >
              Stop Tracking
            </button>
            <button
              onClick={clearPath}
              style={{ width: "100%", padding: "8px" }}
            >
              Clear Path
            </button>
          </>
        )}

        {currentPosition && (
          <div style={{ marginTop: "10px", fontSize: "12px" }}>
            <div>Lat: {currentPosition.lat.toFixed(6)}</div>
            <div>Lng: {currentPosition.lng.toFixed(6)}</div>
            <div>Points: {path.length}</div>
            <div style={{ color: isTracking ? "green" : "gray" }}>
              Status: {isTracking ? "Tracking..." : "Stopped"}
            </div>
          </div>
        )}
      </div>
    </div>
  );
}
```

---

## Distance Calculation

### Haversine Formula for Distance

```jsx
// utils/distance.js

// Calculate distance between two points (Haversine formula)
export function calculateDistance(lat1, lon1, lat2, lon2) {
  const R = 6371; // Earth's radius in kilometers
  const dLat = toRadians(lat2 - lat1);
  const dLon = toRadians(lon2 - lon1);

  const a =
    Math.sin(dLat / 2) * Math.sin(dLat / 2) +
    Math.cos(toRadians(lat1)) *
      Math.cos(toRadians(lat2)) *
      Math.sin(dLon / 2) *
      Math.sin(dLon / 2);

  const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
  const distance = R * c;

  return distance;
}

function toRadians(degrees) {
  return degrees * (Math.PI / 180);
}

// Format distance for display
export function formatDistance(distanceKm) {
  if (distanceKm < 1) {
    return `${Math.round(distanceKm * 1000)} meters`;
  }
  if (distanceKm < 10) {
    return `${distanceKm.toFixed(1)} km`;
  }
  return `${Math.round(distanceKm)} km`;
}

// Component example
function DistanceCalculator() {
  const [userLocation, setUserLocation] = useState(null);
  const [destination, setDestination] = useState({ lat: 51.505, lng: -0.09 });
  const [distance, setDistance] = useState(null);

  const getUserLocation = () => {
    navigator.geolocation.getCurrentPosition((position) => {
      const loc = {
        lat: position.coords.latitude,
        lng: position.coords.longitude,
      };
      setUserLocation(loc);

      const dist = calculateDistance(
        loc.lat,
        loc.lng,
        destination.lat,
        destination.lng,
      );
      setDistance(dist);
    });
  };

  return (
    <div>
      <button onClick={getUserLocation}>Get Distance</button>

      {distance !== null && (
        <div>
          <h3>Distance to Destination</h3>
          <p>{formatDistance(distance)}</p>
          <p>
            From: {userLocation.lat.toFixed(4)}, {userLocation.lng.toFixed(4)}
          </p>
          <p>
            To: {destination.lat}, {destination.lng}
          </p>
        </div>
      )}
    </div>
  );
}
```

---

## Reverse Geocoding

### Convert Coordinates to Address

```jsx
import { useState } from "react";

function ReverseGeocoding() {
  const [address, setAddress] = useState("");
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);

  // Using OpenStreetMap Nominatim API (free, no API key)
  const getAddressFromCoords = async (lat, lng) => {
    setLoading(true);
    setError(null);

    try {
      const response = await fetch(
        `https://nominatim.openstreetmap.org/reverse?format=json&lat=${lat}&lon=${lng}`,
      );
      const data = await response.json();

      if (data.display_name) {
        setAddress(data.display_name);
      } else {
        setError("No address found");
      }
    } catch (err) {
      setError("Failed to get address");
    } finally {
      setLoading(false);
    }
  };

  const getLocationAndAddress = () => {
    if (!navigator.geolocation) {
      setError("Geolocation not supported");
      return;
    }

    navigator.geolocation.getCurrentPosition(
      async (position) => {
        const { latitude, longitude } = position.coords;
        await getAddressFromCoords(latitude, longitude);
      },
      (err) => {
        setError(err.message);
      },
    );
  };

  return (
    <div style={{ padding: "20px" }}>
      <h2>Reverse Geocoding</h2>
      <p>Get your current address from coordinates</p>

      <button onClick={getLocationAndAddress} disabled={loading}>
        {loading ? "Getting address..." : "Get My Address"}
      </button>

      {address && (
        <div style={{ marginTop: "20px" }}>
          <h3>📍 Your Address</h3>
          <p>{address}</p>
        </div>
      )}

      {error && (
        <div style={{ marginTop: "20px", color: "red" }}>Error: {error}</div>
      )}
    </div>
  );
}
```

### Forward Geocoding (Address to Coordinates)

```jsx
function ForwardGeocoding() {
  const [searchTerm, setSearchTerm] = useState("");
  const [coordinates, setCoordinates] = useState(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);

  const getCoordinatesFromAddress = async (address) => {
    setLoading(true);
    setError(null);

    try {
      const response = await fetch(
        `https://nominatim.openstreetmap.org/search?format=json&q=${encodeURIComponent(address)}`,
      );
      const data = await response.json();

      if (data && data.length > 0) {
        setCoordinates({
          lat: parseFloat(data[0].lat),
          lng: parseFloat(data[0].lon),
          displayName: data[0].display_name,
        });
      } else {
        setError("Location not found");
      }
    } catch (err) {
      setError("Failed to search location");
    } finally {
      setLoading(false);
    }
  };

  const handleSearch = (e) => {
    e.preventDefault();
    if (searchTerm.trim()) {
      getCoordinatesFromAddress(searchTerm);
    }
  };

  return (
    <div style={{ padding: "20px" }}>
      <h2>Forward Geocoding</h2>
      <p>Find coordinates for an address</p>

      <form onSubmit={handleSearch}>
        <input
          type="text"
          value={searchTerm}
          onChange={(e) => setSearchTerm(e.target.value)}
          placeholder="Enter address..."
          style={{ padding: "8px", width: "300px", marginRight: "10px" }}
        />
        <button type="submit" disabled={loading}>
          {loading ? "Searching..." : "Search"}
        </button>
      </form>

      {coordinates && (
        <div style={{ marginTop: "20px" }}>
          <h3>📍 Coordinates</h3>
          <p>
            <strong>Latitude:</strong> {coordinates.lat}
          </p>
          <p>
            <strong>Longitude:</strong> {coordinates.lng}
          </p>
          <p>
            <strong>Address:</strong> {coordinates.displayName}
          </p>

          <a
            href={`https://www.google.com/maps?q=${coordinates.lat},${coordinates.lng}`}
            target="_blank"
            rel="noopener noreferrer"
          >
            Open in Google Maps
          </a>
        </div>
      )}

      {error && (
        <div style={{ marginTop: "20px", color: "red" }}>Error: {error}</div>
      )}
    </div>
  );
}
```

---

## Complete Examples

### Example 1: Nearby Places Finder

```jsx
import { useState } from "react";

function NearbyPlaces() {
  const [userLocation, setUserLocation] = useState(null);
  const [places, setPlaces] = useState([]);
  const [loading, setLoading] = useState(false);
  const [category, setCategory] = useState("restaurant");

  const categories = {
    restaurant: "Restaurants",
    cafe: "Cafes",
    bar: "Bars",
    park: "Parks",
    museum: "Museums",
    hotel: "Hotels",
  };

  const calculateDistance = (lat1, lon1, lat2, lon2) => {
    const R = 6371;
    const dLat = ((lat2 - lat1) * Math.PI) / 180;
    const dLon = ((lon2 - lon1) * Math.PI) / 180;
    const a =
      Math.sin(dLat / 2) * Math.sin(dLat / 2) +
      Math.cos((lat1 * Math.PI) / 180) *
        Math.cos((lat2 * Math.PI) / 180) *
        Math.sin(dLon / 2) *
        Math.sin(dLon / 2);
    const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
    return R * c;
  };

  const getUserLocation = () => {
    if (!navigator.geolocation) {
      alert("Geolocation not supported");
      return;
    }

    setLoading(true);

    navigator.geolocation.getCurrentPosition(
      async (position) => {
        const location = {
          lat: position.coords.latitude,
          lng: position.coords.longitude,
        };
        setUserLocation(location);

        // Search for nearby places
        await searchNearbyPlaces(location);
      },
      (error) => {
        alert(`Error: ${error.message}`);
        setLoading(false);
      },
    );
  };

  const searchNearbyPlaces = async (location) => {
    // Using Overpass API for OpenStreetMap data
    const query = `
      [out:json];
      (
        node["amenity"="${category}"](around:1000,${location.lat},${location.lng});
        way["amenity"="${category}"](around:1000,${location.lat},${location.lng});
      );
      out body;
    `;

    try {
      const response = await fetch("https://overpass-api.de/api/interpreter", {
        method: "POST",
        body: query,
      });
      const data = await response.json();

      const formattedPlaces = data.elements.map((place) => ({
        id: place.id,
        name: place.tags?.name || "Unnamed",
        type: place.tags?.amenity,
        lat: place.lat || place.center?.lat,
        lng: place.lon || place.center?.lon,
        distance: calculateDistance(
          location.lat,
          location.lng,
          place.lat || place.center?.lat,
          place.lon || place.center?.lon,
        ),
      }));

      // Sort by distance
      formattedPlaces.sort((a, b) => a.distance - b.distance);
      setPlaces(formattedPlaces.slice(0, 20));
    } catch (error) {
      console.error("Error fetching places:", error);
      alert("Failed to find nearby places");
    } finally {
      setLoading(false);
    }
  };

  return (
    <div style={{ padding: "20px", maxWidth: "600px", margin: "0 auto" }}>
      <h1>Nearby Places Finder</h1>

      <div style={{ marginBottom: "20px" }}>
        <select
          value={category}
          onChange={(e) => setCategory(e.target.value)}
          style={{ padding: "8px", marginRight: "10px" }}
        >
          {Object.entries(categories).map(([value, label]) => (
            <option key={value} value={value}>
              {label}
            </option>
          ))}
        </select>

        <button
          onClick={getUserLocation}
          disabled={loading}
          style={{ padding: "8px 16px" }}
        >
          {loading ? "Searching..." : `Find Nearby ${categories[category]}`}
        </button>
      </div>

      {userLocation && (
        <div
          style={{
            marginBottom: "20px",
            padding: "10px",
            background: "#e3f2fd",
            borderRadius: "8px",
          }}
        >
          <strong>📍 Your Location:</strong>
          <br />
          Lat: {userLocation.lat.toFixed(6)}
          <br />
          Lng: {userLocation.lng.toFixed(6)}
        </div>
      )}

      {places.length > 0 && (
        <div>
          <h3>
            Nearby {categories[category]} ({places.length} found)
          </h3>
          {places.map((place) => (
            <div
              key={place.id}
              style={{
                padding: "12px",
                marginBottom: "10px",
                border: "1px solid #ddd",
                borderRadius: "8px",
                background: "white",
              }}
            >
              <div style={{ fontWeight: "bold" }}>{place.name}</div>
              <div style={{ fontSize: "14px", color: "#666" }}>
                Distance:{" "}
                {place.distance < 1
                  ? `${Math.round(place.distance * 1000)} meters`
                  : `${place.distance.toFixed(1)} km`}
              </div>
              <a
                href={`https://www.google.com/maps?q=${place.lat},${place.lng}`}
                target="_blank"
                rel="noopener noreferrer"
                style={{ fontSize: "12px" }}
              >
                Open in Maps →
              </a>
            </div>
          ))}
        </div>
      )}

      {!loading && places.length === 0 && userLocation && (
        <div style={{ textAlign: "center", color: "#666" }}>
          No {categories[category]} found nearby
        </div>
      )}
    </div>
  );
}
```

### Example 2: Distance Tracker

```jsx
import { useState, useEffect, useRef } from "react";

function DistanceTracker() {
  const [isTracking, setIsTracking] = useState(false);
  const [watchId, setWatchId] = useState(null);
  const [positions, setPositions] = useState([]);
  const [totalDistance, setTotalDistance] = useState(0);
  const [startTime, setStartTime] = useState(null);
  const [currentSpeed, setCurrentSpeed] = useState(null);

  const calculateDistance = (lat1, lon1, lat2, lon2) => {
    const R = 6371;
    const dLat = ((lat2 - lat1) * Math.PI) / 180;
    const dLon = ((lon2 - lon1) * Math.PI) / 180;
    const a =
      Math.sin(dLat / 2) * Math.sin(dLat / 2) +
      Math.cos((lat1 * Math.PI) / 180) *
        Math.cos((lat2 * Math.PI) / 180) *
        Math.sin(dLon / 2) *
        Math.sin(dLon / 2);
    const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
    return R * c;
  };

  const startTracking = () => {
    if (!navigator.geolocation) {
      alert("Geolocation not supported");
      return;
    }

    setPositions([]);
    setTotalDistance(0);
    setStartTime(Date.now());

    const id = navigator.geolocation.watchPosition(
      (position) => {
        const newPos = {
          lat: position.coords.latitude,
          lng: position.coords.longitude,
          timestamp: Date.now(),
          speed: position.coords.speed,
        };

        setCurrentSpeed(position.coords.speed);

        setPositions((prev) => {
          const updated = [...prev, newPos];

          // Calculate distance if we have at least 2 points
          if (updated.length >= 2) {
            const lastTwo = updated.slice(-2);
            const dist = calculateDistance(
              lastTwo[0].lat,
              lastTwo[0].lng,
              lastTwo[1].lat,
              lastTwo[1].lng,
            );
            setTotalDistance((prevDist) => prevDist + dist);
          }

          return updated;
        });
      },
      (error) => {
        console.error("Tracking error:", error);
        alert(`Error: ${error.message}`);
      },
      {
        enableHighAccuracy: true,
        maximumAge: 0,
        timeout: 5000,
      },
    );

    setWatchId(id);
    setIsTracking(true);
  };

  const stopTracking = () => {
    if (watchId) {
      navigator.geolocation.clearWatch(watchId);
      setWatchId(null);
      setIsTracking(false);
    }
  };

  const formatTime = (ms) => {
    const seconds = Math.floor(ms / 1000);
    const minutes = Math.floor(seconds / 60);
    const hours = Math.floor(minutes / 60);
    return `${hours}h ${minutes % 60}m ${seconds % 60}s`;
  };

  const formatSpeed = (speedMs) => {
    if (!speedMs) return "0";
    const speedKmh = speedMs * 3.6;
    return speedKmh.toFixed(1);
  };

  // Cleanup
  useEffect(() => {
    return () => {
      if (watchId) {
        navigator.geolocation.clearWatch(watchId);
      }
    };
  }, [watchId]);

  const elapsedTime = startTime ? Date.now() - startTime : 0;
  const avgSpeed =
    totalDistance > 0 && elapsedTime > 0
      ? (totalDistance / (elapsedTime / 3600000)).toFixed(1)
      : 0;

  return (
    <div style={{ padding: "20px", maxWidth: "500px", margin: "0 auto" }}>
      <h1>🏃 Distance Tracker</h1>

      <div
        style={{
          textAlign: "center",
          padding: "20px",
          background: "#f0f0f0",
          borderRadius: "10px",
          marginBottom: "20px",
        }}
      >
        <div style={{ fontSize: "48px", fontWeight: "bold" }}>
          {totalDistance < 1
            ? `${Math.round(totalDistance * 1000)} m`
            : `${totalDistance.toFixed(2)} km`}
        </div>
        <div style={{ color: "#666" }}>Total Distance</div>
      </div>

      <div
        style={{
          display: "grid",
          gridTemplateColumns: "1fr 1fr",
          gap: "10px",
          marginBottom: "20px",
        }}
      >
        <div
          style={{
            padding: "10px",
            background: "#e3f2fd",
            borderRadius: "8px",
            textAlign: "center",
          }}
        >
          <div style={{ fontSize: "20px", fontWeight: "bold" }}>
            {formatTime(elapsedTime)}
          </div>
          <div style={{ fontSize: "12px", color: "#666" }}>Time</div>
        </div>

        <div
          style={{
            padding: "10px",
            background: "#e3f2fd",
            borderRadius: "8px",
            textAlign: "center",
          }}
        >
          <div style={{ fontSize: "20px", fontWeight: "bold" }}>
            {formatSpeed(currentSpeed)} km/h
          </div>
          <div style={{ fontSize: "12px", color: "#666" }}>Current Speed</div>
        </div>

        <div
          style={{
            padding: "10px",
            background: "#e3f2fd",
            borderRadius: "8px",
            textAlign: "center",
          }}
        >
          <div style={{ fontSize: "20px", fontWeight: "bold" }}>
            {avgSpeed} km/h
          </div>
          <div style={{ fontSize: "12px", color: "#666" }}>Average Speed</div>
        </div>

        <div
          style={{
            padding: "10px",
            background: "#e3f2fd",
            borderRadius: "8px",
            textAlign: "center",
          }}
        >
          <div style={{ fontSize: "20px", fontWeight: "bold" }}>
            {positions.length}
          </div>
          <div style={{ fontSize: "12px", color: "#666" }}>Points Recorded</div>
        </div>
      </div>

      <div style={{ display: "flex", gap: "10px" }}>
        {!isTracking ? (
          <button
            onClick={startTracking}
            style={{
              flex: 1,
              padding: "12px",
              backgroundColor: "#28a745",
              color: "white",
              border: "none",
              borderRadius: "8px",
              fontSize: "16px",
              cursor: "pointer",
            }}
          >
            Start Tracking
          </button>
        ) : (
          <button
            onClick={stopTracking}
            style={{
              flex: 1,
              padding: "12px",
              backgroundColor: "#dc3545",
              color: "white",
              border: "none",
              borderRadius: "8px",
              fontSize: "16px",
              cursor: "pointer",
            }}
          >
            Stop Tracking
          </button>
        )}
      </div>

      {isTracking && (
        <div
          style={{ marginTop: "20px", textAlign: "center", color: "#28a745" }}
        >
          🔴 Tracking active...
        </div>
      )}

      {positions.length > 0 && (
        <div style={{ marginTop: "20px" }}>
          <details>
            <summary
              style={{
                cursor: "pointer",
                padding: "10px",
                background: "#f8f9fa",
                borderRadius: "8px",
              }}
            >
              Location History ({positions.length} points)
            </summary>
            <div
              style={{
                maxHeight: "300px",
                overflow: "auto",
                marginTop: "10px",
              }}
            >
              {positions
                .slice()
                .reverse()
                .map((pos, idx) => (
                  <div
                    key={idx}
                    style={{
                      padding: "8px",
                      borderBottom: "1px solid #eee",
                      fontSize: "12px",
                    }}
                  >
                    {new Date(pos.timestamp).toLocaleTimeString()} - Lat:{" "}
                    {pos.lat.toFixed(6)}, Lng: {pos.lng.toFixed(6)}
                    {pos.speed && `, Speed: ${formatSpeed(pos.speed)} km/h`}
                  </div>
                ))}
            </div>
          </details>
        </div>
      )}
    </div>
  );
}
```

---

## API Reference

### Navigator.geolocation Methods

| Method               | Description                 | Returns          |
| -------------------- | --------------------------- | ---------------- |
| getCurrentPosition() | Get position once           | void (async)     |
| watchPosition()      | Track position continuously | watchId (number) |
| clearWatch()         | Stop tracking               | void             |

### Position Object Properties

| Property         | Description          | Example       |
| ---------------- | -------------------- | ------------- |
| coords.latitude  | Latitude in degrees  | 51.505        |
| coords.longitude | Longitude in degrees | -0.09         |
| coords.accuracy  | Accuracy in meters   | 10            |
| coords.altitude  | Altitude in meters   | 100           |
| coords.speed     | Speed in m/s         | 5.2           |
| coords.heading   | Direction in degrees | 90            |
| timestamp        | Time of position     | 1634567890123 |

### Error Codes

| Code | Name                 | Description            |
| ---- | -------------------- | ---------------------- |
| 1    | PERMISSION_DENIED    | User denied permission |
| 2    | POSITION_UNAVAILABLE | Position unavailable   |
| 3    | TIMEOUT              | Request timed out      |

## Troubleshooting

Problem 1: Geolocation not working

Solutions:

- Use HTTPS (or localhost)
- Check browser permissions
- Ensure GPS is enabled on device

Problem 2: Inaccurate location

Solutions:

- Enable high accuracy
- Move to open area (for GPS)
- Check WiFi/network connection

Problem 3: Battery drain

Solutions:

- Stop tracking when not needed
- Reduce update frequency
- Use lower accuracy when possible

## Useful Resources

- MDN Geolocation API: https://developer.mozilla.org/en-US/docs/Web/API/Geolocation_API
- Leaflet.js: https://leafletjs.com/
- OpenStreetMap Nominatim: https://nominatim.openstreetmap.org/
- Overpass API: https://overpass-api.de/

---

Created for beginners learning geolocation in React
