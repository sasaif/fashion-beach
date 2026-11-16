# Assets & licences (état STEP 6)

Aucun asset payant. Chaque fichier livré dans `app/public/assets/` ou généré au runtime est listé ici.

| Asset | Fichier(s) | Origine | Licence |
|---|---|---|---|
| Avatar de base | `assets/avatar/avatar_base_f01.vrm` | `VRM1_Constraint_Twist_Sample.vrm`, dépôt officiel pixiv/three-vrm (exemples) | **VRM Public License 1.0**, © 2022 pixiv Inc. — usage commercial OK, modification + redistribution OK, crédit non requis, usage haineux/antisocial interdit. Un dérivé redistribué garde une licence au moins aussi restrictive. Source : https://vrm.dev/licenses/1.0/ (métadonnées lues dans le fichier, voir ASSET_EVALUATION.md §2A) |
| Texture corps de base | `assets/avatar/base/body_base_f01.png` | dérivée de la texture de l'avatar ci-dessus | VRM PL 1.0 (dérivé) |
| Vêtements (t-shirt, jean, bomber, baskets, haut de base) | `assets/clothing/*.glb` | géométrie dérivée des meshes de l'avatar VRoid (pipeline numpy `app/scripts/pipeline`), textures peintes de façon procédurale | VRM PL 1.0 (dérivé), voir STEP4.md |
| Visage : peau nettoyée, nez, bouches | `assets/face/**.png` | peau = texture de l'avatar inpaintée ; variantes dessinées par script (`make_face_cosmetics.py`) | VRM PL 1.0 (dérivé) / créé pour le projet |
| Animations | `assets/anim/ual_clips*.glb` | Quaternius **Universal Animation Library 1 & 2 (Standard)**, retargetées sur le VRM ; le geste « coucou » est créé pour le projet | **CC0 1.0** (page itch + `License.txt` des zips) — https://quaternius.itch.io/universal-animation-library |
| Miniatures du catalogue | `assets/thumbs/*.webp` | rendues depuis les assets ci-dessus (`scripts/make-thumbs.mjs`) | même licence que l'asset représenté |
| **Monde plage (STEP 6)** : sol, océan, ciel, palmiers, parasols, serviettes, coquillages, étoiles de mer, rochers, lanternes, bancs, passerelles, boutique, place, feu de camp, scène, guirlandes, dunes, barrières, îles, nuages | aucun fichier : géométrie **100 % procédurale** générée au chargement (`app/src/world/beach/*.ts`), shaders eau/ciel écrits pour le projet | créé pour le projet — aucune licence tierce |
| Panneaux (« Boutique ✿ », « Scène des amis », « Fashion Beach ») | canvas 512×384 dessiné au runtime | texte rendu avec la police système | créé pour le projet |

Les packs CC0 évalués (Kenney, Quaternius nature, Poly Haven) n'ont finalement **pas** été utilisés pour la plage : la géométrie procédurale donnait un style plus cohérent avec l'avatar, 0 octet à télécharger et aucun risque de licence.
