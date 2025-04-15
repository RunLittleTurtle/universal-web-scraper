# Guidelines UI - Style Guide

**Objectif Général:** Créer une interface utilisateur **simple, intuitive, cohérente et efficace**, en utilisant **nativement Bootstrap 5**, et en respectant les préférences de thèmes définies.

**I. Principes Fondamentaux:**

1. Simplicité Avant Tout:
   - Privilégier la clarté et la facilité d'utilisation. Éviter les éléments superflus.
   - **LLM Check:** *Avant d'ajouter un élément UI (bouton, lien, section), demander : "Est-ce absolument nécessaire pour la fonctionnalité ? Existe-t-il déjà un moyen d'accomplir cette action ?"*
2. Non-Redondance:
   - Chaque fonctionnalité ou action doit avoir un point d'accès principal clair. Éviter d'avoir plusieurs boutons/liens faisant exactement la même chose dans des contextes similaires.
   - **LLM Check:** *Avant d'ajouter un élément UI, vérifier dans le code de la vue/partial concerné si un élément existant remplit déjà cette fonction précise. Si oui, ne pas ajouter le nouvel élément.*
3. Cohérence Globale:
   - Utiliser les mêmes types de composants pour des actions similaires à travers l'application (ex: toujours une modale pour confirmer une suppression).
   - Respecter la structure de page et la terminologie de manière uniforme.
4. Design Orienté Action:
   - Les éléments interactifs (boutons, liens) doivent clairement indiquer leur fonction.
   - Grouper logiquement les actions liées (ex: utiliser `btn-group` de Bootstrap).
5. Progressive Disclosure:
   - Ne pas surcharger l'interface initiale. Utiliser des modales, des accordéons (`accordion`), des offcanvas, ou des liens vers des pages de détail pour les informations/actions moins fréquentes ou plus complexes.

**II. Framework & Style:**

1. Bootstrap 5 Natif:

   - Utiliser **exclusivement** les composants et les classes utilitaires de Bootstrap 5 par défaut.
   - **Interdiction:** Ne pas introduire de librairies de composants UI tierces (sauf cas *exceptionnel* validé, et si oui, elle **DOIT** hériter des styles Bootstrap).
   - **CSS Personnalisé:** Limiter au strict minimum. Si nécessaire, utiliser les variables CSS de Bootstrap pour assurer la cohérence avec les thèmes.

2. Thèmes (Dark / Light):

   - Dark Mode (Défaut):

     - Palette:

        

       S'inspirer de l'image fournie (style Synthwave/Retrowave).

       - *Fond Principal:* Très sombre (quasi-noir ou violet très profond, ex: `#1a1a2e` ou similaire).
       - *Fond Secondaire/Cartes:* Légèrement moins sombre (`#1f1f38`).
       - *Texte Principal:* Clair, mais pas blanc pur (ex: `#e0e0e0`).
       - *Accents / Actions Primaires:* Rose/Magenta vif (ex: `#ff00ff`, `#f92b7a`).
       - *Accents Secondaires / Info:* Orange/Jaune vif (ex: `#ff8c00`, `#fada5e`).
       - *Grilles / Bordures / Séparateurs:* Lignes fines de couleur néon (violet/rose léger, ex: `#a020f0`, `#ff69b4` avec opacité réduite si besoin).

     - **Contraste:** Assurer un bon contraste entre le texte et le fond.

   - Light Mode (Optionnel):

     - Palette:

        

       Classique, propre et professionnelle.

       - *Fond Principal:* Blanc (`#ffffff`).
       - *Fond Secondaire/Cartes:* Très léger gris (`#f8f9fa`).
       - *Texte Principal:* Noir ou gris très foncé (`#212529`).
       - *Accents / Actions Primaires:* Couleur primaire standard de Bootstrap (bleu) ou une couleur professionnelle sobre.
       - *Accents Secondaires / Info:* Gris moyen ou autre couleur sobre.
       - *Bordures / Séparateurs:* Gris clair (`#dee2e6`).

     - **Contraste:** Assurer un excellent contraste.

   - **Sélecteur de Thème:** Placer un bouton (icône lune/soleil) discret dans la barre de navigation ou les paramètres utilisateur pour basculer entre les modes. Utiliser les attributs `data-bs-theme` de Bootstrap 5.3+.

