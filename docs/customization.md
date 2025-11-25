# 🎨 Guide de personnalisation

Personnalisez votre widget pour qu'il corresponde parfaitement à vos besoins !

---

## 🔧 Paramètres de base

### Modifier votre prénom

```jinja2
{% set NAME = 'Thomas' %}  # ← Changez ici
```

**Résultat :**
- "Bonjour Thomas" → "Bonjour Marie"

---

### Changer la couleur du texte

```jinja2
{% set COLOR = '#5b5b5b' %}  # ← Code couleur hexadécimal
```

**Exemples de couleurs :**
- `#5b5b5b` - Gris (défaut)
- `#888888` - Gris clair
- `#333333` - Gris foncé
- `#666666` - Gris moyen

**💡 Astuce :** Testez des couleurs sur [HTML Color Codes](https://htmlcolorcodes.com/)

---

### Changer la devise

```jinja2
{% set CURRENCY = '€' %}  # ← Symbole de devise
```

**Exemples :**
- `€` - Euro
- `$` - Dollar
- `£` - Livre
- `CHF` - Franc suisse

---

### Format d'heure

```jinja2
{% set USE_24H = true %}  # true = 24h, false = 12h (AM/PM)
```

**Non utilisé actuellement**, mais prêt pour de futures fonctionnalités (affichage d'heures d'événements).

---

### Séparateur de milliers

```jinja2
{% set SEP = '' %}  # Vide par défaut
```

**Exemples :**
- `''` - Pas de séparateur : `12456`
- `' '` - Espace : `12 456`
- `','` - Virgule : `12,456`

**Affecte :** Le nombre de pas affiché

---

## 😀 Personnaliser les emojis

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

### Idées de personnalisation

**Sommeil :**
- `🌈` (arc-en-ciel)
- `😴` (endormi)
- `💤` (zzz)
- `🛌` (lit)

**Événements :**
- `🗓️` (calendrier)
- `📅` (calendrier alternatif)
- `📆` (calendrier arraché)
- `🎯` (cible)

**Tâches complètes :**
- `✅` (coche verte)
- `✔️` (coche)
- `🎉` (fête)
- `👍` (pouce en l'air)

**Tâches en cours :**
- `☑️` (case cochée)
- `📝` (note)
- `⏳` (sablier)
- `🔨` (marteau)

**Pas :**
- `🚶‍♂️` (marcheur homme)
- `🚶‍♀️` (marcheuse femme)
- `👟` (chaussure)
- `🏃` (coureur)

**Consommation :**
- `⚡` (éclair)
- `🔋` (batterie)
- `💡` (ampoule)
- `⚙️` (engrenage)

**Anniversaires :**
- `🎂` (gâteau)
- `🎉` (fête)
- `🎁` (cadeau)
- `🎈` (ballon)

---

## 🏠 Configurer vos entités

### Entités principales

```jinja2
{% set ENTITIES = {
  'tasks': 'sensor.taches_aujourd_hui',
  'steps': 'sensor.votre_pas_journee',
  'conso': 'sensor.votre_conso_electrique',
  'sleep': 'sensor.votre_duree_sommeil'
} %}
```

**Remplacez** les entités par vos propres sensors :

1. **Ouvrez** Outils de développement > États
2. **Cherchez** vos sensors
3. **Copiez** le nom exact (avec `sensor.`)
4. **Remplacez** dans le template

---

### Calendrier d'anniversaires (optionnel)

```jinja2
{% set BIRTHDAYS = [
  'calendar.anniversaires'
] %}
```

**Pour désactiver les anniversaires :**
```jinja2
{% set BIRTHDAYS = [] %}  # Liste vide
```

**Pour plusieurs calendriers :**
```jinja2
{% set BIRTHDAYS = [
  'calendar.anniversaires_famille',
  'calendar.anniversaires_amis'
] %}
```

---

## ⏰ Personnaliser les messages par période

### Heures de transition

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

**Modifier les heures :**
- `5 <= h < 12` → Matin de 5h à 11h59
- `12 <= h < 17` → Après-midi de 12h à 16h59
- `17 <= h < 22` → Soirée de 17h à 21h59
- Sinon → Nuit de 22h à 4h59

---

### Changer les messages

**Exemple : Modifier le message du matin**

```jinja2
{# AVANT #}
{{ open }}Bonjour{{ close }} {{ NAME }}, {{ open }}tu as dormi{{ close }} {{ EMOJI.sleep }} {{ sleep_raw }}, {{ open }}tu as{{ close }} {{ join_list(items) }} {{ open }}aujourd'hui{{ close }}

{# APRÈS #}
{{ open }}Hello{{ close }} {{ NAME }}, {{ open }}sommeil :{{ close }} {{ EMOJI.sleep }} {{ sleep_raw }}, {{ join_list(items) }} {{ open }}pour aujourd'hui{{ close }}
```

**💡 Astuce :** Gardez la structure avec `{{ open }}...{{ close }}` pour maintenir la cohérence des couleurs.

---

## 📊 Masquer des statistiques

### Masquer les pas

```jinja2
{# AVANT #}
<p style="text-align:start"><small>{{ open }}{{ EMOJI.steps }} {{ steps_txt }} pas  {{ EMOJI.power }} {{ conso }}{{ CURRENCY }}{{ close }}</small></p>

{# APRÈS (pas masqués) #}
<p style="text-align:start"><small>{{ open }}{{ EMOJI.power }} {{ conso }}{{ CURRENCY }}{{ close }}</small></p>
```

### Masquer la consommation

```jinja2
{# APRÈS (conso masquée) #}
<p style="text-align:start"><small>{{ open }}{{ EMOJI.steps }} {{ steps_txt }} pas{{ close }}</small></p>
```

### Masquer toute la ligne de statistiques

```jinja2
{# Supprimez complètement cette ligne #}
<p style="text-align:start"><small>...</small></p>
```

---

## 🎯 Ajouter de nouvelles statistiques

### Exemple : Ajouter la météo

**1. Ajoutez l'entité dans ENTITIES :**
```jinja2
{% set ENTITIES = {
  'tasks': 'sensor.taches_aujourd_hui',
  'steps': 'sensor.votre_pas_journee',
  'conso': 'sensor.votre_conso_electrique',
  'sleep': 'sensor.votre_duree_sommeil',
  'meteo': 'sensor.temperature_exterieure'  # ← Nouveau
} %}
```

**2. Récupérez la valeur :**
```jinja2
{% set meteo = states(ENTITIES.meteo)|float(0)|round(1) %}
```

**3. Affichez-la :**
```jinja2
<p style="text-align:start"><small>{{ open }}🌡️ {{ meteo }}°C  {{ EMOJI.steps }} {{ steps_txt }} pas  {{ EMOJI.power }} {{ conso }}{{ CURRENCY }}{{ close }}</small></p>
```

---

## 🌍 Traduire en anglais

**Exemple pour le matin :**

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

**⚠️ N'oubliez pas de modifier aussi :**
- `évènement` → `event`
- `tâche` → `task`
- `anniversaire` → `birthday`
- La fonction plurielle `s(n)`

---

## 💾 Sauvegarder vos modifications

Après chaque modification :

1. **Modifiez** le template dans le widget Android
2. **Vérifiez** l'aperçu en bas de l'écran
3. **Sauvegardez** le widget
4. **Attendez** quelques secondes pour voir les changements

---

## 💡 Exemples de personnalisation

### Style minimaliste (encore plus simple)

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

### Style détaillé

```jinja2
{# Morning #}
{% if 5 <= h < 12 %}
<b>
<p style="text-align:start">
{{ open }}🌅 Bonjour{{ close }} {{ NAME }} {{ open }}!{{ close }}<br>
{{ open }}Nuit :{{ close }} {{ EMOJI.sleep }} {{ sleep_raw }}<br>
{{ open }}Aujourd'hui :{{ close }} {{ join_list(items) }}
</p>
<p style="text-align:start"><small>{{ open }}📊 Activité : {{ EMOJI.steps }} {{ steps_txt }} pas<br>💰 Énergie : {{ EMOJI.power }} {{ conso }}{{ CURRENCY }}{{ close }}</small></p>
</b>
```

---

## 🚀 Prochaines étapes

- 🔧 [Dépannage](troubleshooting.md)
- 🚀 [Fonctionnalités avancées](advanced_features.md)
- 📖 [Retour au README](../README.md)