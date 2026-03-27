# weather-cache

A lightweight weather caching system for Linux. Fetches current conditions and forecasts from [Open-Meteo](https://open-meteo.com/) (with [wttr.in](https://wttr.in/) fallback) and optionally weather alerts from [Environment Canada](https://api.weather.gc.ca/). Includes a [Waybar](https://github.com/Alexays/Waybar) widget for displaying weather in your status bar.

## Features

- Current temperature, feels-like, wind, humidity, and condition
- 3-day forecast
- Environment Canada weather alerts (optional, for Canadian locations)
- Waybar custom module with emoji icons and detailed tooltip
- Runs on a systemd timer (every 15 minutes)
- Fallback data source if primary fails
- No API keys required — all sources are free and public

## Dependencies

- `bash`, `curl`, `python3`
- `systemd` (for automatic updates)
- [Waybar](https://github.com/Alexays/Waybar) (optional, for the status bar widget)

## Install

```bash
# Copy scripts
cp weather-cache waybar-weather ~/.local/bin/
chmod +x ~/.local/bin/weather-cache ~/.local/bin/waybar-weather

# Create config
mkdir -p ~/.config/weather-cache
cp weather-cache.conf.example ~/.config/weather-cache/weather-cache.conf
# Edit the config with your location:
$EDITOR ~/.config/weather-cache/weather-cache.conf

# Install and start the systemd timer
cp weather-cache.service weather-cache.timer ~/.config/systemd/user/
systemctl --user daemon-reload
systemctl --user enable --now weather-cache.timer

# Optional: install Claude Code /weather skill globally
mkdir -p ~/.claude/skills/weather
cp .claude/skills/weather/SKILL.md ~/.claude/skills/weather/SKILL.md
```

## Configuration

Edit `~/.config/weather-cache/weather-cache.conf`:

| Variable | Required | Description |
|----------|----------|-------------|
| `LATITUDE` | Yes | Location latitude (e.g. `43.65`) |
| `LONGITUDE` | Yes | Location longitude (e.g. `-79.38`) |
| `LOCATION_NAME` | Yes | City name for wttr.in fallback (e.g. `Toronto Ontario`) |
| `UNITS` | No | `metric` (°C, km/h) or `imperial` (°F, mph). Default: `metric` |
| `TIMEZONE` | No | Timezone (auto-detected from `/etc/timezone` if not set) |
| `CACHE_DIR` | No | Cache directory (default: `~/.cache/weather`) |
| `ALERT_BBOX` | No | Bounding box for Environment Canada alerts (`lon_min,lat_min,lon_max,lat_max`) |
| `ALERT_FILTER` | No | Filter alerts by area name (defaults to `LOCATION_NAME`) |

You can also override the config path with `WEATHER_CACHE_CONF=/path/to/conf`.

## Waybar integration

Add a custom module to your Waybar config:

```json
"custom/weather": {
    "exec": "~/.local/bin/waybar-weather",
    "return-type": "json",
    "interval": 300
}
```

The widget shows an emoji + temperature (e.g. `☀️ 22°C`) with a detailed tooltip on hover including feels-like, wind, humidity, forecast, and any active alerts.

## Claude Code integration

This repo includes a `/weather` [skill](https://docs.anthropic.com/en/docs/claude-code/skills) for [Claude Code](https://docs.anthropic.com/en/docs/claude-code/overview). When working in a project that uses weather-cache, Claude can answer weather questions using your cached data. The skill is available automatically when working in this repo. To make it available globally, see the optional step in the install block above.

## Cache format

Weather data is cached as plain text in `~/.cache/weather/`:

- `conditions.txt` — current conditions and 3-day forecast
- `alerts.txt` — weather alerts (if `ALERT_BBOX` is configured)
- `weather-cache.log` — error log (auto-rotated)

This makes it easy to read from other tools (scripts, voice assistants, etc.).

## Data sources

| Source | Used for | Fallback |
|--------|----------|----------|
| [Open-Meteo](https://open-meteo.com/) | Current conditions + forecast | Primary |
| [wttr.in](https://wttr.in/) | Current conditions + forecast | If Open-Meteo fails |
| [Environment Canada GeoMet](https://api.weather.gc.ca/) | Weather alerts | Optional (Canada only) |

All sources are free and require no API keys.

## License

[MIT](LICENSE)
