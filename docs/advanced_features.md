# 🚀 Advanced Features

Go further with your widget by adding custom features!

---

## 📱 Display Next Event

### Purpose
Display title and time of next event of the day.

### Code

The `events_today` sensor already contains this information in its attributes!

```jinja2
{# In WIDGET LOGIC section #}
{% set next_title = state_attr(EVENTS_SENSOR, 'next_event_title')|default('', true) %}
{% set next_time = state_attr(EVENTS_SENSOR, 'next_event_time')|default('', true) %}
```

**Conditional display:**

```jinja2
{% if next_title != '' %}
<p style="text-align:start"><small>{{ open }}{{ EMOJI.event }} {{ next_time }} : {{ next_title }}{{ close }}</small></p>
{% endif %}
```

**Result:**
> Good morning Thomas, you slept 🌈 7h 34, you have 📅 2 events and ☑️ 3 tasks today
>
> 🗓️ 14:30 : Team meeting

---

## ⏰ Display Alarm at Night

### Purpose
Display next alarm time during night period (10pm-5am).

### Prerequisites
You need a sensor indicating next alarm time. Examples:
- `sensor.next_alarm` (Android Companion App)
- `sensor.iphone_next_alarm` (iOS)

### Code

**1. Add entity in ENTITIES:**

```jinja2
{% set ENTITIES = {
  'tasks': 'sensor.taches_aujourd_hui',
  'steps': 'sensor.thomas_daily_steps',
  'conso': 'sensor.daily_power_consumption',
  'sleep': 'sensor.thomas_sleep_duration',
  'alarm': 'sensor.next_alarm'  # ← New
} %}
```

**2. Get and calculate data:**

```jinja2
{# In WIDGET LOGIC section #}
{% set alarm_raw = states(ENTITIES.alarm) %}
{% set alarm_time = '' %}
{% set alarm_in = '' %}

{% if alarm_raw not in ['unknown', 'unavailable', 'none'] %}
  {% set alarm_ts = as_timestamp(alarm_raw) %}
  {% set now_ts = as_timestamp(now()) %}
  {% set diff_hours = ((alarm_ts - now_ts) / 3600)|round(1) %}
  {% set alarm_time = (as_datetime(alarm_ts)|as_local).strftime('%H:%M') %}
  {% set alarm_in = 'in ' ~ diff_hours ~ 'h' %}
{% endif %}
```

**3. Modify night period:**

```jinja2
{# Night #}
{% else %}
<b>
<p style="text-align:start">
{{ open }}Good night{{ close }} {{ NAME }}, {{ open }}you have{{ close }} {{ join_list(items) }} {{ open }}today{{ close }}
{% if alarm_time != '' %}
<br>{{ open }}⏰ Alarm{{ close }} {{ alarm_in }} {{ open }}(at {{ alarm_time }}){{ close }}
{% endif %}
</p>
<p style="text-align:start"><small>{{ open }}{{ EMOJI.steps }} {{ steps_txt }} steps  {{ EMOJI.power }} {{ conso }}{{ CURRENCY }}{{ close }}</small></p>
</b>
{% endif %}
```

**Result:**
> Good night Thomas, you have 📅 0 events and ✅ 0 tasks today
> ⏰ Alarm in 7.5h (at 06:30)

---

## 🎯 Display Step Goal

### Purpose
Encourage user to reach daily step goal.

### Code

```jinja2
{# Define goal #}
{% set STEP_GOAL = 10000 %}

{# Calculate percentage #}
{% set step_percent = ((steps / STEP_GOAL) * 100)|round(0)|int %}
{% set steps_remaining = (STEP_GOAL - steps)|int %}
```

**Conditional display:**

```jinja2
{# In statistics line #}
<p style="text-align:start"><small>{{ open }}{{ EMOJI.steps }} {{ steps_txt }} / {{ STEP_GOAL|string }} steps ({{ step_percent }}%){{ close }}</small></p>

{# Or with encouragement message #}
{% if steps < STEP_GOAL %}
<p style="text-align:start"><small>{{ open }}💪 Only {{ steps_remaining }} steps left to reach your goal!{{ close }}</small></p>
{% else %}
<p style="text-align:start"><small>{{ open }}🎉 Goal reached! Well done!{{ close }}</small></p>
{% endif %}
```

---

## 🌡️ Add Weather

### Purpose
Display current temperature and weather conditions.

### Prerequisites
Have weather integration configured (Met.no, OpenWeatherMap, etc.)

### Code

**1. Add entities:**

```jinja2
{% set ENTITIES = {
  ...
  'weather_temp': 'sensor.temperature',
  'weather_cond': 'sensor.weather_condition'
} %}
```

**2. Get data:**

```jinja2
{% set temp = states(ENTITIES.weather_temp)|float(0)|round(0) %}
{% set cond = states(ENTITIES.weather_cond) %}

{# Emoji based on condition #}
{% set weather_emoji = '☀️' %}
{% if 'cloud' in cond|lower %}{% set weather_emoji = '☁️' %}
{% elif 'rain' in cond|lower %}{% set weather_emoji = '🌧️' %}
{% elif 'snow' in cond|lower %}{% set weather_emoji = '❄️' %}
{% endif %}
```

**3. Display:**

```jinja2
<p style="text-align:start"><small>{{ open }}{{ weather_emoji }} {{ temp }}°C  {{ EMOJI.steps }} {{ steps_txt }} steps  {{ EMOJI.power }} {{ conso }}{{ CURRENCY }}{{ close }}</small></p>
```

