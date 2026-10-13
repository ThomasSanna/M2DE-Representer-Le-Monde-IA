# TP Ontologie — Partie 3 (justification) + Partie 4 + Partie 5

> Base IRI : `http://www.univ-corse.fr/dev/ontologie#` — préfixe `:` = cette IRI.
> Fichier OWL/RDF peuplé en parallèle avec exactement les individus ci-dessous.

## Rappel du peuplement

| Individu | Type | `maitrise` | `travailleSur` / `utilise` |
|---|---|---|---|
| Fred | Developpeur | React (Front), Spring (Back) | travailleSur WikiMaster |
| Michel | Developpeur | Vue (Front) | travailleSur Dialed |
| PetruSantu | Developpeur | NodeJS (Back), PostgreSQL (Data) | travailleSur Healthly |
| Ugo | Developpeur | React (Front), NodeJS (Back), MongoDB (Data) | travailleSur WikiMaster, Healthly |
| WikiMaster | Projet | — | utilise React, Spring (pas de Data) |
| Dialed | Projet | — | utilise Vue, PostgreSQL |
| Healthly | Projet | — | utilise NodeJS, MongoDB |
| React, Vue | FrontEnd | — | — |
| Spring, NodeJS | BackEnd | — | — |
| PostgreSQL, MongoDB | Data | — | — |

Classes définies :
- `DeveloppeurFullStack ≡ Developpeur and (maitrise some FrontEnd) and (maitrise some BackEnd)`
- `ProjetData ≡ Projet and (utilise some Data)`

---

## Partie 3 — Raisonnement (HermiT / Pellet)

Lancer le raisonneur dans Protégé : `Reasoner > HermiT` (ou Pellet) `> Start reasoner`.
Les inférences apparaissent en **jaune** (instances inférées) dans l'onglet `Individuals`. **Rien n'est déclaré directement** : aucune assertion de type `DeveloppeurFullStack` ou `ProjetData` dans le fichier — tout est inféré.

### Individus classifiés

**`DeveloppeurFullStack` : Fred, Ugo**

- **Fred** : est `Developpeur` (déclaré) + `maitrise some FrontEnd` (maîtrise React, et `React a FrontEnd`) + `maitrise some BackEnd` (maîtrise Spring, et `Spring a BackEnd`). Les deux conditions existentielles sont satisfaites → classifié `DeveloppeurFullStack`.
- **Ugo** : est `Developpeur` (déclaré) + `maitrise some FrontEnd` (maîtrise React) + `maitrise some BackEnd` (maîtrise NodeJS). Les deux conditions sont satisfaites → classifié `DeveloppeurFullStack`.

**`ProjetData` : Dialed, Healthly**

- **Dialed** : est `Projet` (déclaré) + `utilise some Data` (utilise PostgreSQL, et `PostgreSQL a Data`) → classifié `ProjetData`.
- **Healthly** : est `Projet` (déclaré) + `utilise some Data` (utilise MongoDB, et `MongoDB a Data`) → classifié `ProjetData`.

### Contre-exemples (non classifiés, et c'est attendu)

- **Michel** : `Developpeur` + `maitrise some FrontEnd` (Vue) mais **aucune** maîtrise d'un individu de type `BackEnd` → la 2ᵉ condition `maitrise some BackEnd` n'est pas satisfaite → pas `DeveloppeurFullStack`.
- **PetruSantu** : `Developpeur` + `maitrise some BackEnd` (NodeJS) mais **aucune** maîtrise d'un `FrontEnd` (ni React ni Vue) → pas `DeveloppeurFullStack`.
- **WikiMaster** : `Projet` mais `utilise` uniquement React (Front) et Spring (Back), **aucune** technologie de type `Data` → pas `ProjetData`.

---

## Partie 4 — Requêtes DL (onglet « DL Query », syntaxe Manchester)

Copier-coller tel quel dans la zone DL Query (raisonneur démarré).

### 1. Développeurs maîtrisant au moins une techno FrontEnd

```manchester
Developpeur and (maitrise some FrontEnd)
```

**Résultat attendu :** Fred, Michel, Ugo.

### 2. Développeurs travaillant sur au moins un projet

```manchester
Developpeur and (travailleSur some Projet)
```

**Résultat attendu :** Fred, Michel, PetruSantu, Ugo (les quatre).

### 3. Projets utilisant au moins une techno Data

```manchester
Projet and (utilise some Data)
```

**Résultat attendu :** Dialed, Healthly.

### 4. Développeurs FullStack (classe définie)

```manchester
DeveloppeurFullStack
```

**Résultat attendu :** Fred, Ugo (par inférence, cf. Partie 3).

---

## Partie 4 (suite) — Requêtes SPARQL (onglet « SPARQL Query » de Protégé)

Préfixes à inclure dans chaque requête.

### 5. Développeurs et technologies maîtrisées

```sparql
PREFIX : <http://www.univ-corse.fr/dev/ontologie#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>

SELECT ?developpeur ?technologie
WHERE {
  ?developpeur a :Developpeur .
  ?developpeur :maitrise ?technologie .
}
ORDER BY ?developpeur
```

**Résultat attendu (8 lignes) :**

| ?developpeur | ?technologie |
|---|---|
| :Fred | :React |
| :Fred | :Spring |
| :Michel | :Vue |
| :PetruSantu | :NodeJS |
| :PetruSantu | :PostgreSQL |
| :Ugo | :React |
| :Ugo | :NodeJS |
| :Ugo | :MongoDB |

### 6. Projets et technologies utilisées

```sparql
PREFIX : <http://www.univ-corse.fr/dev/ontologie#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>

SELECT ?projet ?technologie
WHERE {
  ?projet a :Projet .
  ?projet :utilise ?technologie .
}
ORDER BY ?projet
```

**Résultat attendu (6 lignes) :**

| ?projet | ?technologie |
|---|---|
| :Dialed | :Vue |
| :Dialed | :PostgreSQL |
| :Healthly | :NodeJS |
| :Healthly | :MongoDB |
| :WikiMaster | :React |
| :WikiMaster | :Spring |

---

## Partie 5 — Monde ouvert (OWA)

Prenons **Michel** : le fichier déclare uniquement `Michel maitrise Vue` (une techno FrontEnd) et **aucune** maîtrise de techno Data.

On ne peut **pas** conclure « Michel ne maîtrise aucune technologie Data ». En logique OWL, on travaille sous l'**hypothèse du monde ouvert (OWA)** : l'absence d'information n'est pas une information négative — le raisonneur considère qu'il est *possible* que Michel maîtrise une techno Data non déclarée, et laisse cette possibilité ouverte.

En monde fermé (CWA, cas des bases de données : tout ce qui n'est pas dans la table est faux), on conclurait que Michel ne maîtrise aucune techno Data ; en OWA, on s'abstient — la requête `Developpeur and not (maitrise some Data)` ne retourne personne sans axiome supplémentaire.

Pour pouvoir conclure négativement, il faudrait expliciter la négation, par exemple : une `NegativeObjectPropertyAssertion(Michel maitrise PostgreSQL / MongoDB)`, un axiome de fermeture (`Michel maitrise only (Vue)` ou `maitrise only (FrontEnd ...)`), l'usage de `owl:complementOf`, ou déclarer les individus distincts (`DifferentIndividuals`) combiné à une énumération fermée. Sans cela, le raisonneur reste correctement silencieux.
