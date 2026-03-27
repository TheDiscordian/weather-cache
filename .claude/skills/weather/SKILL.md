---
name: weather
description: Check current weather conditions, forecast, and active weather alerts from cached data. Use when the user asks about weather, temperature, or forecasts.
---

## Current Conditions

!`cat ~/.cache/weather/conditions.txt 2>/dev/null || echo "No weather data cached. Run weather-cache to populate."`

## Weather Alerts

!`cat ~/.cache/weather/alerts.txt 2>/dev/null || echo "No alert data available."`

## Instructions

Using the weather data above, respond to the user's question. Guidelines:

- The data is cached and refreshed every 15 minutes via a systemd timer — do not attempt to re-fetch
- The cached data already reflects the user's configured units (°C or °F) — use whichever unit appears in the data
- If the user asks about a specific day, check the forecast section
- If there are active weather alerts, always mention them prominently
- If the cache is missing or stale, inform the user that weather data is unavailable
- Keep responses concise — lead with the answer (e.g., "Currently -6°C and clear")
- If asked about weather for a different location, explain that this system only tracks the configured location
