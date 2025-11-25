# 🚀 Fonctionnalités avancées

Allez plus loin avec votre widget en ajoutant des fonctionnalités personnalisées !

---

## 📱 Afficher le prochain événement

### But
Afficher le titre et l'heure du prochain événement de la journée.

### Code

Le sensor `events_today` contient déjà ces informations dans ses attributs !

```jinja2
{# Dans la section WIDGET LOGIC #}
{% set next_title = state_attr(EVENTS_SENSOR, 'next_event_title')|default('', true) %}
{% set next_time = state_attr(EVENTS_SENSOR, 'next_event_time')|default('', true) %}
```

**Affichage conditionnel :**

```jinja2
{% if next_title != '' %}
<p style="text-align:start"><small>{{ open }}{{ EMOJI.event }} {{ next_time }} : {{ next_title }}{{ close }}</small></p>
{% endif %}
```

**Résultat :**
> Bonjour Thomas, tu as dormi 🌈 7h 34, tu as 📅 2 événements et ☑️ 3 tâches aujourd'hui
>
> 🗓️ 14:30 : Réunion équipe

---

## ⏰ Afficher le réveil la nuit

### But
Afficher l'heure du prochain réveil pendant la période nuit (22h-5h).

### Prérequis
Vous devez avoir un sensor qui indique l'heure du prochain réveil. Exemples :
- `sensor.next_alarm` (Companion App Android)
- `sensor.iphone_next_alarm` (iOS)

### Code

**1. Ajouter l'entité dans ENTITIES :**

```jinja2
{% set ENTITIES = {
  'tasks': 'sensor.taches_aujourd_hui',
  'steps': 'sensor.thomas_pas_journee',
  'conso': 'sensor.prix_consommation_journee_total',
  'sleep': 'sensor.thomas_duree_sommeil',
  'alarm': 'sensor.next_alarm'  # ← Nouveau
} %}
```

**2. Récupérer et calculer les données :**

```jinja2
{# Dans la section WIDGET LOGIC #}
{% set alarm_raw = states(ENTITIES.alarm) %}
{% set alarm_time = '' %}
{% set alarm_in = '' %}

{% if alarm_raw not in ['unknown', 'unavailable', 'none'] %}
  {% set alarm_ts = as_timestamp(alarm_raw) %}
  {% set now_ts = as_timestamp(now()) %}
  {% set diff_hours = ((alarm_ts - now_ts) / 3600)|round(1) %}
  {% set alarm_time = (as_datetime(alarm_ts)|as_local).strftime('%H:%M') %}
  {% set alarm_in = 'dans ' ~ diff_hours ~ 'h' %}
{% endif %}
```

**3. Modifier la période nuit :**

```jinja2
{# Night #}
{% else %}
<b>
<p style="text-align:start">
{{ open }}Bonne nuit{{ close }} {{ NAME }}, {{ open }}tu as{{ close }} {{ join_list(items) }} {{ open }}aujourd'hui{{ close }}
{% if alarm_time != '' %}
<br>{{ open }}⏰ Réveil{{ close }} {{ alarm_in }} {{ open }}(à {{ alarm_time }}){{ close }}
{% endif %}
</p>
<p style="text-align:start"><small>{{ open }}{{ EMOJI.steps }} {{ steps_txt }} pas  {{ EMOJI.power }} {{ conso }}{{ CURRENCY }}{{ close }}</small></p>
</b>
{% endif %}
```

**Résultat :**
> Bonne nuit Thomas, tu as 📅 0 événement et ✅ 0 tâche aujourd'hui
> ⏰ Réveil dans 7.5h (à 06:30)

---

## 🎯 Afficher un objectif de pas

### But
Encourager l'utilisateur à atteindre un objectif quotidien de pas.

### Code

```jinja2
{# Définir l'objectif #}
{% set STEP_GOAL = 10000 %}

{# Calculer le pourcentage #}
{% set step_percent = ((steps / STEP_GOAL) * 100)|round(0)|int %}
{% set steps_remaining = (STEP_GOAL - steps)|int %}
```

**Affichage conditionnel :**

```jinja2
{# Dans la ligne de statistiques #}
<p style="text-align:start"><small>{{ open }}{{ EMOJI.steps }} {{ steps_txt }} / {{ STEP_GOAL|string }} pas ({{ step_percent }}%){{ close }}</small></p>

{# Ou avec message d'encouragement #}
{% if steps < STEP_GOAL %}
<p style="text-align:start"><small>{{ open }}💪 Plus que {{ steps_remaining }} pas pour atteindre ton objectif !{{ close }}</small></p>
{% else %}
<p style="text-align:start"><small>{{ open }}🎉 Objectif atteint ! Bravo !{{ close }}</small></p>
{% endif %}
```

---

## 🌡️ Ajouter la météo

### But
Afficher la température et les conditions météo actuelles.

### Prérequis
Avoir une intégration météo configurée (Met.no, OpenWeatherMap, etc.)

### Code

**1. Ajouter les entités :**

```jinja2
{% set ENTITIES = {
  ...
  'weather_temp': 'sensor.temperature',
  'weather_cond': 'sensor.weather_condition'
} %}
```

**2. Récupérer les données :**

```jinja2
{% set temp = states(ENTITIES.weather_temp)|float(0)|round(0) %}
{% set cond = states(ENTITIES.weather_cond) %}

{# Emoji selon la condition #}
{% set weather_emoji = '☀️' %}
{% if 'cloud' in cond|lower %}{% set weather_emoji = '☁️' %}
{% elif 'rain' in cond|lower %}{% set weather_emoji = '🌧️' %}
{% elif 'snow' in cond|lower %}{% set weather_emoji = '❄️' %}
{% endif %}
```

