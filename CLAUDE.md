# weather-cache

A weather caching system with two scripts and a systemd timer.

## Project structure

- `weather-cache` — main script that fetches and caches weather data
- `waybar-weather` — formats cached data as JSON for Waybar custom module
- `weather-cache.conf.example` — example configuration file
- `weather-cache.service` — systemd oneshot service
- `weather-cache.timer` — systemd timer (every 15 minutes)

## How it works

1. `weather-cache` loads config from `~/.config/weather-cache/weather-cache.conf`
2. Fetches current conditions from Open-Meteo API (falls back to wttr.in)
3. Optionally fetches Environment Canada alerts if `ALERT_BBOX` is configured
4. Writes plain-text cache files to `~/.cache/weather/`
5. `waybar-weather` reads the cache and outputs JSON for Waybar

## Key details

- All location data is in the user's config file, not in the scripts
- Config path can be overridden with `WEATHER_CACHE_CONF` env var
- Cache dir can be overridden with `CACHE_DIR` in config or `WEATHER_CACHE_DIR` env var (for waybar-weather)
- Log file auto-rotates at 1000 lines (keeps last 500)
- The alerts feature is Canada-specific (Environment Canada API) and optional

## Guidelines

- Keep cache format as plain text — other tools depend on parsing it
- Don't add API key requirements — all sources must remain free and public
- The systemd units use `%h` for home directory portability
