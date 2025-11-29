# 📖 Detailed Installation Guide

This guide will walk you through installing the minimalist widget on your Home Assistant step by step.

---

## 📋 Prerequisites

Before starting, make sure you have:

### Home Assistant
- ✅ Home Assistant installed and running
- ✅ Access to configuration files (via File Editor, SSH, or Samba)
- ✅ Home Assistant app installed on Android

### Required Integrations
- 📅 **Calendar**: Google Calendar, CalDAV, or other
- ✅ **Todo**: Google Tasks, Todoist, or other
- 📱 **Companion App**: For sleep and step data
- ⚡ **Energy** (optional): For power consumption

---

## 🚀 Step-by-Step Installation

### Step 1: Identify Your Entities

Before installing the widget, identify your existing entities in Home Assistant.

**1.1 Open Developer Tools > States**

**1.2 Find your entities:**
- 📅 **Calendar(s)**: `calendar.my_calendar`
- ✅ **Todo**: `todo.my_list`
- 😴 **Sleep**: `sensor.name_sleep_duration`
- 🚶‍♂️ **Steps**: `sensor.name_daily_steps`
- ⚡ **Energy**: `sensor.name_consumption`

**💡 Tip:** Note these names, you'll need them!

---

### Step 2: Configure Sensors

Sensors automatically count your tasks and events.

**2.1 Open your configuration.yaml**

Via:
- File Editor (addon)
- SSH / Terminal
- Samba Share
- VS Code (with addon)

**2.2 Add sensors**

Copy the content of [`config/configuration_example.yaml`](../config/configuration_example.yaml) to the end of your `configuration.yaml`.

**2.3 Adapt entity names**

Replace zones marked **"ADAPT THIS"**:

```yaml
# ❌ BEFORE (example)
entity_id: todo.your_task_list

# ✅ AFTER (your real entities)
entity_id: todo.thomas
```

**Entities to adapt:**
- `todo.your_task_list` → Your todo entity
- `calendar.your_calendar_1` → Your main calendar
- `calendar.your_calendar_2` → Your 2nd calendar (optional)

**2.4 Save the file**

---

### Step 3: Restart Home Assistant

**Option 1: Full restart** (recommended)
- Settings > System > Restart

**Option 2: Reload templates only**
- Developer Tools > YAML > Reload template entities

⏱️ Wait 1-2 minutes after restart.

---

### Step 4: Verify Sensors

**4.1 Open Developer Tools > States**

**4.2 Find your new sensors:**
- `sensor.taches_aujourd_hui` ✅
- `sensor.events_today` ✅

**4.3 Check values**

Click on each sensor to see:
- **State**: The displayed number (e.g., `3`)
- **Attributes**: Additional information

**❌ Sensors don't appear?**
→ Check [Troubleshooting](troubleshooting.md)

---

### Step 5: Configure Android Widget

**5.1 On your Android phone**

1. Long press on home screen
2. Tap **Widgets**
3. Scroll to **Home Assistant**

![Widget List](../assets/screenshots/step1_widget_list.png)

4. Look for the **Template** widget (description: "Widget de modèle 2 x 1 - Rendu de n'importe quel modèle avec une mise en forme HTML")

![Template Widget](../assets/screenshots/step2_template_widget.png)

5. Drag **Template** to your screen

**5.2 Configure widget**

![Widget Configuration](../assets/screenshots/step3_widget_config.png)

1. **Template field** ("Saisir le modèle ici"): Copy content of [`config/widget_template.jinja2`](../config/widget_template.jinja2)
2. **Widget text size**: Adjust to your preference (default: 28)
3. **Widget theme**: Choose Transparent, Light, or Dark
4. **Widget text color**: White or Black
5. **Preview**: Check display at top of screen

![Widget Preview](../assets/screenshots/step4_preview.png)

**5.3 Customize template**

In the **USER PARAMETERS** section of the template:

```jinja2
{% set NAME = 'Thomas' %}          # ← Your first name
{% set COLOR = '#5b5b5b' %}        # ← Text color
{% set CURRENCY = '€' %}           # ← Currency

{% set ENTITIES = {
  'tasks': 'sensor.taches_aujourd_hui',
  'steps': 'sensor.YOUR_daily_steps',        # ← ADAPT THIS
  'conso': 'sensor.YOUR_power_consumption',  # ← ADAPT THIS
  'sleep': 'sensor.YOUR_sleep_duration'      # ← ADAPT THIS
} %}

{% set BIRTHDAYS = [
  'calendar.YOUR_birthdays'  # ← ADAPT THIS (optional)
] %}
```

**5.4 Save** ("AJOUTER UN WIDGET" / "METTRE À JOUR LE WIDGET")

---

## ✅ Final Verification

Your widget should now display:

**Morning (5am-12pm):**
> Bonjour [Name], tu as dormi 🌈 7h 34, tu as 📅 2 évènements et ☑️ 3 tâches aujourd'hui
>
> 🚶‍♂️ 124 pas  ⚡ 0.50€

**Afternoon (12pm-5pm):**
> Bon après-midi [Name], tu as 📅 1 évènement et ☑️ 2 tâches aujourd'hui
>
> 🚶‍♂️ 5 432 pas  ⚡ 1.24€

---

## 🎨 Next Steps

- 📚 [Customize your widget](customization.md)
- 🚀 [Advanced features](advanced_features.md)
- 🐛 [Troubleshooting](troubleshooting.md)

---

## 💬 Need Help?

- 🐛 [Open an issue](../../issues)
- 💬 [Home Assistant Forum](https://community.home-assistant.io/)
- 📖 [Back to README](../README.md)