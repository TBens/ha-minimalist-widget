# 🔧 Troubleshooting Guide

Solve the most common problems with your widget.

---

## 🚫 Sensors Don't Appear

### Symptom
After HA restart, `sensor.tasks_today` and/or `sensor.events_today` don't appear in **Developer Tools > States**.

### Solutions

**1. Check configuration errors**

- Open **Settings > System > Logs**
- Look for errors containing "template" or "sensor"
- Common errors:
  - Incorrect indentation (YAML)
  - Missing quotes
  - Non-existent entity

**2. Check YAML indentation**

```yaml
# ❌ INCORRECT (wrong indentation)
template:
- trigger:
  - platform: time_pattern

# ✅ CORRECT
template:
  - trigger:
      - platform: time_pattern
```

**3. Check that entities exist**

```yaml
entity_id: todo.my_list  # ← This entity must exist!
```

- Go to **Developer Tools > States**
- Search for `todo.my_list`
- If it doesn't exist, fix the name

**4. Reload templates**

- **Developer Tools > YAML**
- **Reload template entities**
- Wait 30 seconds
- Check again

---

## ⚠️ Widget Displays "Unavailable"

### Symptom
Widget displays "Unavailable" or "Unknown" instead of data.

### Solutions

**1. Check that sensors exist**

In widget template, verify names:

```jinja2
{% set ENTITIES = {
  'tasks': 'sensor.tasks_today',  # ← Does it exist?
  'steps': 'sensor.thomas_daily_steps',  # ← Does it exist?
  ...
} %}
```

**2. Test in Developer Tools**

- **Developer Tools > Template**
- Test:
```jinja2
{{ states('sensor.tasks_today') }}
```
- If returns `unknown` or `unavailable` → Sensor not configured correctly

**3. Check app permissions**

- **Android Settings > Apps > Home Assistant**
- Check permissions (location, physical activity)

---

## 🔄 Widget Doesn't Update

### Symptom
Displayed data doesn't change, even after several hours.

### Solutions

**1. Check sensor triggers**

Sensors should update automatically:

```yaml
trigger:
  - platform: time_pattern
    minutes: "/10"  # ← Every 10 minutes
```

**2. Force manual update**

- **Tap the widget** to refresh it
- Or **Developer Tools > YAML > Reload template entities**

**3. Check rate limits**

If your template is too complex, Home Assistant may limit updates.

**Solution:**
- Simplify template
- Increase update interval (`minutes: "/15"` instead of `"/10"`)

---

## ❌ "Template" Error in Widget

### Symptom
Widget displays template error or doesn't display at all.

### Solutions

**1. Check Jinja2 syntax**

Common errors:
```jinja2
# ❌ INCORRECT (missing closing brace)
{{ NAME }

# ✅ CORRECT
{{ NAME }}
```

**2. Test template in Developer Tools**

- **Developer Tools > Template**
- Paste your complete code
- Identify error line

**3. Check entity names**

```jinja2
{% set ENTITIES = {
  'tasks': 'sensor.tasks_today',  # ← Check exact name
  ...
} %}
```

---

## 🔢 Numbers Don't Display Correctly

### Symptom
- Steps display `0` instead of real number
- Consumption displays `0.0`
- Sleep displays `—`

### Solutions

**1. Check that sensors exist**

```jinja2
{{ states('sensor.thomas_daily_steps') }}
```

**2. Check data type**

Some sensors return text instead of numbers:

```jinja2
# ❌ If sensor returns "5432 steps"
{% set steps = states(ENTITIES.steps)|int(0) %}  # → 0

# ✅ Solution: extract number
{% set steps = states(ENTITIES.steps)|regex_findall_index('\\d+') | int(0) %}
```

**3. Check units**

```yaml
# Sensor configuration
unit_of_measurement: "steps"  # Sometimes necessary
```

---

## 📅 Events Don't Display

### Symptom
Event counter always displays `0`.

### Solutions

**1. Check calendar integration**

- **Settings > Devices & Services**
- Verify calendar is connected
- Test event access

**2. Check calendar name**

```yaml
entity_id:
  - calendar.my_calendar  # ← Exact name?
```

**3. Test manually**

In **Developer Tools > Services**:
```yaml
service: calendar.get_events
target:
  entity_id: calendar.my_calendar
data:
  start_date_time: "{{ now() }}"
  end_date_time: "{{ today_at('23:59:59') }}"
```

---

## ✅ Tasks Don't Filter Correctly

### Symptom
Counter displays all tasks, not just today's.

### Solutions

**1. Check that tasks have dates**

In Google Tasks or your task app:
- Assign date to important tasks
- Tasks without date will be counted

**2. Check sensor**

```yaml
{% for task in tasks %}
  {% if task.due is not defined or task.due is none or task.due <= today %}
    # ← This condition filters tasks
  {% endif %}
{% endfor %}
```

**3. Test service manually**

```yaml
service: todo.get_items
target:
  entity_id: todo.my_list
data:
  status: needs_action
```

---

## 🎂 Birthdays Don't Display

### Symptom
Widget never displays birthdays, even when there are some.

### Solutions

**1. Check birthday calendar**

```jinja2
{% set BIRTHDAYS = [
  'calendar.birthdays'  # ← Does it exist?
] %}
```

**2. Check event format**

Birthdays must:
- Be all-day events
- Have `start_time` attribute

**3. Temporarily disable**

If you don't use this feature:
```jinja2
{% set BIRTHDAYS = [] %}
```

---

## 🐛 Other Problems

### Widget is Too Slow

**Solutions:**
- Increase sensor update interval (`minutes: "/15"`)
- Simplify template
- Reduce number of calendars

### Text is Cut Off

**Solutions:**
- Reduce text size in widget settings
- Use larger widget (4x2 instead of 3x1)
- Simplify messages

### Colors Don't Display

**Solutions:**
```jinja2
{% set COLOR = '#5b5b5b' %}  # ← Check color code
```

---

## 📞 Still Need Help?

If your problem persists:

1. **Check logs** in Home Assistant
2. **Open an issue** on GitHub with:
   - Your HA version
   - Your template code
   - Errors in logs
3. **Ask on forum** Home Assistant

---

## 🚀 Useful Resources

- 📖 [Installation guide](installation.md)
- 🎨 [Customization guide](customization.md)
- 📚 [Jinja2 Documentation](https://jinja.palletsprojects.com/)
- 💬 [Home Assistant Forum](https://community.home-assistant.io/)
- 📖 [Back to README](../README.md)