# 🌈 Home Assistant Minimalist Widget

> A minimalist Android widget for Home Assistant displaying your daily summary with contextual messages based on time of day.

[![Home Assistant](https://img.shields.io/badge/Home%20Assistant-Compatible-41BDF5?style=flat&logo=homeassistant&logoColor=ffffff)](https://www.home-assistant.io/)
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Language](https://img.shields.io/badge/Language-Jinja2-red)](https://jinja.palletsprojects.com/)
[![Buy Me A Coffee](https://img.shields.io/badge/Buy%20Me%20A%20Coffee-Support-FFDD00?logo=buymeacoffee&logoColor=white)](https://buymeacoffee.com/tben)

---

## 📸 Preview

![Preview](assets/screenshots/illustration.png)

**Inspired by [Joi Planner](https://joi.software/)** - An app that I wanted to recreate with my own Home Assistant data!

---

## ✨ Features

- 🌅 **Contextual messages** - Different messages based on time of day (morning, afternoon, evening, night)
- 😴 **Sleep duration** - Display sleep time via smartphone integration
- 📅 **Remaining events** - Only counts upcoming events in the day
- ✅ **Smart tasks** - Automatically filters tasks for today (includes overdue and no-date tasks)
- 🎂 **Birthdays** - Automatic detection of today's birthdays
- 🚶‍♂️ **Daily statistics** - Step count and power consumption
- 🎨 **Customizable** - Emojis, colors, and data fully configurable

## 📋 Display Examples

![Preview](assets/screenshots/exemple-affichages.png)

---

## 🚀 Quick Installation

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
- `sensor.taches_aujourd_hui` - Counts tasks for today
- `sensor.events_today` - Counts remaining events

### 2. Restart Home Assistant

Restart HA or reload templates:
- **Developer Tools** > **YAML** > **Reload template entities**

### 3. Add the widget

1. Long press on your Android home screen
2. Select **Home Assistant** from the widget list

![Widget List](assets/screenshots/step1_widget_list.png)

3. Choose **Template** widget

![Template Widget](assets/screenshots/step2_template_widget.png)

4. Configure the widget:

![Widget Configuration](assets/screenshots/step3_widget_config.png)

- Copy the content of [`config/widget_template.jinja2`](config/widget_template.jinja2)
- Customize the parameters at the top of the code
- Check the preview

![Preview](assets/screenshots/step4_preview.png)

5. Save!

---

## 📚 Documentation

- 📖 [Detailed installation](docs/installation.md)
- 🎨 [Customization guide](docs/customization.md)
- 🔧 [Troubleshooting](docs/troubleshooting.md)
- 🚀 [Advanced features](docs/advanced_features.md)

---

## 🗂️ Project Structure

```
ha-minimalist-widget/
├── README.md                       # This file
├── LICENSE                         # MIT License
├── CHANGELOG.md                    # Version history
│
├── config/
│   ├── configuration_example.yaml # Complete configuration for HA
│   └── widget_template.jinja2     # Android widget code
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

---

## ⚙️ Customization

The widget is fully customizable! Modify the **USER PARAMETERS** section:

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

---

## ☕ Support the Project

If this widget is useful to you and you want to support its development, you can buy me a coffee! ☕

<a href="https://buymeacoffee.com/tben" target="_blank">
  <img src="https://cdn.buymeacoffee.com/buttons/v2/default-yellow.png" alt="Buy Me A Coffee" height="50">
</a>

Every contribution is appreciated and helps maintain this project! 🙏

---

<p align="center">
  Made with ❤️ for the Home Assistant community
</p>

<p align="center">
  <a href="#-home-assistant-minimalist-widget">⬆️ Back to top</a>
</p>