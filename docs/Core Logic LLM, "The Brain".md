# Core Logic LLM, "The Brain"

Vous avez tout à fait raison de soulever ce point, c'est une préoccupation très légitime et absolument cruciale pour la viabilité économique et la performance de l'application ! L'idée d'utiliser le LLM pour traiter *chaque ligne individuelle* parmi des milliers serait effectivement prohibitive en termes de coûts et de temps d'exécution.

Le plan initial, tel que décrit, impliquait un peu trop implicitement l'utilisation du LLM pour l'extraction sur l'ensemble du contenu à chaque `Scrape Now`. **Clarifions et optimisons cela pour une approche beaucoup plus réaliste et rentable.**

## Approche Optimisée (Hybride: LLM pour la structure/adaptation, Selectors pour le volume):

L'objectif est de combiner la puissance d'adaptation du LLM avec l'efficacité des scrapers traditionnels (Nokogiri) pour l'extraction en volume.

1. `Build Data Structure` (Phase Initiale - Rôle Étendu du LLM):

   *   Le LLM analyse le HTML initial et l'objectif du projet.
   *   Il définit le schéma (colonnes, types).
   *   **NOUVEAU / PRÉCISION :** Il doit *aussi* identifier :
       *   **Le Sélecteur du Conteneur d'Item :** Quel élément HTML englobe chaque enregistrement individuel (par exemple, le `div` qui contient toutes les infos d'UNE offre d'emploi, ou d'UNE voiture listée).
       *   **Les Sélecteurs Relatifs des Champs :** Pour chaque colonne définie dans le schéma, quel est le sélecteur CSS ou XPath *relatif au conteneur d'item* qui permet de cibler la donnée (par exemple, `h2.job-title`, `.price-tag`, `img.vehicle-image[src]`).
   *   Ces sélecteurs (conteneur et relatifs) sont stockés, par exemple dans le `ProjectSchema` ou sur le modèle `ScrapeUrl`. C'est le **plan d'extraction** généré par l'IA.

2. `Scrape Now` (Exécutions Futures - Rôle Principal des Selectors, LLM en Supervision/Fallback):

   *   **Étape 1 : Fetch HTML Complet :** Récupérer le contenu à jour de la page (avec Mechanize, Selenium si besoin). Stocker ce HTML brut pour la vue "Données Brutes".
   *   **Étape 2 : Localiser les Conteneurs :** Utiliser Nokogiri et le `Sélecteur du Conteneur d'Item` stocké pour trouver *tous* les éléments HTML correspondant aux enregistrements individuels sur la page (potentiellement des centaines ou milliers).
   *   **Étape 3 : Extraction par Sélecteurs (pour chaque conteneur) :**
       *   Pour chaque `conteneur_html` trouvé :
           *   Utiliser Nokogiri et les `Sélecteurs Relatifs des Champs` stockés pour extraire les données spécifiques (titre, prix, date, etc.) *à l'intérieur de ce conteneur*.
           *   Construire l'objet `data` pour cet enregistrement.
   *   **Étape 4 : Validation & Fallback LLM (Crucial & Économique) :**
       *   **Option A (Validation Globale/Échantillon) :**
           *   Après avoir extrait quelques enregistrements (disons les 5-10 premiers) via les sélecteurs : **Valider** ces enregistrements. Est-ce que toutes les colonnes attendues sont présentes ? Les formats semblent-ils corrects ?
           *   **Si la validation réussit :** On suppose que le plan d'extraction (sélecteurs) est toujours valide pour cette page. Continuer à utiliser les sélecteurs Nokogiri pour les 990+ enregistrements restants. **=> Pas d'appel LLM ici, coût minimal.**
           *   **Si la validation échoue :** C'est le signal que la structure du site a probablement changé. **ICI, on fait intervenir le LLM**:
               *   Soit on envoie le HTML complet (ou juste la partie concernée) au LLM avec le schéma et on lui demande de **ré-analyser et fournir de NOUVEAUX sélecteurs** (un re-`Build`).
               *   Soit (plus coûteux mais plus direct), on demande au LLM d'**extraire les données** pour tous les enregistrements de cette page (fallback complet).
       *   **Option B (Validation par Item + Fallback Ciblé - Préférable) :**
           *   Pour *chaque* `conteneur_html` où l'extraction par sélecteur a été tentée :
               *   Valider *cet enregistrement spécifique*.
               *   **Si la validation réussit :** Super, on garde les données extraites par Nokogiri.
               *   **Si la validation échoue pour cet item :** Envoyer **UNIQUEMENT le HTML de ce petit conteneur** au LLM avec le schéma, et lui demander d'extraire les données juste pour *cet item*. **=> Coût LLM limité aux items qui posent problème.** Si un grand pourcentage échoue (>10-20%?), on peut déclencher une ré-analyse complète (Option A).
   *   **Étape 5 : Déduplication & Sauvegarde :** Traiter les données (validées par sélecteur ou extraites par LLM en fallback) pour la déduplication et sauvegarder les `ScrapedRecord`.

## Avantages de cette approche Hybride :

*   **Coût Maîtrisé :** Le LLM n'est sollicité que lors du `Build` initial, ou lors des `Scrape Now` *uniquement si* les sélecteurs échouent (ce qui indique un changement de site). La vaste majorité des extractions se fait via Nokogiri, qui est rapide et gratuit.
*   **Performance :** L'extraction via sélecteurs est beaucoup plus rapide que via LLM.
*   **Adaptabilité Conservée :** Le système peut détecter les changements (via l'échec des sélecteurs) et utiliser l'intelligence du LLM pour s'adapter ou extraire les données malgré le changement.
*   **Flexibilité :** On peut ajuster le seuil de déclenchement du fallback LLM.

## Conclusion:

Oui, le plan doit absolument intégrer cette optimisation. **Le LLM est essentiel pour le `Build Data Structure` afin de créer le schéma ET le plan d'extraction (sélecteurs). Pour les `Scrape Now`, les outils de scraping traditionnels (Nokogiri en tête) font le gros du travail en appliquant ces sélecteurs. Le LLM n'intervient en `Scrape Now` que comme mécanisme de validation ou de fallback ciblé lorsque les sélecteurs ne fonctionnent plus.**

Cela rend le projet beaucoup plus réaliste économiquement tout en conservant l'intelligence adaptative comme filet de sécurité. Le plan de développement détaillé précédent devra être ajusté pour refléter cette logique (stockage des sélecteurs, étapes de validation dans `ScrapeJob`, logique de fallback).



Okay, I understand the issue with Typora and nested or complex code fences. Let's try a different approach using **indented code blocks** for the main prompt text. Markdown generally handles indented code blocks (4 spaces indentation) very reliably across different editors, including Typora.

I will also make the sub-sections *within* the prompt template (like INSTRUCTIONS, EXPECTED OUTPUT) clearer using comments (`#`) inside the indented block, rather than Markdown headers, to avoid any potential formatting conflicts.

Here's the revised document structure: