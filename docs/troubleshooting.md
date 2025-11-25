# 🔧 Guide de dépannage

Résolvez les problèmes les plus courants avec votre widget.

---

## 🚫 Les sensors n'apparaissent pas

### Symptôme
Après le redémarrage de HA, `sensor.taches_aujourd_hui` et/ou `sensor.events_today` n'apparaissent pas dans **Outils de développement > États**.

### Solutions

**1. Vérifier les erreurs de configuration**

- Ouvrez **Paramètres > Système > Journaux**
- Cherchez des erreurs contenant "template" ou "sensor"
- Les erreurs courantes :
  - Indentation incorrecte (YAML)
  - Guillemets manquants
  - Entité inexistante

**2. Vérifier l'indentation YAML**

```yaml
# ❌ INCORRECT (mauvaise indentation)
template:
- trigger:
  - platform: time_pattern

# ✅ CORRECT
template:
  - trigger:
      - platform: time_pattern
```

**3. Vérifier que les entités existent**

```yaml
entity_id: todo.ma_liste  # ← Cette entité doit exister !
```

- Allez dans **Outils de développement > États**
- Cherchez `todo.ma_liste`
- Si elle n'existe pas, corrigez le nom

**4. Recharger les templates**

- **Outils de développement > YAML**
- **Recharger les entités de template**
- Attendez 30 secondes
- Vérifiez à nouveau

---

## ⚠️ Le widget affiche "Indisponible"

### Symptôme
Le widget affiche "Indisponible" ou "Unknown" au lieu des données.

### Solutions

**1. Vérifier que les sensors existent**

Dans le template du widget, vérifiez les noms :

```jinja2
{% set ENTITIES = {
  'tasks': 'sensor.taches_aujourd_hui',  # ← Existe-t-il ?
  'steps': 'sensor.thomas_pas_journee',  # ← Existe-t-il ?
  ...
} %}
```

**2. Tester dans Developer Tools**

- **Outils de développement > Template**
- Testez :
```jinja2
{{ states('sensor.taches_aujourd_hui') }}
```
- Si retourne `unknown` ou `unavailable` → Le sensor n'est pas configuré correctement

**3. Vérifier les permissions de l'app**

- **Paramètres Android > Apps > Home Assistant**
- Vérifiez les permissions (localisation, activité physique)

---

## 🔄 Le widget ne se met pas à jour

### Symptôme
Les données affichées ne changent pas, même après plusieurs heures.

### Solutions

**1. Vérifier les triggers des sensors**

Les sensors doivent se mettre à jour automatiquement :

```yaml
trigger:
  - platform: time_pattern
    minutes: "/10"  # ← Toutes les 10 minutes
```

**2. Forcer la mise à jour manuellement**

- **Appuyez sur le widget** pour le rafraîchir
- Ou **Outils de développement > YAML > Recharger les entités de template**

**3. Vérifier les rate limits**

Si votre template est trop complexe, Home Assistant peut limiter les mises à jour.

**Solution :**
- Simplifiez le template
- Augmentez l'intervalle de mise à jour (`minutes: "/15"` au lieu de `"/10"`)

---

## ❌ Erreur "template" dans le widget

### Symptôme
Le widget affiche une erreur de template ou ne s'affiche pas du tout.

### Solutions

**1. Vérifier la syntaxe Jinja2**

Erreurs courantes :
```jinja2
# ❌ INCORRECT (accolade fermante manquante)
{{ NAME }

# ✅ CORRECT
{{ NAME }}
```

**2. Tester le template dans Developer Tools**

- **Outils de développement > Template**
- Collez votre code complet
- Identifiez la ligne d'erreur

**3. Vérifier les noms d'entités**

```jinja2
{% set ENTITIES = {
  'tasks': 'sensor.taches_aujourd_hui',  # ← Vérifiez le nom exact
  ...
} %}
```

---

## 🔢 Les nombres ne s'affichent pas correctement

### Symptôme
- Les pas affichent `0` au lieu du nombre réel
- La consommation affiche `0.0`
- Le sommeil affiche `—`