---

## 🚨 Home Automation Alerts

### Purpose
Display alerts if something needs your attention.

### Alert Examples

**1. Forgotten lights when away:**

```jinja2
{# Define entities #}
{% set presence = is_state('person.thomas', 'home') %}
{% set lights_on = is_state('light.living_room', 'on') or is_state('light.kitchen', 'on') %}

{# Create alert #}
{% set alerts = [] %}
{% if not presence and lights_on %}
  {% set _ = alerts.append('💡 Lights on (away)') %}
{% endif %}
```

**2. Door open too long:**

```jinja2
{% set door_open = is_state('binary_sensor.front_door', 'on') %}
{% set door_time = (now() - states.binary_sensor.front_door.last_changed).total_seconds() / 60 %}

{% if door_open and door_time > 30 %}
  {% set _ = alerts.append('🚪 Door open for ' ~ door_time|round(0) ~ 'min') %}
{% endif %}
```

**3. Display alerts:**

```jinja2
{% if alerts|length > 0 %}
<p style="text-align:start; color:red;">
<small><b>⚠️ Alerts:</b><br>
{% for alert in alerts %}
{{ alert }}<br>
{% endfor %}
</small>
</p>
{% endif %}
```

**Result:**
> ⚠️ Alerts:
> 💡 Lights on (away)
> 🚪 Door open for 45min

---

## 📊 Weekly Statistics

### Purpose
Compare daily statistics with week average.

### Code

**Create sensor for weekly average:**

```yaml
# In configuration.yaml
template:
  - sensor:
      - name: "Average Weekly Steps"
        state: >
          {{ states('sensor.thomas_weekly_steps')|int / 7 }}
        unit_of_measurement: "steps"
```

**Display comparison:**

```jinja2
{% set avg_steps = states('sensor.average_weekly_steps')|int(0) %}
{% set diff = steps - avg_steps %}
{% set diff_percent = ((diff / avg_steps) * 100)|round(0) if avg_steps > 0 else 0 %}

<p style="text-align:start"><small>
{{ open }}{{ EMOJI.steps }} {{ steps_txt }} steps 
{% if diff > 0 %}
(+{{ diff_percent }}% vs average)
{% elif diff < 0 %}
({{ diff_percent }}% vs average)
{% endif %}
{{ close }}
</small></p>
```

---

## 🎨 Color Themes

### Purpose
Change colors based on time or mood.

### Code

```jinja2
{# Define theme based on time #}
{% if 5 <= h < 12 %}
  {% set COLOR = '#FF9800' %}  {# Orange for morning #}
{% elif 12 <= h < 17 %}
  {% set COLOR = '#2196F3' %}  {# Blue for afternoon #}
{% elif 17 <= h < 22 %}
  {% set COLOR = '#9C27B0' %}  {# Purple for evening #}
{% else %}
  {% set COLOR = '#607D8B' %}  {# Gray-blue for night #}
{% endif %}
```

---

## 🔔 Motivational Messages

### Purpose
Display encouragement messages based on statistics.

### Code

```jinja2
{% set motivations = [] %}

{# Tasks complete #}
{% if tasks == 0 %}
  {% set _ = motivations.append('🎉 All your tasks are done!') %}
{% endif %}

{# Step goal reached #}
{% if steps >= 10000 %}
  {% set _ = motivations.append('💪 Step goal reached!') %}
{% endif %}

{# No events #}
{% if ev_rest == 0 %}
  {% set _ = motivations.append('😌 No more events today!') %}
{% endif %}

{# Display #}
{% if motivations|length > 0 %}
<p style="text-align:start"><small><i>{{ motivations|join(' ') }}</i></small></p>
{% endif %}
```

---

## 📱 Multiple Widgets

### Purpose
Create multiple widgets with different information.

### Ideas

**Widget 1: Complete summary** (current)
**Widget 2: Tasks only**
```jinja2
<b><p style="text-align:start">
{% if tasks == 0 %}
✅ No tasks!
{% else %}
📝 {{ tasks }} task{{ s(tasks) }} to do
{% endif %}
</p></b>
```

**Widget 3: Next event**
```jinja2
<b><p style="text-align:start">
{% if next_title != '' %}
{{ EMOJI.event }} {{ next_time }}<br>
{{ next_title }}
{% else %}
😌 No events
{% endif %}
</p></b>
```

---

## 🎯 Integration with Automations

### Purpose
Trigger actions from widget (via tap action).

**Note:** Template widgets don't natively support tap actions, but you can:
1. Create button in widget
2. Use HA script
3. Call it via link

**Example (advanced):**
```html
<a href="homeassistant://navigate/lovelace/tasks">📝 View tasks</a>
```

---

## 💡 Future Ideas

- 📧 Unread email count
- 📦 Package tracking
- 🚗 Commute time to work
- 💰 Daily budget
- 🏃 Fitness goals
- 🎵 Current music
- 📺 Series to watch

---

## 🔗 Resources

- 📖 [Jinja2 Documentation](https://jinja.palletsprojects.com/)
- 📚 [Home Assistant Templates](https://www.home-assistant.io/docs/configuration/templating/)
- 💬 [Home Assistant Forum](https://community.home-assistant.io/)
- 📖 [Back to README](../README.md)