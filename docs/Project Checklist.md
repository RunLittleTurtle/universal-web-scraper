# Project Checklist

Okay, voici une checklist priorisée (P0-P3) regroupant toutes les fonctionnalités des User Stories et du PRD, organisée par page/module parent. La priorité prend en compte la valeur utilisateur fondamentale et un flux de développement logique.

## Légende des Priorités :

*   **P0 : Indispensable (MVP Core)** - Fonctionnalités absolument nécessaires pour lancer le produit et démontrer sa valeur principale. d
*   **P1 : Haute Priorité** - Fonctionnalités très importantes qui complètent l'offre de base et répondent à des besoins utilisateurs clés rapidement après le lancement.
*   **P2 : Priorité Moyenne** - Fonctionnalités utiles améliorant l'expérience ou ajoutant de la valeur significative, mais pouvant attendre une itération ultérieure.
*   **P3 : Basse Priorité** - Fonctionnalités "Nice-to-have", avancées, ou moins critiques pour le lancement initial.
*   **Les status** sont à la fin de la ligne. Le LLM et l'humain opérateur doivent ajuster le status après leur manipulations. [Backlog] [To Do] [in Progress] [Test] [In Review] [Done]

---

## Checklist de Développement Priorisée

### I. Système d'Authentification & Gestion de Compte (Auth & Account)

*   **(P0)** **Système d'Authentification complet:** Permettre aux utilisateurs de s'inscrire et de se connecter avec email + mot de passe. [In Progress]
*   **(P1)** **Gestion des Identifiants:** Permettre à l'utilisateur de changer ses informations de connexion (mot de passe).[Backlog]
*   **(P2)** **Journal d'Activité:** Afficher l'historique des actions récentes (connexions, scrapes, erreurs, etc.).[Backlog]
*   **(P2)** **Préférences Utilisateur - Fuseau Horaire:** Permettre à l'utilisateur de choisir son fuseau horaire d'affichage.[Backlog]
*   **(P3)** **Support Multi-utilisateurs:** Gérer plusieurs utilisateurs (si pertinent pour des équipes/organisations, sinon plus bas).[Backlog]

### II. Tableau de Bord (Dashboard)

*   **(P1)** **Vue Globale (Statistiques):** Afficher les indicateurs clés : # projets, # scrapers actifs/inactifs, # URLs actives/inactives, # enregistrements totaux, statut rapide des scrapes programmés.[Backlog]

### III. Gestion des Projets (Project Creation & Configuration)

*(Concerne principalement la Page "Détail d'un Projet" et la logique de création)*

* **(P0)** **Création de Projet:** Permettre à l'utilisateur de créer un projet en définissant un thème/objectif commun. [Backlog]

* **(P0)** **Ajout/Gestion d'URLs:** Permettre à l'utilisateur d'ajouter une ou plusieurs URLs à un projet. [Backlog]

* **(P0) Revue Interactive de la Structure (Purpose Input & Feedback Loop):**

  Permettre à l'utilisateur de guider l'analyse initiale ('Purpose'), puis de revoir la structure proposée par l'IA (avec exemples), et de fournir un feedback pour correction avant finalisation. [Backlog]

  - *(Détails techniques associés :)* Le LLM analyse (basé sur 'Purpose'), propose une structure. L'UI affiche la proposition/exemples. L'utilisateur soumet un feedback (`user_feedback`). Le LLM raffine si nécessaire. Le `ProjectSchema` final est créé/mis à jour. [Backlog]

* **(P0)** **Analyse Initiale & Construction de Structure (LLM - Backend)("Build Data Structure")** [Backlog]

  *   *(Action implicite avant le premier scrape)* Le LLM analyse les URLs, détermine les données pertinentes, nomme les colonnes, crée la structure JSON/table initiale, et sélectionne/configure le scraper approprié. [Backlog]
  *   *Features associées:* `LLM analyzes...`, `LLM names columns dynamically`, `LLM creates JSON dynamically`, `Platform automatically adapts table/DB structure (initial)`, `Platform automatically deploys best crawler (initial)`. [Backlog]

* **(P0)** **Lancer un Scrape Manuel ("Scrape Now"):** Permettre de déclencher un scrape immédiatement pour le projet ou des URLs spécifiques. [Backlog]