**III. Structure & Layout:**

1. Mise en Page Standard:

    

   Utiliser une structure cohérente :

   - Barre de Navigation (`navbar`) en haut.
   - Conteneur Principal (`container` ou `container-fluid`) pour le contenu.
   - Optionnel: Pied de page (`footer`) simple.

2. **Responsive Design:** Utiliser nativement le système de grille de Bootstrap (`row`, `col-*`) pour assurer la fluidité sur toutes les tailles d'écran.

3. **Hiérarchie Visuelle:** Utiliser les balises H1-H6, les marges (`m-*`, `p-*`), et les classes typographiques de Bootstrap pour structurer l'information.

**IV. Utilisation des Composants Bootstrap Spécifiques:**

1. Boutons (`button`, `btn`):
   - Labels clairs et concis.
   - Utiliser les couleurs sémantiques (`btn-primary`, `btn-secondary`, `btn-danger`, `btn-success`, `btn-warning`, `btn-info`). L'action principale de la page/section doit utiliser `btn-primary`.
   - Éviter un excès de boutons visibles en même temps.
2. Formulaires (`form`):
   - Utiliser les styles Bootstrap (`form-control`, `form-label`, `form-select`, `form-check`).
   - Labels toujours présents (`<label>`). Placeholders informatifs mais non suffisants comme label.
   - Regroupement logique des champs (`fieldset` ou divs). Helper text (`form-text`) si nécessaire.
   - Bouton de soumission clair (`type="submit"`).
3. Tableaux (`table`):
   - Utiliser les classes de base (`table`, `table-striped`, `table-hover`).
   - Rendre responsive (`table-responsive`).
   - Colonnes clairement nommées (`<th>`).
   - Limiter le nombre de colonnes visibles par défaut, privilégier un lien vers une page de détail pour plus d'informations.
4. Navigation (`navbar`, `nav`, `breadcrumb`):
   - `navbar` principale en haut.
   - Utiliser `nav` pour les sous-navigations si nécessaire (ex: onglets `nav-tabs`).
   - Utiliser `breadcrumb` pour indiquer la position dans la hiérarchie si > 2 niveaux.
5. Feedback Visuel (`alert`, `toast`, `spinner`):
   - Utiliser les `toast` pour les notifications non bloquantes (succès d'une action asynchrone). Facilement intégrable avec Hotwire/Turbo Streams.
   - Utiliser les `alert` pour les messages importants ou persistants.
   - Utiliser les `spinner` pour indiquer les chargements ou processus en arrière-plan.
6. Modales (`modal`):
   - À utiliser pour : confirmations d'actions destructrices, formulaires courts, affichage rapide d'informations détaillées sans quitter le contexte principal.

**V. Directives Spécifiques pour le LLM (Windsurf):**

1. **Confirmation de Contexte:** *Avant de générer le code UI pour une fonctionnalité, confirmer : "Dans quelle page/section cette UI doit-elle apparaître ? Quel est le but exact de l'utilisateur à cet endroit ?"*
2. **Priorité aux Standards:** *Toujours essayer d'implémenter la fonctionnalité en utilisant un composant Bootstrap standard avant d'envisager une solution personnalisée.*
3. **Intégration Hotwire/Turbo:** *Lors de la génération de vues et de contrôleurs, penser à l'intégration avec Hotwire. Utiliser les Turbo Frames et Turbo Streams pour des mises à jour partielles et dynamiques lorsque c'est pertinent (ex: ajout d'un item à une liste, mise à jour d'un statut, affichage d'un toast).*
4. **Accessibilité (Base):** *Utiliser les balises HTML sémantiques (nav, main, header, footer, section, article). S'assurer que les contrôles de formulaire ont des labels associés. Utiliser les attributs ARIA de base si pertinent (ex: `aria-label` pour les boutons icônes).*