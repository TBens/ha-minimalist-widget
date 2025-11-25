# 📖 Guide d'installation détaillé

Ce guide vous accompagne pas à pas pour installer le widget minimaliste sur votre Home Assistant.

---

## 📋 Prérequis

Avant de commencer, assurez-vous d'avoir :

### Home Assistant
- ✅ Home Assistant installé et fonctionnel
- ✅ Accès aux fichiers de configuration (via File Editor, SSH, ou Samba)
- ✅ Application Home Assistant installée sur Android

### Intégrations requises
- 📅 **Calendrier** : Google Calendar, CalDAV, ou autre
- ✅ **Todo** : Google Tasks, Todoist, ou autre
- 📱 **Companion App** : Pour les données de sommeil et de pas
- ⚡ **Énergie** (optionnel) : Pour la consommation électrique

---

## 🚀 Installation étape par étape

### Étape 1 : Vérifier vos entités

Avant d'installer le widget, identifiez vos entités existantes dans Home Assistant.

**1.1 Ouvrez Outils de développement > États**

**1.2 Cherchez vos entités :**
- 📅 **Calendrier(s)** : `calendar.mon_calendrier`
- ✅ **Todo** : `todo.ma_liste`
- 😴 **Sommeil** : `sensor.nom_duree_sommeil`
- 🚶‍♂️ **Pas** : `sensor.nom_pas_journee`
- ⚡ **Énergie** : `sensor.nom_consommation`

**💡 Astuce :** Notez ces noms, vous en aurez besoin !

---

### Étape 2 : Configurer les sensors

Les sensors permettent de compter automatiquement vos tâches et événements.

**2.1 Ouvrir votre configuration.yaml**

Via :
- File Editor (addon)
- SSH / Terminal
- Samba Share
- VS Code (avec addon)

**2.2 Ajouter les sensors**

Copiez le contenu de [`config/configuration_example.yaml`](../config/configuration_example.yaml) à la fin de votre `configuration.yaml`.

**2.3 Adapter les noms d'entités**

Remplacez les zones marquées **"À ADAPTER"** :

```yaml
# ❌ AVANT (exemple)
entity_id: todo.votre_liste_taches

# ✅ APRÈS (vos vraies entités)
entity_id: todo.thomas
```

**Entités à adapter :**
- `todo.votre_liste_taches` → Votre entité todo
- `calendar.votre_calendrier_1` → Votre calendrier principal
- `calendar.votre_calendrier_2` → Votre 2ème calendrier (optionnel)

**2.4 Sauvegarder le fichier**

---

### Étape 3 : Redémarrer Home Assistant

**Option 1 : Redémarrage complet** (recommandé)
- Paramètres > Système > Redémarrer

**Option 2 : Recharger les templates uniquement**
- Outils de développement > YAML > Recharger les entités de template

⏱️ Attendez 1-2 minutes après le redémarrage.

---

### Étape 4 : Vérifier les sensors

**4.1 Ouvrez Outils de développement > États**

**4.2 Cherchez vos nouveaux sensors :**
- `sensor.taches_aujourd_hui` ✅
- `sensor.events_today` ✅

**4.3 Vérifiez les valeurs**

Cliquez sur chaque sensor pour voir :
- **État** : Le nombre affiché (ex: `3`)
- **Attributs** : Informations supplémentaires

**❌ Les sensors n'apparaissent pas ?**
→ Consultez [Troubleshooting](troubleshooting.md)

---

### Étape 5 : Configurer le widget Android

**5.1 Sur votre téléphone Android**

1. Long press sur l'écran d'accueil
2. Appuyez sur **Widgets**
3. Scroll jusqu'à **Home Assistant**
4. Faites glisser **Template** sur votre écran

**5.2 Configurer le widget**

1. **Nom** : Donnez un nom (ex: "Mon jour")
2. **Template** : Copiez le contenu de [`config/widget_template.jinja2`](../config/widget_template.jinja2)
3. **Aperçu** : Vérifiez l'affichage en bas
4. **Sauvegarder**

**5.3 Personnaliser le template**

Dans la section **USER PARAMETERS** du template :

```jinja2
{% set NAME = 'Thomas' %}          # ← Votre prénom
{% set COLOR = '#5b5b5b' %}        # ← Couleur du texte
{% set CURRENCY = '€' %}           # ← Devise

{% set ENTITIES = {
  'tasks': 'sensor.taches_aujourd_hui',
  'steps': 'sensor.VOTRE_pas_journee',        # ← À ADAPTER
  'conso': 'sensor.VOTRE_conso_electrique',   # ← À ADAPTER
  'sleep': 'sensor.VOTRE_duree_sommeil'       # ← À ADAPTER
} %}

{% set BIRTHDAYS = [
  'calendar.VOTRE_anniversaires'  # ← À ADAPTER (optionnel)
] %}
```

**5.4 Sauvegarder**

---

## ✅ Vérification finale

Votre widget devrait maintenant afficher :

**Le matin (5h-12h) :**
> Bonjour [Nom], tu as dormi 🌈 7h 34, tu as 📅 2 événements et ☑️ 3 tâches aujourd'hui
>
> 🚶‍♂️ 124 pas  ⚡ 0.50€

**L'après-midi (12h-17h) :**
> Bon après-midi [Nom], tu as 📅 1 événement et ☑️ 2 tâches aujourd'hui
>
> 🚶‍♂️ 5 432 pas  ⚡ 1.24€

---

## 🎨 Prochaines étapes

- 📚 [Personnaliser votre widget](customization.md)
- 🚀 [Fonctionnalités avancées](advanced_features.md)
- 🐛 [Résolution de problèmes](troubleshooting.md)

---

## 💬 Besoin d'aide ?

- 🐛 [Ouvrez une issue](../../issues)
- 💬 [Forum Home Assistant](https://community.home-assistant.io/)
- 📖 [Retour au README](../README.md)