**3. Afficher :**

```jinja2
<p style="text-align:start"><small>{{ open }}{{ weather_emoji }} {{ temp }}°C  {{ EMOJI.steps }} {{ steps_txt }} pas  {{ EMOJI.power }} {{ conso }}{{ CURRENCY }}{{ close }}</small></p>
```

---

## 🚨 Alertes domotiques

### But
Afficher des alertes si quelque chose nécessite votre attention.

### Exemples d'alertes

**1. Lumières oubliées quand absent :**

```jinja2
{# Définir les entités #}
{% set presence = is_state('person.thomas', 'home') %}
{% set lights_on = is_state('light.salon', 'on') or is_state('light.cuisine', 'on') %}

{# Créer l'alerte #}
{% set alerts = [] %}
{% if not presence and lights_on %}
  {% set _ = alerts.append('💡 Lumières allumées (absent)') %}
{% endif %}
```

**2. Porte ouverte trop longtemps :**

```jinja2
{% set door_open = is_state('binary_sensor.porte_entree', 'on') %}
{% set door_time = (now() - states.binary_sensor.porte_entree.last_changed).total_seconds() / 60 %}

{% if door_open and door_time > 30 %}
  {% set _ = alerts.append('🚪 Porte ouverte depuis ' ~ door_time|round(0) ~ 'min') %}
{% endif %}
```

**3. Afficher les alertes :**

```jinja2
{% if alerts|length > 0 %}
<p style="text-align:start; color:red;">
<small><b>⚠️ Alertes :</b><br>
{% for alert in alerts %}
{{ alert }}<br>
{% endfor %}
</small>
</p>
{% endif %}
```

**Résultat :**
> ⚠️ Alertes :
> 💡 Lumières allumées (absent)
> 🚪 Porte ouverte depuis 45min

---

## 📊 Statistiques hebdomadaires

### But
Comparer les statistiques du jour avec la moyenne de la semaine.

### Code

**Créer un sensor pour la moyenne hebdomadaire :**

```yaml
# Dans configuration.yaml
template:
  - sensor:
      - name: "Moyenne pas semaine"
        state: >
          {{ states('sensor.thomas_pas_semaine')|int / 7 }}
        unit_of_measurement: "pas"
```

**Afficher la comparaison :**

```jinja2
{% set avg_steps = states('sensor.moyenne_pas_semaine')|int(0) %}
{% set diff = steps - avg_steps %}
{% set diff_percent = ((diff / avg_steps) * 100)|round(0) if avg_steps > 0 else 0 %}

<p style="text-align:start"><small>
{{ open }}{{ EMOJI.steps }} {{ steps_txt }} pas 
{% if diff > 0 %}
(+{{ diff_percent }}% vs moyenne)
{% elif diff < 0 %}
({{ diff_percent }}% vs moyenne)
{% endif %}
{{ close }}
</small></p>
```

---

## 🎨 Thèmes de couleurs

### But
Changer les couleurs selon l'heure ou l'humeur.

### Code

```jinja2
{# Définir le thème selon l'heure #}
{% if 5 <= h < 12 %}
  {% set COLOR = '#FF9800' %}  {# Orange pour le matin #}
{% elif 12 <= h < 17 %}
  {% set COLOR = '#2196F3' %}  {# Bleu pour l'après-midi #}
{% elif 17 <= h < 22 %}
  {% set COLOR = '#9C27B0' %}  {# Violet pour la soirée #}
{% else %}
  {% set COLOR = '#607D8B' %}  {# Gris-bleu pour la nuit #}
{% endif %}
```

---

## 🔔 Messages motivants

### But
Afficher des messages d'encouragement selon les statistiques.

### Code

```jinja2
{% set motivations = [] %}

{# Tâches complètes #}
{% if tasks == 0 %}
  {% set _ = motivations.append('🎉 Toutes tes tâches sont faites !') %}
{% endif %}

{# Objectif pas atteint #}
{% if steps >= 10000 %}
  {% set _ = motivations.append('💪 Objectif de pas atteint !') %}
{% endif %}

{# Pas d'événements #}
{% if ev_rest == 0 %}
  {% set _ = motivations.append('😌 Plus d\'événements aujourd\'hui !') %}
{% endif %}

{# Afficher #}
{% if motivations|length > 0 %}
<p style="text-align:start"><small><i>{{ motivations|join(' ') }}</i></small></p>
{% endif %}
```

---

## 📱 Plusieurs widgets

### But
Créer plusieurs widgets avec des informations différentes.

### Idées

**Widget 1 : Résumé complet** (actuel)
**Widget 2 : Tâches uniquement**
```jinja2
<b><p style="text-align:start">
{% if tasks == 0 %}
✅ Aucune tâche !
{% else %}
📝 {{ tasks }} tâche{{ s(tasks) }} à faire
{% endif %}
</p></b>
```

**Widget 3 : Prochain événement**
```jinja2
<b><p style="text-align:start">
{% if next_title != '' %}
{{ EMOJI.event }} {{ next_time }}<br>
{{ next_title }}
{% else %}
😌 Aucun événement
{% endif %}
</p></b>
```

---

## 💡 Idées futures

- 📧 Nombre d'emails non lus
- 📦 Suivi de colis
- 🚗 Temps de trajet jusqu'au travail
- 💰 Budget du jour
- 🏃 Objectifs fitness
- 🎵 Musique en cours
- 📺 Série à regarder

---

## 🔗 Ressources

- 📖 [Documentation Jinja2](https://jinja.palletsprojects.com/)
- 📚 [Templates Home Assistant](https://www.home-assistant.io/docs/configuration/templating/)
- 💬 [Forum Home Assistant](https://community.home-assistant.io/)
- 📖 [Retour au README](../README.md)