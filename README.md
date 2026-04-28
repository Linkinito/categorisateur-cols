# 🏔️ Catégorisateur de cols — Linkinito

Un outil web interactif pour estimer la catégorie d'un col cycliste selon un modèle inspiré du système Tour de France, conçu pour être adaptable à d'autres épreuves (Giro, Vuelta, critériums amateurs…).

👉 **[Accéder à l'outil en ligne](https://VOTRE_USERNAME.github.io/categorisateur-cols/)**

---

## 🎯 Fonctionnalités

- **Sliders + saisie libre** pour la longueur (km), la pente moyenne (%) et l'altitude du sommet (m)
- **Toggle arrivée au sommet** avec bonus configurable
- **Catégorisation instantanée** : HC, 1C, 2C, 3C, 4C ou Non catégorisé
- **Détail des métriques** : vitesse estimée, temps d'ascension, C² brute et corrigée, bonus altitude et arrivée
- **Seuils entièrement personnalisables** :
  - Limites C² et temps étalon par catégorie, avec validation des cohérences
  - Ajustement global proportionnel (×0,25 à ×4) pour C² et étalons simultanément
  - Bonus d'altitude paramétrable (gain %, intervalle en m, altitude plancher)
  - Bonus d'arrivée au sommet ajustable (3–20%)
  - Réinitialisation en un clic
- **100% côté client** — aucune dépendance serveur, fonctionne hors ligne une fois chargé

---

## 📐 Le modèle de catégorisation

### Paramètres d'entrée

| Paramètre | Description |
|---|---|
| Longueur (km) | Distance de l'ascension |
| Pente moyenne (%) | Gradient moyen sur toute la montée |
| Altitude du sommet (m) | Altitude au point culminant |
| Arrivée au sommet | Si l'étape se termine au col (oui/non) |

### Calcul étape par étape

**1. Vitesse estimée** (modèle logarithmique)
```
vitesse (km/h) = −15 × ln(pente_%) + 50
```
Exemples : 5% → ~25,9 km/h | 8% → ~17,0 km/h | 12% → ~12,3 km/h

**2. Temps d'ascension**
```
temps (min) = longueur / vitesse × 60
```

**3. Cotation au carré brute (C²)**
```
C² = longueur × pente² 
```
(pente en valeur absolue, ex. 7 et non 0,07)

**4. Bonus d'altitude** *(par défaut : +1% tous les 100 m au-dessus de 1 000 m)*
```
bonus_alt = 1 + (altitude − 1000) × 0,0001     si altitude > 1000 m
          = 1                                    sinon
```

**5. Bonus arrivée au sommet** *(par défaut : +10%)*
```
bonus_arrivée = 1,10  si arrivée au sommet
              = 1,00  sinon
```

**6. C² corrigée**
```
C²_corr = C² × bonus_alt × bonus_arrivée
```

**7. Catégorie de base** — première catégorie dont la limite C² est ≤ C²_corr

| Catégorie | Limite C² min. | Temps étalon |
|---|---|---|
| HC (Hors Catégorie) | 720 | 40 min |
| 1C | 360 | 24 min |
| 2C | 180 | 12 min |
| 3C | 90 | 6 min |
| 4C | 15 | 3 min |

**8. Vérification de durée** — si l'ascension est trop courte par rapport à l'étalon de sa catégorie :
```
ratio = temps_étalon / temps_ascension
seuil_ajusté = limite_C²_catégorie × ratio

Si C²_corr ≥ seuil_ajusté → catégorie maintenue
Sinon → rétrogradation d'un cran (voire Non catégorisé)
```

---

## 🗂️ Structure du projet

```
categorisateur-cols/
├── index.html      ← Application complète (tout-en-un, autonome)
└── README.md
```

---

## 🚀 Utilisation locale

Aucune installation requise. Il suffit d'ouvrir `index.html` dans un navigateur.

```bash
git clone https://github.com/VOTRE_USERNAME/categorisateur-cols.git
cd categorisateur-cols
open index.html   # macOS
xdg-open index.html   # Linux
```

---

## 📌 Exemples de cols emblématiques

| Col | Long. | Pente | Alt. | AAS | C²_corr | Catégorie |
|---|---|---|---|---|---|---|
| Alpe d'Huez | 13,8 km | 8,1% | 1 850 m | ✓ | ~1 154 | HC |
| Mont Ventoux (Bédoin) | 21,5 km | 7,5% | 1 909 m | ✓ | ~1 447 | HC |
| Col du Galibier | 18,1 km | 6,9% | 2 642 m | ✗ | ~1 019 | HC |
| Col de la Madeleine | 19,2 km | 8,0% | 2 000 m | ✗ | ~1 350 | HC |
| Col d'Ornon | 8,5 km | 7,2% | 1 367 m | ✗ | ~449 | 1C |
| Col de Portet-d'Aspet | 5,4 km | 7,1% | 1 069 m | ✗ | ~270 | 2C |

*(valeurs approximatives, à vérifier avec les profils officiels)*

---

## ⚙️ Personnalisation avancée

Tous les seuils et paramètres de bonification sont modifiables depuis l'interface dans la section **"Modifier les seuils et paramètres"** :

- Adapter le modèle à une épreuve amateur (seuils plus bas)
- Tester un resserrement des catégories (multiplicateur global C²)
- Ajuster l'altitude plancher pour les courses en haute montagne (Vuelta, Giro)

---

## 📄 Licence

Ce projet est distribué sous licence **MIT**. Vous pouvez librement l'utiliser, le modifier et le redistribuer, y compris à des fins commerciales.

---

*Modèle développé par Linkinito. Contributions et retours bienvenus via les Issues.*
