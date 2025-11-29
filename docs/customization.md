# 🎨 Customization Guide

Customize your widget to perfectly match your needs!

---

## 🔧 Basic Parameters

### Change Your Name

```jinja2
{% set NAME = 'Thomas' %}  # ← Change here
```

**Result:**
- "Bonjour Thomas" → "Bonjour Marie"

---

### Change Text Color

```jinja2
{% set COLOR = '#5b5b5b' %}  # ← Hexadecimal color code
```

**Color examples:**
- `#5b5b5b` - Gray (default)
- `#888888` - Light gray
- `#333333` - Dark gray
- `#666666` - Medium gray

**💡 Tip:** Test colors on [HTML Color Codes](https://htmlcolorcodes.com/)

---

### Change Currency

```jinja2
{% set CURRENCY = '€' %}  # ← Currency symbol
```

**Examples:**
- `€` - Euro
- `$` - Dollar
- `£` - Pound
- `CHF` - Swiss Franc

---

### Time Format

```jinja2
{% set USE_24H = true %}  # true = 24h, false = 12h (AM/PM)
```

**Not currently used**, but ready for future features (displaying event times).

---

### Thousands Separator

```jinja2
{% set SEP = '' %}  # Empty by default
```

**Examples:**
- `''` - No separator: `12456`
- `' '` - Space: `12 456`
- `','` - Comma: `12,456`

**Affects:** Step count display

---

## 😀 Customize Emojis

```jinja2
{% set EMOJI = {
  'sleep': '🌈',
  'event': '🗓️',
  'task_ok': '✅',
  'task_many': '☑️',
  'steps': '🚶‍♂️',
  'power': '⚡',
  'bday': '🎂'
} %}
```

### Customization Ideas

**Sleep:**
- `🌈` (rainbow)
- `😴` (sleeping)
- `💤` (zzz)
- `🛌` (bed)

**Events:**
- `🗓️` (calendar)
- `📅` (calendar alt)
- `📆` (tear-off calendar)
- `🎯` (target)

**Tasks complete:**
- `✅` (check mark)
- `✔️` (check)
- `🎉` (party)
- `👍` (thumbs up)

**Tasks in progress:**
- `☑️` (checked box)
- `📝` (note)
- `⏳` (hourglass)
- `🔨` (hammer)

**Steps:**
- `🚶‍♂️` (man walking)
- `🚶‍♀️` (woman walking)
- `👟` (shoe)
- `🏃` (runner)

**Power:**
- `⚡` (lightning)
- `🔋` (battery)
- `💡` (bulb)
- `⚙️` (gear)

**Birthdays:**
- `🎂` (cake)
- `🎉` (party)
- `🎁` (gift)
- `🎈` (balloon)

---

## 🏠 Configure Your Entities

### Main Entities

```jinja2
{% set ENTITIES = {
  'tasks': 'sensor.taches_aujourd_hui',
  'steps': 'sensor.your_daily_steps',
  'conso': 'sensor.your_power_consumption',
  'sleep': 'sensor.your_sleep_duration'
} %}
```

**Replace** entities with your own sensors:

1. **Open** Developer Tools > States
2. **Find** your sensors
3. **Copy** exact name (with `sensor.`)
4. **Replace** in template

---

### Birthday Calendar (optional)

```jinja2
{% set BIRTHDAYS = [
  'calendar.birthdays'
] %}
```

**To disable birthdays:**
```jinja2
{% set BIRTHDAYS = [] %}  # Empty list
```

**For multiple calendars:**
```jinja2
{% set BIRTHDAYS = [
  'calendar.family_birthdays',
  'calendar.friends_birthdays'
] %}
```

---

## ⏰ Customize Messages by Period

### Transition Hours

```jinja2
{# Morning #}
{% if 5 <= h < 12 %}
  ...

{# Afternoon #}
{% elif 12 <= h < 17 %}
  ...

{# Evening #}
{% elif 17 <= h < 22 %}
  ...

{# Night #}
{% else %}
  ...
```

**Modify hours:**
- `5 <= h < 12` → Morning from 5am to 11:59am
- `12 <= h < 17` → Afternoon from 12pm to 4:59pm
- `17 <= h < 22` → Evening from 5pm to 9:59pm
- Otherwise → Night from 10pm to 4:59am

---

### Change Messages

**Example: Modify morning message**

```jinja2
{# BEFORE #}
{{ open }}Bonjour{{ close }} {{ NAME }}, {{ open }}tu as dormi{{ close }} {{ EMOJI.sleep }} {{ sleep_raw }}, {{ open }}tu as{{ close }} {{ join_list(items) }} {{ open }}aujourd'hui{{ close }}

{# AFTER #}
{{ open }}Hello{{ close }} {{ NAME }}, {{ open }}you slept{{ close }} {{ EMOJI.sleep }} {{ sleep_raw }}, {{ open }}you have{{ close }} {{ join_list(items) }} {{ open }}today{{ close }}
```

**💡 Tip:** Keep structure with `{{ open }}...{{ close }}` to maintain color consistency.

---

## 📊 Hide Statistics

### Hide Steps

```jinja2
{# BEFORE #}
<p style="text-align:start"><small>{{ open }}{{ EMOJI.steps }} {{ steps_txt }} pas  {{ EMOJI.power }} {{ conso }}{{ CURRENCY }}{{ close }}</small></p>

{# AFTER (steps hidden) #}
<p style="text-align:start"><small>{{ open }}{{ EMOJI.power }} {{ conso }}{{ CURRENCY }}{{ close }}</small></p>
```

### Hide Consumption

```jinja2
{# AFTER (consumption hidden) #}
<p style="text-align:start"><small>{{ open }}{{ EMOJI.steps }} {{ steps_txt }} pas{{ close }}</small></p>
```

### Hide Entire Statistics Line

```jinja2
{# Delete this line completely #}
<p style="text-align:start"><small>...</small></p>
```

---

## 🎯 Add New Statistics

### Example: Add Weather

**1. Add entity in ENTITIES:**
```jinja2
{% set ENTITIES = {
  'tasks': 'sensor.taches_aujourd_hui',
  'steps': 'sensor.your_daily_steps',
  'conso': 'sensor.your_power_consumption',
  'sleep': 'sensor.your_sleep_duration',
  'weather': 'sensor.outdoor_temperature'  # ← New
} %}
```

**2. Get value:**
```jinja2
{% set weather = states(ENTITIES.weather)|float(0)|round(1) %}
```

**3. Display it:**
```jinja2
<p style="text-align:start"><small>{{ open }}🌡️ {{ weather }}°C  {{ EMOJI.steps }} {{ steps_txt }} pas  {{ EMOJI.power }} {{ conso }}{{ CURRENCY }}{{ close }}</small></p>
```

---

## 🌍 Translate to English

**Example for morning:**

```jinja2
{# Morning #}
{% if 5 <= h < 12 %}
<b>
<p style="text-align:start">
{{ open }}Good morning{{ close }} {{ NAME }}, {{ open }}you slept{{ close }} {{ EMOJI.sleep }} {{ sleep_raw }}, {{ open }}you have{{ close }} {{ join_list(items) }} {{ open }}today{{ close }}
</p>
<p style="text-align:start"><small>{{ open }}{{ EMOJI.steps }} {{ steps_txt }} steps  {{ EMOJI.power }} {{ conso }}{{ CURRENCY }}{{ close }}</small></p>
</b>
```

**⚠️ Don't forget to also modify:**
- `évènement` → `event`
- `tâche` → `task`
- `anniversaire` → `birthday`
- Plural function `s(n)`

---

## 💾 Save Your Changes

After each modification:

1. **Edit** template in Android widget
2. **Check** preview at bottom of screen
3. **Save** widget
4. **Wait** a few seconds to see changes

---

## 💡 Customization Examples

### Minimalist Style (even simpler)

```jinja2
{# Morning #}
{% if 5 <= h < 12 %}
<b>
<p style="text-align:start">
{{ open }}Bonjour{{ close }} {{ NAME }}<br>
{{ EMOJI.sleep }} {{ sleep_raw }}<br>
{{ join_list(items) }}
</p>
</b>
```

### Detailed Style

```jinja2
{# Morning #}
{% if 5 <= h < 12 %}
<b>
<p style="text-align:start">
{{ open }}🌅 Bonjour{{ close }} {{ NAME }} {{ open }}!{{ close }}<br>
{{ open }}Night:{{ close }} {{ EMOJI.sleep }} {{ sleep_raw }}<br>
{{ open }}Today:{{ close }} {{ join_list(items) }}
</p>
<p style="text-align:start"><small>{{ open }}📊 Activity: {{ EMOJI.steps }} {{ steps_txt }} steps<br>💰 Energy: {{ EMOJI.power }} {{ conso }}{{ CURRENCY }}{{ close }}</small></p>
</b>
```

---

## 🚀 Next Steps

- 🔧 [Troubleshooting](troubleshooting.md)
- 🚀 [Advanced features](advanced_features.md)
- 📖 [Back to README](../README.md)