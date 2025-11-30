# 🌈 Home Assistant Minimalist Widget

> A minimalist Android widget for Home Assistant displaying your daily summary with contextual messages based on time of day.

<p>
  <a href="https://www.home-assistant.io/" target="_blank">
    <img src="assets/badges/home-assistant.png" height="28" alt="Home Assistant Compatible" />
  </a>
  <a href="https://buymeacoffee.com/tben" target="_blank">
    <img src="assets/badges/buy-me-a-coffee.png" height="28" alt="Buy Me a Coffee" />
  </a>
</p>

<p align="left">
  <img src="assets/screenshots/widget-cover.gif" width="80%" />
</p>

**Inspired by [Joi Planner](https://joi.software/)** - An app that I wanted to recreate with my own Home Assistant data!

## Features

- ☀️ **Contextual messages** - Different messages based on time of day (morning, afternoon, evening, night)
- ✅ **Smart tasks** - Automatically filters tasks for today (includes overdue and no-date tasks)
- 📅 **Remaining events** - Only counts upcoming events in the day
- 🌙 **Sleep duration** - Display sleep time via smartphone integration
- 🎂 **Birthdays** - Automatic detection of today's birthdays
- 📊 **Daily statistics** - Step count and power consumption
- 🎨 **Customizable** - Emojis, colors, and data fully configurable

## Display Examples

<table style="border: none;">
<tr>
<td width="33%" align="center" valign="top" style="border: none;">
<img src="assets/screenshots/widget_morning.png" width="100%" />
<br/>
Morning
</td>
<td width="33%" align="center" valign="top" style="border: none;">
<img src="assets/screenshots/widget_afternoon.png" width="100%" />
<br/>
Afternoon
</td>
<td width="33%" align="center" valign="top" style="border: none;">
<img src="assets/screenshots/widget_evening.png" width="100%" />
<br/>
Evening
</td>
</tr>
</table>

## Quick Installation

### Prerequisites

- Home Assistant installed
- Home Assistant app for Android
- Configured integrations:
  - 📅 Calendar (Google Calendar, CalDAV, etc.)
  - ✅ Todo (Google Tasks, Todoist, etc.)
  - 📱 Companion App for smartphone (sleep, steps)

### 1. Create sensors

Copy the content of [`config/configuration_example.yaml`](config/configuration_example.yaml) into your `configuration.yaml`.

**Required sensors:**
- `sensor.tasks_today` - Counts tasks for today
- `sensor.events_today` - Counts remaining events

### 2. Restart Home Assistant

Restart HA or reload templates:
- **Developer Tools** > **YAML** > **Reload template entities**

### 3. Add the widget

<table>
<tr>
<td width="33%" valign="top">
<p align="center"><img src="assets/screenshots/step1_widget_list.png" width="100%" /></p>
<b>1. Widget List</b>
<br/>
Long press on home screen and select Home Assistant
</td>
<td width="33%" valign="top">
<p align="center"><img src="assets/screenshots/step2_template_widget.png" width="100%" /></p>
<b>2. Choose Template</b>
<br/>
Select the Template widget
</td>
<td width="33%" valign="top">
<p align="center"><img src="assets/screenshots/step3_widget_config.png" width="100%" /></p>
<b>3. Configure & Save</b>
<br/>
Paste <a href="config/widget_template_en.jinja2">code</a>, set size to <b>28</b>, <b>Transparent</b> theme, <b>White</b> text
</td>
</tr>
</table>

## Documentation

- 📖 [Detailed installation](docs/installation.md)
- 🎨 [Customization guide](docs/customization.md)
- 🔧 [Troubleshooting](docs/troubleshooting.md)
- 🚀 [Advanced features](docs/advanced_features.md)

## Project Structure

```
ha-minimalist-widget/
├── README.md                       # This file
├── LICENSE                         # MIT License
├── CHANGELOG.md                    # Version history
│
├── config/
│   ├── configuration_example.yaml # Complete configuration for HA
│   ├── widget_template_en.jinja2  # Android widget code (English)
│   └── widget_template_fr.jinja2  # Android widget code (French)
│
├── docs/
│   ├── installation.md            # Detailed installation guide
│   ├── customization.md           # Advanced customization
│   ├── troubleshooting.md         # Problem solving
│   └── advanced_features.md       # Advanced features
│
└── assets/
    ├── screenshots/               # Widget screenshots
    │   ├── widget_morning.png
    │   ├── widget_afternoon.png
    │   ├── widget_evening.png
    │   └── widget_night.png
    └── demo.gif                   # Demo animation
```

## Customization

The widget is fully customizable! Modify the **USER PARAMETERS** section:

**Available languages:**
- 🇬🇧 English: [`widget_template_en.jinja2`](config/widget_template_en.jinja2)
- 🇫🇷 French: [`widget_template_fr.jinja2`](config/widget_template_fr.jinja2)

```jinja2
{% set NAME       = 'Thomas' %}        # Your first name
{% set COLOR      = '#5b5b5b' %}       # Text color
{% set CURRENCY   = '€' %}             # Currency symbol
{% set EMOJI = {
  'sleep': '🌈',                       # Customizable emojis
  'event': '🗓️',
  'task_ok': '✅',
  'task_many': '☑️',
  'steps': '🚶‍♂️',
  'power': '⚡',
  'bday': '🎂'
} %}
```

**See the [customization documentation](docs/customization.md) for more options.**

## Support the Project

If this widget is useful to you and you want to support its development, you can buy me a coffee! ☕

<a href="https://buymeacoffee.com/tben" target="_blank">
  <img src="https://cdn.buymeacoffee.com/buttons/v2/default-yellow.png" alt="Buy Me A Coffee" height="50">
</a>

---

<p align="center">
  Made with ❤️ for the Home Assistant community
</p>

<p align="center">
  <a href="#-home-assistant-minimalist-widget">⬆️ Back to top</a>
</p>