* **(P0)** **Scraping du Post Initial:** Assurer que le scraper récupère toujours le contenu original de la page, ceci sera utilile pour confimrer que les données sont correct et pour le déboggage (Données Brutes HTML) (détail technique d'implémentation). [Backlog]

* **(P0) Un système de détection du système:** Si celui-ci tourne en boucle infini. si c'est le cas, le système soit arrêter, recevoir un diagnostique LLM, envoyer une erreur à l'interface du User et envoyer un courriel à l'administrateur avec le diagnostique du LLM, afin que l'administrateur puisse faire la correction au code de l'app. [Backlog]

* **(P1)** **Activer/Désactiver une URL:** Permettre de mettre en pause le scraping pour une URL spécifique sans la supprimer. [Backlog]

* **(P1) Heure UTC:** Ajouter dans les setting de l'utilisateur la fonctionnalité de pouvoir choisir l'horloge interne de l'application. Ex: UTC Toronto.

* **(P1)** **Programmation Simple (Scheduling - Daily):** Permettre de programmer des scrapes quotidiens. [Backlog]

* **(P1)** **Suivi Visuel (Progress Graph):** Afficher une barre/graphe de progression en temps réel pour les scrapes actifs. [Backlog]

* **(P2)** **Programmation Avancée (Scheduling - Weekly, Monthly):** Ajouter des options de fréquence hebdomadaire et mensuelle. [Backlog]

* **(P2)** **Sélection d'une Plage de Dates de Scraping:** Permettre de ne scraper que le contenu publié dans un intervalle de dates spécifique (peut être complexe). [Backlog]

* **(P3)** **Configuration d'un Manifeste:** Permettre à l'utilisateur de définir des règles/limites spécifiques pour guider le scraper (fonctionnalité avancée). [Backlog]

* 

### IV. Visualisation des Données (Data Table - Project Details Page)

*   **(P0)** **Vue Table Simple (Pagination):** Afficher les données collectées dans un tableau paginé pour un chargement rapide. [Backlog]
*   **(P0)** **Fonctionnalités de Table Basiques:** Afficher les colonnes définies par l'IA, permettre le tri simple sur une colonne. [Backlog]
*   **(P1)** **Personnalisation de la Table (Affichage Colonnes):** Permettre à l'utilisateur de choisir quelles colonnes afficher/masquer. [Backlog]
*   **(P1)** **Fonctionnalités de Table Intermédiaires:** Permettre le déplacement (réorganisation) des colonnes. [Backlog]
*   **(P1)** **Vue Table Étendue ("Real DB"):** Offrir une option pour afficher toutes les lignes sans pagination (attention aux performances). [Backlog]
*   **(P2)** **Enrichissement Visuel (Logos Emplois):** Récupérer et afficher le logo de l'entreprise (via Google Favicon API, puis Clearbit API Free Tier en fallback) pour les projets de type "Emploi". [Backlog]
*   **(P2)** **Fonctionnalités de Table Avancées (Filtrage Basique):** Ajouter des options de filtrage simples par colonne. [Backlog]
*   **(P3)** **Filtres Avancés (Type Coda.io):** Implémenter un système de règles de filtrage complexes basé sur les types de colonnes/champs. [Backlog]
*   **(P3)** **Affichage Images:** Afficher les images téléchargées (si option activée au niveau projet) dans la table et/ou la modale. (Dépend de IX - Gestion Images) [Backlog]

### V. Interaction avec les Données (Lignes de Données & Page de Détail)

- **(P0)** **Page de Vue Détaillée:** Ouvrir une **page dédiée** (`/projects/:project_id/records/:id`) au clic sur une ligne, affichant tous les champs collectés pour cet enregistrement. [Backlog]
- **(P0)** **Vue des Données Brutes (Page Détail):** Inclure une section/onglet dans la **page détail** pour voir le HTML/JSON brut source ("Source of Truth"). [Backlog]
- **(P0)** **Sélection de Lignes (Checkbox):** Permettre de sélectionner une ou plusieurs lignes dans la table. [Backlog]
- **(P0)** **Action en Masse - Supprimer:** Permettre de supprimer les lignes sélectionnées. [Backlog]
- **(P1)** **Graphique de Progression:** Un graphique de progression permettant de voir l'avancement en % du scraping. Incluant les différentes phases écrite textuellement, qui sont une réflection des logs pertinent pour qu'un utilisateur comprennent à quelle étape se situe le progrès de son scraping. [Backlog]
- **(P3)** **Ajout/Gestion de Tags (par ligne & en masse):** Permettre d'ajouter/modifier des tags sur une ligne (**depuis la page détail** ou la table) et sur la sélection. [Backlog]
- **(P3)** **Ajout/Gestion de Statut (par ligne & en masse):** Permettre d'ajouter/modifier un statut sur une ligne (**depuis la page détail**) et sur la sélection. [Backlog]
- **(P3)** **Personnalisation des Champs (Page Détail):** Permettre à l'utilisateur de choisir/réorganiser les champs affichés dans la **page de vue détaillée**. [Backlog]
- **(P3)** **Action en Masse - "Change Selected List":** Permettre de marquer/démarquer des lignes comme appartenant à une liste "sélectionnée" (utilisation moins prioritaire que tags/status). [Backlog]

### VI. Gestion des Données & Export (Data Management)

*   **(P2)** **Export CSV:** Permettre d'exporter les données de la table (filtrées ou toutes) en format CSV. [Backlog]
*   **(P0)** **Déduplication ("Remove Duplicates"):** Implémenter une logique pour éviter d'ajouter des enregistrements identiques (basée sur une clé ou similarité). Ceci doit être une option dans le projet, car parfois c'est utile comme dans la recherche d'emplois, on ne veut pas avoir le même job post deux fois, mais dans el cas ou on recherche des voyages, cela reste le même posting, mais c'est le prix qui change, alors on veut quand même savoir que lOrsque l'on scrape, ce voyage est encore disponible, et à quel nouveau prix. [Backlog]

### VII. Recherche (Search)

*   **(P2)** **Recherche Contextuelle (Table):** Barre de recherche pour filtrer rapidement les données au sein de la table actuelle. [Backlog]
*   **(P3)** **Recherche Globale:** Barre de recherche permettant de chercher à travers tous les projets, URLs, et potentiellement les données collectées. [Backlog]

### VIII. Notifications

*   **(P2)** **Configuration des Notifications Email:** Permettre à l'utilisateur d'activer/configurer les notifications email pour les scrapes programmés. [Backlog]
*   **(P3)** **Réception des Notifications Email:** Envoyer un email avec un résumé du scrape et un lien vers la table lorsque le scrape programmé est terminé. [Backlog]

### IX. Moteur de Scraping & Intelligence (Core Engine - Backend/Implicit)

*(Ces éléments sont souvent transversaux ou backend, mais essentiels)*

*   **(P0)** **Adaptation Dynamique Structure (LLM):** L'IA adapte la structure de la BDD/table si de nouvelles données ou URLs le nécessitent (continu). [Backlog]
*   **(P0)** **Sélection/Déploiement Scraper (LLM):** L'IA choisit et utilise le meilleur modèle de scraper pour les URLs (continu). [Backlog]
*   **(P3)** **Modèles de Scraping Parallèles:** Exécuter plusieurs types de scrapers en simultané pour analyse comparative (optimisation avancée). [Backlog]
*   **(P3)** **Gestion & Stockage des Images (Optionnel):** Si activé par l'utilisateur pour le projet, télécharger les images associées, les redimensionner (qualité réduite), les stocker dans le cloud (ex: S3), et stocker la référence (URL cloud) avec les données. (Remplace/détaille l'ancien "Load images, URLs, cloud") [Backlog]

### X. Intégration & API (Integration & API)

- (P2)API d'Intégration: Développer une API REST (ou similaire) documentée [Backlog]
  - Accéder (Pull) aux données (projets, URLs, lignes de données, etc.). [Backlog]
  - Déclencher des actions (Push/Control) (ex: démarrer un scrape). [Backlog]
  - **(Optionnel P2/P3)** Recevoir des notifications via Webhooks. [Backlog]

---

Cette checklist fournit une base solide pour planifier vos sprints de développement, en commençant par les éléments P0 pour construire le cœur fonctionnel de l'application.