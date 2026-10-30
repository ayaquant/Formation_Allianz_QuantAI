# Dictionnaire de données — Portefeuille vie synthétique
**Formation IA pour actuariat vie — Allianz × Quant AI Lab**

Fichiers : `portefeuille_vie_synthetique.csv` (5 025 lignes, dont ~25 doublons volontaires)
et `portefeuille_vie_light.csv` (1 500 lignes, sous-échantillon pour la session 1).

**Cible du TP 1 / TP 2 :** `rachat_12m` — le contrat subit-il un rachat (total ou partiel
> 30 % de l'encours) dans les 12 mois suivant la date d'observation ?

## Variables

| Colonne | Type | Description | Vigilance |
|---|---|---|---|
| `id_contrat` | texte | identifiant du contrat | ~25 doublons volontaires à détecter et traiter |
| `age_actuel` | nombre | âge de l'assuré à la date d'observation | |
| `anciennete_annees` | nombre | ancienneté du contrat en années | franchir 8 ans change la fiscalité des rachats |
| `produit` | catégorie | `multi_support`, `euro_croissance`, `mono_euro`, `retraite_per` | |
| `canal` | catégorie | `agence`, `courtier`, `banque`, `en_ligne` | |
| `segment` | catégorie | `grand_public`, `affluent`, `prive` | segment `prive` volontairement rare (~6 %) ; ~3 % de manquantes |
| `versement_initial` | nombre | montant du premier versement (€) | très asymétrique (log-normale) |
| `versement_programme_mensuel` | nombre | mensualité programmée (€, 0 = aucune) | ~4 % de manquantes à imputer |
| `encours` | nombre | valeur actuelle du contrat (€) | dérivée des versements et de l'ancienneté |
| `part_unites_compte` | nombre | part de l'encours investie en UC (0–1) | |
| `taux_10ans` | nombre | taux d'État 10 ans à la date d'observation (%) | variable de contexte macro |
| `perf_uc_12m` | nombre | performance des UC sur 12 mois (%) | variable de contexte marché |
| `nb_arbitrages_12m` | nombre | arbitrages réalisés sur 12 mois | |
| `nb_rachats_partiels_5a` | nombre | rachats partiels sur 5 ans (antérieurs à la cible) | licite : historique connu à la date d'observation |
| `nb_contacts_12m` | nombre | contacts client sur 12 mois | ~6 % de manquantes à imputer |
| `montant_rachat_mois_m1` | nombre | montant racheté le mois suivant l'observation (€) | ⛔ **Fuite de données** : postérieure à la cible — INTERDITE comme variable explicative |
| `code_interne_hash` | texte | code technique interne | variable de bruit, sans sens métier — à exclure |
| `rachat_12m` | binaire | **cible** : rachat dans les 12 mois (1 = oui) | taux ≈ 6 % : classe déséquilibrée |

## Défauts volontaires (à détecter pendant les TP)

1. **Doublons** d'identifiants de contrat (~25 lignes).
2. **Valeurs manquantes** : `versement_programme_mensuel` (~4 %), `nb_contacts_12m` (~6 %), `segment` (~3 %).
3. **Segment rare** : `prive` (~6 % des contrats) — la performance globale masque sa fragilité.
4. **Variable de fuite** : `montant_rachat_mois_m1` (seule, AUC ≈ 0,93 — trop beau pour être vrai).
5. **Variable de bruit** : `code_interne_hash`.

## Période d'observation

Les caractéristiques sont figées à la date d'observation T ; la cible est observée sur
[T, T+12 mois]. Toute variable décrivant ce qui se passe **après** T est une fuite potentielle.
