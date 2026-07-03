# Liste des modifications de la codebase

- Nettoyage de la codebase (propreté - pas de modification structurelle)
- Ajout des différents titres en Markdown

## Mise à jour de la fonction `allowed_move`

- Les déplacements sur GOLD sont autorisés (fin de partie)
- La gestion des bords de la map est correctement implémentée

## Mise à jour de la fonction `move`

- La fonction retourne désormais
  - La nouvelle position
  - La collecte ou la non-collecte de GOLD

```python
move_result = {
        "gold_collected": False,
        "new_pos": old_pos
    }
```

## Mise à jour du moteur de décision

- A chaque décision, la perception est affichée dans le notebook

## Mise à jour de la simulation

- Si le joueur trouve un GOLD, la simulation est stoppée

# A faire pour le projet

## Définition

Pour la suite de ce projet, ces définitions sont importantes :

- **Charge LLM :** Décrit le périmètre décisionnel du LLM assisté par la charge algorithmique.
- **Charge algorithmique :** Décrit l'ensemble des mécanismes algorithmiques qui fournissent des informations de perception fiables. Elle peut aussi, de manière plus avancée, contraindre en amont les options soumises au LLM (ex : retirer une direction invalide avant de solliciter une décision).
- **Traction algorithmique/LLM :** Définit le point d'équilibre entre les deux charges. Plus l'algorithme est puissant, plus le LLM devient anecdotique. Moins l'algorithmique est puissant, plus le LLM doit être robuste.

## Suppression des biais liés à la langue

Modifier la couche de contrat et de perception pour supprimer le français. Les valeurs de déplacement doivent être :

- UP
- DOWN
- LEFT
- RIGHT

Les informations de perception (clefs du dictionnaire) et le prompt doivent également être basculé en Anglais.

- Est-ce que cela a un impact (à modèle équivalent) ?  

<p style="color: blue">
Aucun changement remarqué. Le player reste coincé près du monstre.
</p>

## Mise en place d'un historique des déplacements

Mettre en place l'historique des déplacements et le fournir au LLM :

- Est-ce cela influence positivement le résultat de la simulation ?

<p style="color: blue">
Cela influence positivement le résultat. Le player ne reste plus bloqué par le monstre mais l'évite.
</p>


## Equilibre de traction algorithmique/LLM

### Perception des échecs de déplacement

Actuellement, lorsque le LLM échoue sur un déplacement (ennemi ou bord de carte), il ne dispose pas de l'information et ne peut donc pas ajuster son comportement.

- Ajoutez dans la perception une information signalant que le dernier déplacement a échoué et pourquoi (bord de carte ou ennemi). Le LLM garde l'entière responsabilité de la décision, l'algorithme se contente d'informer, conformément à la définition de la charge algorithmique.
- Bonus (optionnel) : au lieu de signaler l'échec après coup, empêchez en amont le LLM de proposer une direction invalide (murs, bords) en filtrant les options avant l'appel. Expliquez en quelques lignes comment cela déplace la traction algo/LLM par rapport à la version de base.

### Améliorations

Trouvez et implémentez une ou plusieurs amélioration(s) qui permettent d'optimiser la simulation tout en conservant au mieux l'équilibre de traction.

- Justifiez en 3 lignes (README.md) ce que votre solution déplace comme charge  

<p style="color: blue">
Cette amélioration déplace vers la charge algorithmique le calcul du chemin optimal (BFS évitant obstacles et zones dangereuses), qui était auparavant une tâche implicitement demandée au LLM via le prompt ("donne-moi le plus court chemin"). Le LLM conserve cependant l'entière responsabilité de la décision finale : la suggestion n'est qu'une information supplémentaire dans la perception, jamais une contrainte, ce qui préserve l'équilibre de traction contrairement à un filtrage actif des options.
</p>

## Barème - Projet NPC Brain (/20)

| Section | Points | Critère |
|---|---|---|
| **Suppression des biais liés à la langue** | **3** | Implémentation (2) + analyse de l'impact observé (1) |
| **Historique des déplacements** | **4** | Implémentation (2) + analyse de l'impact sur le comportement (2) |
| **Perception des échecs de déplacement** | **6** | Signalement fonctionnel et exploité par le LLM (4) + clarté/pertinence de l'info fournie (2) |
| **Amélioration(s) + justification de traction** | **5** | Implémentation (3) + justification écrite de ce qui bascule vers l'algo (2) |
| **Compréhension générale algo/LLM** | **2** | Code propre, cohérent avec les définitions du sujet |
| **Total** | **/20** | |

**Bonus (+2, plafonné à 20/20)** : filtrage actif des directions invalides en amont de l'appel LLM (section "Perception des échecs"), avec justification de l'impact sur la traction.