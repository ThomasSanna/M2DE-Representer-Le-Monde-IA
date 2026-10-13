Corpus (Nombre de documents : N = 3)
d1 : « soleil brille fort »
d2 : « lune brille doucement »
d3 : « maison ancienne belle »
Vocabulaire et ordre figé (dimension = 8) :
𝑠𝑜𝑙𝑒𝑖𝑙, 𝑙𝑢𝑛𝑒, 𝑏𝑟𝑖𝑙𝑙𝑒, 𝑓𝑜𝑟𝑡, 𝑑𝑜𝑢𝑐𝑒𝑚𝑒𝑛𝑡, 𝑚𝑎𝑖𝑠𝑜𝑛, 𝑎𝑛𝑐𝑖𝑒𝑛𝑛𝑒, 𝑏𝑒𝑙𝑙𝑒

Q1: 
- Donnez les vecteurs one-hot des mots « soleil », « lune », « maison » dans l’ordre du
vocabulaire.
soleil = (1, 0, 0, 0, 0, 0, 0, 0)
lune = (0, 1, 0, 0, 0, 0, 0, 0)
maison = (0, 0, 0, 0, 0, 1, 0, 0)

- Calculez la similarité cosinus entre les paires (soleil, lune), (soleil, maison) et (lune,
maison) (expliquez vos calculs).
(soleil, lune) = soleil*lune/||soleil||*||lune|| (1*0 + 0*1 + 0*0) / (sqrt(1^2 + 0^2 + 0^2) * sqrt(0^2 + 1^2 + 0^2)) = 0 / (1 * 1) = 0
(soleil, maison) = (1*0 + 0*0 + 0*1) / (sqrt(1^2 + 0^2 + 0^2) * sqrt(0^2 + 0^2 + 1^2)) = 0 / (1 * 1) = 0
(lune, maison) = (0*0 + 1*0 + 0*1) / (sqrt(0^2 + 1^2 + 0^2) * sqrt(0^2 + 0^2 + 1^2)) = 0 / (1 * 1) = 0

- Pourquoi ceqe mesure n’apporte-t-elle aucune information sémantique avec le one-
hot ?
Chaque élément du vocabulaire est seul dans sa dimension, donc les vecteurs sont ortogonaux entre eux : aucune similarité.

Q2 — Représentation TF-IDF (base 𝑒)
- Etapes 1 et 2 - Calculez TF(t) , DF(t) et IDF(t) pour chaque terme du
vocabulaire

TF(t, d) = nombre d’occurrences du terme t dans le document d (ici, chaque terme présent n’apparaît qu’une fois).
DF(t) = nombre de documents contenant t. IDF(t) = ln(N / DF(t)) avec N = 3.

| Terme | TF d1 | TF d2 | TF d3 | DF | IDF |
|---|---|---|---|---|---|
| soleil | 1 | 0 | 0 | 1 | ln(3) ≈ 1,099 |
| lune | 0 | 1 | 0 | 1 | ln(3) ≈ 1,099 |
| brille | 1 | 1 | 0 | 2 | ln(3/2) ≈ 0,405 |
| fort | 1 | 0 | 0 | 1 | ln(3) ≈ 1,099 |
| doucement | 0 | 1 | 0 | 1 | ln(3) ≈ 1,099 |
| maison | 0 | 0 | 1 | 1 | ln(3) ≈ 1,099 |
| ancienne | 0 | 0 | 1 | 1 | ln(3) ≈ 1,099 |
| belle | 0 | 0 | 1 | 1 | ln(3) ≈ 1,099 |

- Etapes 3 et 4 - Construisez les vecteurs TF-IDF complets (dimension 8) de
d1, d2 et d3 (dans l’ordre du vocabulaire)

TF-IDF(t, d) = TF(t, d) × IDF(t). Tableau : phrases (documents) en colonnes, mots en lignes.

Tableau 3×8 — phrases en colonnes, mots en lignes :

| Terme | d1 | d2 | d3 |
|---|---|---|---|
| soleil | 1,099 | 0 | 0 |
| lune | 0 | 1,099 | 0 |
| brille | 0,405 | 0,405 | 0 |
| fort | 1,099 | 0 | 0 |
| doucement | 0 | 1,099 | 0 |
| maison | 0 | 0 | 1,099 |
| ancienne | 0 | 0 | 1,099 |
| belle | 0 | 0 | 1,099 |

d1 = (ln 3, 0, ln(3/2), ln 3, 0, 0, 0, 0) ≈ (1,099 ; 0 ; 0,405 ; 1,099 ; 0 ; 0 ; 0 ; 0)
d2 = (0, ln 3, ln(3/2), 0, ln 3, 0, 0, 0) ≈ (0 ; 1,099 ; 0,405 ; 0 ; 1,099 ; 0 ; 0 ; 0)
d3 = (0, 0, 0, 0, 0, ln 3, ln 3, ln 3) ≈ (0 ; 0 ; 0 ; 0 ; 0 ; 1,099 ; 1,099 ; 1,099)

Q4 — Construire la matrice de co-occurrence des mots du vocabulaire
‘𝑠𝑜𝑙𝑒𝑖𝑙, 𝑙𝑢𝑛𝑒, 𝑏𝑟𝑖𝑙𝑙𝑒, 𝑓𝑜𝑟𝑡, 𝑑𝑜𝑢𝑐𝑒𝑚𝑒𝑛𝑡, 𝑚𝑎𝑖𝑠𝑜𝑛, 𝑎𝑛𝑐𝑖𝑒𝑛𝑛𝑒, 𝑏𝑒𝑙𝑙𝑒‘
en considérant que deux mots co-apparaissent s’ils sont dans le même document (fenêtre choisie : 2 mots à gauche et à droite)

| | soleil | lune | brille | fort | doucement | maison | ancienne | belle |
|---|---|---|---|---|---|---|---|---|
|soleil | 0 | 0 | 1 | 1 | 0 | 0 | 0 | 0 |
|lune | 0 | 0 | 1 | 0 | 1 | 0 | 0 | 0 |
|brille | 1 | 1 | 0 | 1 | 1 | 0 | 0 | 0 |
|fort | 1 | 0 | 1 | 0 | 0 | 0 | 0 | 0 |
|doucement | 0 | 1 | 1 | 0 | 0 | 0 | 0 | 0 |
|maison | 0 | 0 | 0 | 0 | 0 | 0 | 1 | 1 |
|ancienne | 0 | 0 | 0 | 0 | 0 | 1 | 0 | 1 |
|belle | 0 | 0 | 0 | 0 | 0 | 1 | 1 | 0 |

sim(soleil, lune) = soleil.lune / ||soleil|| * ||lune|| = 0+1+0+0../sqrt(2)*sqrt(2) = 1/2 = 0,5

- Calculer la similarité cosinus entre les vecteurs de soleil et lune. Que montre ce calcul par rapport à TF-IDF ?