### Solutions

**1. Vérifier que les sensors existent**

```jinja2
{{ states('sensor.thomas_pas_journee') }}
```

**2. Vérifier le type de données**

Certains sensors retournent du texte au lieu de nombres :

```jinja2
# ❌ Si le sensor retourne "5432 pas"
{% set steps = states(ENTITIES.steps)|int(0) %}  # → 0

# ✅ Solution : extraire le nombre
{% set steps = states(ENTITIES.steps)|regex_findall_index('\\d+') | int(0) %}
```

**3. Vérifier les unités**

```yaml
# Configuration du sensor
unit_of_measurement: "pas"  # Parfois nécessaire
```

---

## 📅 Les événements ne s'affichent pas

### Symptôme
Le compteur d'événements affiche toujours `0`.

### Solutions

**1. Vérifier l'intégration calendrier**

- **Paramètres > Appareils et services**
- Vérifiez que votre calendrier est bien connecté
- Testez l'accès aux événements

**2. Vérifier le nom du calendrier**

```yaml
entity_id:
  - calendar.mon_calendrier  # ← Nom exact ?
```

**3. Tester manuellement**

Dans **Outils de développement > Services** :
```yaml
service: calendar.get_events
target:
  entity_id: calendar.mon_calendrier
data:
  start_date_time: "{{ now() }}"
  end_date_time: "{{ today_at('23:59:59') }}"
```

---

## ✅ Les tâches ne se filtrent pas correctement

### Symptôme
Le compteur affiche toutes les tâches, pas seulement celles du jour.

### Solutions

**1. Vérifier que les tâches ont des dates**

Dans Google Tasks ou votre app de tâches :
- Assignez une date aux tâches importantes
- Les tâches sans date seront comptées

**2. Vérifier le sensor**

```yaml
{% for task in tasks %}
  {% if task.due is not defined or task.due is none or task.due <= today %}
    # ← Cette condition filtre les tâches
  {% endif %}
{% endfor %}
```

**3. Tester le service manually**

```yaml
service: todo.get_items
target:
  entity_id: todo.ma_liste
data:
  status: needs_action
```

---

## 🎂 Les anniversaires ne s'affichent pas

### Symptôme
Le widget n'affiche jamais d'anniversaires, même quand il y en a.

### Solutions

**1. Vérifier le calendrier d'anniversaires**

```jinja2
{% set BIRTHDAYS = [
  'calendar.anniversaires'  # ← Existe-t-il ?
] %}
```

**2. Vérifier le format des événements**

Les anniversaires doivent :
- Être des événements d'une journée entière
- Avoir un attribut `start_time`

**3. Désactiver temporairement**

Si vous n'utilisez pas cette fonctionnalité :
```jinja2
{% set BIRTHDAYS = [] %}
```

---

## 🐛 Autres problèmes

### Le widget est trop lent

**Solutions :**
- Augmentez l'intervalle de mise à jour des sensors (`minutes: "/15"`)
- Simplifiez le template
- Réduisez le nombre de calendriers

### Le texte est coupé

**Solutions :**
- Réduisez la taille du texte dans les paramètres du widget
- Utilisez un widget plus grand (4x2 au lieu de 3x1)
- Simplifiez les messages

### Les couleurs ne s'affichent pas

**Solutions :**
```jinja2
{% set COLOR = '#5b5b5b' %}  # ← Vérifiez le code couleur
```

---

## 📞 Toujours besoin d'aide ?

Si votre problème persiste :

1. **Vérifiez les logs** de Home Assistant
2. **Ouvrez une issue** sur GitHub avec :
   - Votre version de HA
   - Le code de votre template
   - Les erreurs dans les logs
3. **Demandez sur le forum** Home Assistant

---

## 🚀 Ressources utiles

- 📖 [Guide d'installation](installation.md)
- 🎨 [Guide de personnalisation](customization.md)
- 📚 [Documentation Jinja2](https://jinja.palletsprojects.com/)
- 💬 [Forum Home Assistant](https://community.home-assistant.io/)
- 📖 [Retour au README](../README.md)