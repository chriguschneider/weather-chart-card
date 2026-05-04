# Weather Chart Card (chrigu fork)

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE.md)
[![hacs](https://img.shields.io/badge/HACS-Custom-orange.svg)](https://hacs.xyz/)

A Lovelace card that renders a daily or hourly forecast from a Home
Assistant `weather.*` entity in the same per-day chart layout as the
sibling [`chriguschneider/weather-station-card`](https://github.com/chriguschneider/weather-station-card),
so a forecast chart and a station-history chart can sit next to each
other panel-for-panel:

![Station and forecast side by side](images/sibling-comparison.png)

> Left: [`weather-station-card`](https://github.com/chriguschneider/weather-station-card) — past 7 days, driven by sensor history.
> Right: this card — daily forecast, driven by a `weather.*` entity.

## What this card does

This is a fork of [`mlamberts78/weather-chart-card`](https://github.com/mlamberts78/weather-chart-card)
with the chart visuals from `weather-station-card` ported across:

- Two-line tick labels: weekday on top in primary text colour,
  `DD/MM` underneath in `--secondary-text-color`.
- Today's column framed with thicker vertical lines on both sides; works
  wherever today sits in the series (leftmost for a future-only forecast).
- `TempAxis` / `PrecipAxis` get a 2 px outer border in
  `--secondary-text-color` so the chart edges read as a continuous frame
  with the today lines.
- Today's weekday and high/low temperature labels render bold (in both
  `style1` and `style2`).

All upstream functionality (`weather.*` subscription, daily / hourly
forecasts, condition icons, wind row, visual editor, locales) is kept.

## Sibling card for past data

The same chart layout for *past* sensor data is available as
[`chriguschneider/weather-station-card`](https://github.com/chriguschneider/weather-station-card)
— shows historical readings of an on-site weather station
(`recorder/statistics_during_period`) instead of a forecast, plus a live
"now" panel driven by current sensor states.

## Installation

### HACS (Custom Repository)

1. In HACS, go to **Frontend → ⋮ → Custom repositories**.
2. Add `https://github.com/chriguschneider/weather-chart-card` with
   category **Dashboard**.
3. Click **Install** on the *Weather Chart Card* entry that appears in
   the Frontend list.
4. Hard-refresh your browser (Ctrl-F5 or equivalent) so the new
   resource loads.
5. Add the card via Lovelace ("Add Card → Custom: Weather Chart Card")
   or paste the YAML below.

### Manual

1. Download `weather-chart-card.js` from the [latest release](https://github.com/chriguschneider/weather-chart-card/releases/latest).
2. Copy it to `<config>/www/community/weather-chart-card/`.
3. In Home Assistant, go to **Settings → Dashboards → Resources** and
   add `/local/community/weather-chart-card/weather-chart-card.js` as a
   JavaScript module.
4. Hard-refresh and add the card.

## Minimal working config

```yaml
type: custom:weather-chart-card
entity: weather.home
forecast:
  type: daily
  number_of_forecasts: 7
  style: style2
```

## Configuration reference

### Card-level

| Key | Type | Default | Description |
| --- | --- | --- | --- |
| `type` | string | — | Always `custom:weather-chart-card`. |
| `entity` | string | **required** | A `weather.*` entity. |
| `title` | string | _none_ | Card header. Omit for header-less. |
| `locale` | string | HA's selected language | Override locale. |
| `use_12hour_format` | bool | `false` | 12-hour clock in main panel. |
| `autoscroll` | bool | `false` | Hourly redraw, hides past forecast columns. |

### Custom-sensor overrides

Each takes a sensor `entity_id` to override the matching attribute on
the configured `weather.*` entity.

| Key | Replaces |
| --- | --- |
| `temp` | Temperature |
| `press` | Pressure |
| `humid` | Humidity |
| `uv` | UV index |
| `winddir` | Wind bearing (in degrees) |
| `windspeed` | Wind speed |
| `feels_like` | Apparent temperature |
| `dew_point` | Dew point |
| `wind_gust_speed` | Gust speed |
| `visibility` | Visibility |
| `description` | Weather description |

### Display toggles

| Key | Type | Default | Description |
| --- | --- | --- | --- |
| `show_main` | bool | `true` | Main panel (icon + temperature + condition). |
| `show_temperature` | bool | `true` | Current temperature in main panel. |
| `show_current_condition` | bool | `true` | Condition text under temperature. |
| `show_attributes` | bool | `true` | Humidity / pressure / wind / sun row. |
| `show_humidity` | bool | `true` | Humidity attribute. |
| `show_pressure` | bool | `true` | Pressure attribute. |
| `show_wind_direction` | bool | `true` | Wind-direction arrow. |
| `show_wind_speed` | bool | `true` | Wind-speed value. |
| `show_sun` | bool | `true` | Sunrise / sunset row. |
| `show_time` | bool | `false` | Live clock. |
| `show_time_seconds` | bool | `false` | Include seconds in clock. |
| `show_day` | bool | `false` | Day-of-week label (requires `show_time`). |
| `show_date` | bool | `false` | Date label (requires `show_time`). |
| `show_feels_like` | bool | `false` | Apparent temperature. |
| `show_dew_point` | bool | `false` | Dew-point attribute. |
| `show_wind_gust_speed` | bool | `false` | Gust-speed value. |
| `show_visibility` | bool | `false` | Visibility attribute. |
| `show_description` | bool | `false` | Weather description text. |
| `show_last_changed` | bool | `false` | "Updated N min ago" footer. |

### Sizing

| Key | Type | Default | Description |
| --- | --- | --- | --- |
| `current_temp_size` | number (px) | `28` | Main-panel temperature. |
| `time_size` | number (px) | `26` | Clock font size. |
| `day_date_size` | number (px) | `15` | Day / date label. |
| `icons_size` | number (px) | `25` | Forecast-row icon size. |

### Icons

| Key | Type | Default | Description |
| --- | --- | --- | --- |
| `icon_style` | `'style1' \| 'style2'` | `'style1'` | Bundled icon set. |
| `animated_icons` | bool | `false` | Use animated SVGs. |
| `icons` | string (URL) | _none_ | Override icon base path (custom set). |

Custom icons must be SVG with the names listed in
[`src/const.js`](src/const.js).

### Units

| Key | Values | Description |
| --- | --- | --- |
| `units.pressure` | `'hPa' \| 'mmHg' \| 'inHg'` | Display unit; auto-converts. |
| `units.speed` | `'m/s' \| 'km/h' \| 'mph' \| 'Bft'` | Display unit; auto-converts. |

### Forecast / chart

| Key | Type | Default | Description |
| --- | --- | --- | --- |
| `forecast.type` | `'daily' \| 'hourly'` | `'daily'` | Aggregation. |
| `forecast.style` | `'style1' \| 'style2'` | `'style1'` | Chart visual style. |
| `forecast.number_of_forecasts` | integer | `0` (auto) | Override forecast columns; `0` = entity default. |
| `forecast.precipitation_type` | `'rainfall' \| 'probability'` | `'rainfall'` | Series for the precipitation bars. |
| `forecast.show_probability` | bool | `false` | Show probability label alongside rainfall. |
| `forecast.condition_icons` | bool | `true` | Icons row above the chart. |
| `forecast.show_wind_forecast` | bool | `true` | Wind row under the chart. |
| `forecast.round_temp` | bool | `false` | Round temperature labels to integers. |
| `forecast.disable_animation` | bool | `false` | Disable chart redraw animation. |
| `forecast.labels_font_size` | number | `11` | Axis-tick / datalabel font size (px). |
| `forecast.precip_bar_size` | number (%) | `100` | Width of precipitation bars (1–100). |
| `forecast.chart_height` | number (px) | `180` | Chart canvas height. |
| `forecast.temperature1_color` | CSS colour | `rgba(255, 152, 0, 1.0)` | High-temperature curve. |
| `forecast.temperature2_color` | CSS colour | `rgba(68, 115, 158, 1.0)` | Low-temperature curve. |
| `forecast.precipitation_color` | CSS colour | `rgba(132, 209, 253, 1.0)` | Precipitation bars. |
| `forecast.chart_datetime_color` | CSS colour or `'auto'` | _none_ | X-axis weekday / date colour. |
| `forecast.chart_text_color` | CSS colour or `'auto'` | _none_ | All other chart text colour. |

## Supported languages

`bg`, `ca`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `el`, `hu`, `it`,
`lt`, `no`, `pl`, `pt`, `ro`, `ru`, `sk`, `es`, `sv`, `uk`, `ko`.

## Attribution & licence

Forked from [`mlamberts78/weather-chart-card`](https://github.com/mlamberts78/weather-chart-card)
v1.0.1 (October 2024). Upstream is no longer maintained. What's new
here is the chart styling adopted from
[`chriguschneider/weather-station-card`](https://github.com/chriguschneider/weather-station-card)
(`dailyTickLabels` and `todayBorder` plugins, axis borders, today-bold
datalabel callbacks) and the `cdn.jsdelivr.net` icon path pointed at
this fork.

Released under the MIT licence — same as upstream. See [LICENSE.md](LICENSE.md).
