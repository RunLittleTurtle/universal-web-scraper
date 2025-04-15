# Le plan de développement

## Philosophie Générale (Conservée et Renforcée):

*   **Hybride par Conception:** LLM = cerveau (compréhension/adaptation), Scrapers classiques = bras (extraction volume).
*   **Itératif:** Fondations P0 (avec cœur hybride + logs), puis couches P1, P2, P3.
*   **Piloté par les Tests (TDD/BDD):** Valider sélecteurs ET fallback LLM.
*   **Modulaire:** Services Objects (LLM Interaction, Fetcher, Extraction Logic, Notifier).
*   **Background Jobs (GoodJob):** `BuildStructureJob`, `ScrapeJob`, et autres tâches longues.
*   **Sécurité & Bonnes Pratiques Rails.**
*   **Hotwire:** Turbo Drive, Frames, Streams (Feedback job), Stimulus (UI), Morph (Tables?).
*   **Logging Verbeux:** Journalisation console (`Rails.logger`) systématique pour suivi/debug.
*   **Scalabilité:** Architecture pensée pour évoluer (Rails, Postgres, GoodJob, Fly.io).
*   **Clear naming convention:**  Prioritize clear naming conventions, small functions/methods, logical structure, and adherence to design patterns. This makes the code itself the primary source of truth.


## File Structure

universal_web_scraper/
├── .git/
├── .github/
│   └── workflows/
│       └── deploy.yml
├── .gitignore
├── .env                 # **IN .gitignore**
├── Gemfile
├── Gemfile.lock
├── README.md            # Links to /docs/*
├── Rakefile
├── bin/
│   ├── bundle
│   ├── rails
│   ├── setup
│   └── dev
├── config/
│   ├── application.rb
│   ├── boot.rb
│   ├── cable.yml
│   ├── database.yml
│   ├── environment.rb
│   ├── environments/
│   │   ├── development.rb
│   │   ├── production.rb
│   │   └── test.rb
│   ├── initializers/    # Holds devise.rb, good_job.rb, pagy.rb etc. after config
│   ├── locales/
│   ├── puma.rb
│   ├── routes.rb
│   ├── storage.yml
│   ├── master.key       # **IN .gitignore if repo is public**
│   └── credentials.yml.enc
├── app/
│   ├── assets/
│   │   ├── builds/
│   │   ├── config/
│   │   ├── images/
│   │   └── stylesheets/
│   │       └── application.bootstrap.scss # Correct for Bootstrap
│   ├── channels/
│   ├── controllers/
│   │   ├── application_controller.rb
│   │   ├── concerns/
│   │   ├── api/
│   │   │   └── v1/
│   │   │       ├── base_controller.rb
│   │   │       ├── projects_controller.rb # API specific
│   │   │       └── # ...
│   │   ├── dashboard_controller.rb
│   │   ├── projects_controller.rb
│   │   ├── scrape_urls_controller.rb
│   │   ├── scraped_records_controller.rb # Handles show (recDetail) & index (expandData)
│   │   ├── user_table_preferences_controller.rb
│   │   ├── settings_controller.rb         # <-- ADDED for Timezone, Notifications, API/Webhook config UI
│   │   ├── activity_logs_controller.rb    # <-- ADDED for viewing activity (P2)
│   │   └── webhook_endpoints_controller.rb # <-- ADDED for managing CRUD of webhooks (P2/P3)
│   ├── helpers/
│   ├── javascript/
│   │   ├── application.js
│   │   └── controllers/                   # Stimulus controllers live here
│   │       ├── hello_controller.js        # Default example
│   │       ├── table_selection_controller.js # Example P0
│   │       ├── progress_graph_controller.js # Example P1
│   │       ├── column_visibility_controller.js # Example P1
│   │       └── # ... more Stimulus controllers as needed
│   ├── jobs/
│   │   ├── application_job.rb
│   │   ├── build_structure_job.rb 
│   │   ├── finalize_structure_job.rb     #(Utilise user_feedback P1) 
│   │   ├── scrape_job.rb
│   │   ├── scheduled_scrape_launcher_job.rb
│   │   ├── csv_export_job.rb
│   │   ├── webhook_send_job.rb
│   │   ├── image_processing_job.rb        # P3
│   │   └── logo_enrichment_job.rb         # P2 (alternative to Service)
│   ├── mailers/
│   │   ├── application_mailer.rb
│   │   ├── scrape_completion_mailer.rb   # P3
│   │   └── admin_notification_mailer.rb  # For errors (P0 loop detect)
│   ├── models/
│   │   ├── application_record.rb
│   │   ├── concerns/
│   │   ├── user.rb
│   │   ├── project.rb
│   │   ├── scrape_url.rb    # Contient 'purpose' et 'user_feedback'
│   │   ├── project_schema.rb
│   │   ├── scraped_record.rb
│   │   ├── user_table_preference.rb
│   │   ├── activity_log.rb
│   │   ├── webhook_endpoint.rb
│   │   └── # ... other models
│   ├── services/
│   │   ├── llm_client.rb
│   │   ├── fetcher_service.rb
│   │   ├── webhook_notifier_service.rb   # P2/P3
│   │   └── logo_enricher_service.rb      # P2 (alternative to Job)
│   └── views/
│       ├── layouts/
│       │   └── application.html.erb      # Main layout
│       ├── shared/                       # Common partials (_navbar.html.erb, _flash_messages.html.erb)
│       ├── devise/                       # Standard Devise views
│       ├── dashboard/
│       │   └── index.html.erb
│       ├── projects/                     # index, show, new, _form, partials for project details
│       ├── scrape_urls/                  # Partials used within projects/show (_scrape_url.html.erb)
│       ├── scraped_records/              # index (expandData), show (recDetail), partials (_record.html.erb, _table.html.erb)
│       ├── user_table_preferences/       # Partials for column settings modal (_form.html.erb)
│       ├── settings/                     # Views for general settings (show.html.erb) <-- ADDED
│       ├── activity_logs/                # View for activity log (index.html.erb) <-- ADDED
│       ├── webhook_endpoints/            # CRUD views for webhooks (index, new, show, _form) <-- ADDED
│       ├── scrape_completion_mailer/     # Email templates <-- ADDED
│       └── admin_notification_mailer/    # Email templates <-- ADDED
├── db/
│   ├── migrate/
│   ├── schema.rb
│   └── seeds.rb
├── docs/                     # Detailed documentation
│   ├── 01_prd_business_value.md
│   ├── 02_checklist.md
│   ├── 03_core_logic.md
│   ├── 04_development_plan.md
│   ├── 05_prompts.md
│   ├── 06_sitemap.md
│   ├── 07_erd.md
│   ├── 08_definition_of_done.md
    ├── 09_style_guide.md       # Your style guide (colors, etc.)
│   └── 10 user_flows.md #1_onboarding.md/2_structure_review/3_manual_scrape/4_data_consultation/5_scheduled_scrape
├── lib/
│   ├── assets/
│   └── tasks/
├── log/
├── public/
├── storage/
├── test/                     # Or `spec/`
├── tmp/
├── vendor/
└── fly.toml

## Plan de Développement Détaillé (Approche Hybride + Revue Interactive + Logging)

### Phase 0 : Fondations et Configuration (Pré-requis Techniques - P0)

*   **1. Initialisation du Projet Ruby on Rails:**
    *   `- Implémentation:` `rails new universal_web_scraper --database=postgresql -j esbuild --css bootstrap`, config DB, Init Git.
    *   `- Log:` `Rails.logger.info "Rails project initialized. Version: #{Rails.version}"`
*   **2. Ajout des Gems Essentielles:**
    *   `- Implémentation:` `devise`, `hotwire-rails`, `good_job`, `pg`, `dotenv-rails`, `httparty`/`faraday`, `nokogiri`, `mechanize`, `capybara`, `selenium-webdriver`, `pagy`. `bundle install`.
    *   `- Log:` `Rails.logger.info "Essential gems loaded: Devise, GoodJob, Nokogiri, Pagy..."`
*   **3. Configuration de Base:**
    *   `- Implémentation:` `rails g devise:install`, `rails g devise User`. Configurer GoodJob (`rails g good_job:install`, migrer). Configurer `dotenv`. Mettre en place framework de test.
    *   `- Log (Initializer ou `config/application.rb`):`
        *   `Rails.logger.info "Application starting in #{Rails.env} mode."`
        *   `Rails.logger.info "Background job adapter: GoodJob (Postgres backend)."`
        *   `Rails.logger.info "LLM API Key Loaded: #{ENV['LLM_API_KEY'].present?}"`
        *   `Rails.logger.info "Database connection established."`
*   **4. Modèles de Base & Relations (P0):**
    *   `- Implémentation:`
        *   Créer modèles `User`, `Project`.
        *   Créer modèle `ScrapeUrl` avec colonnes: `url:string`, `status:string` (statuts initiaux : `pending`, `active`, `inactive`, `error`), `project_id:references`, `purpose:text` (obligatoire, décrit l'objectif du scrape).
        *   Créer modèle `ProjectSchema` avec colonnes: `columns:jsonb`, `item_container_selector:string`, `field_selectors:jsonb`, `schema_version:integer`, `project_id:references`.
        *   Créer modèle `ScrapedRecord` avec colonnes: `project_id:references`, `scrape_url_id:references`, `project_schema_id:references`, `data:jsonb`, `unique_hash:string`, `raw_item_html:text`.
        *   Définir relations (`belongs_to`, `has_many`), exécuter migrations, ajouter index (`status`, `unique_hash`, FKs), ajouter validation `presence: true` sur `ScrapeUrl.purpose`.
    *   `- Log:`
        *   `Rails.logger.info "Database schema migrated successfully (P0 Models). Models: User, Project, ScrapeUrl(with purpose), ProjectSchema, ScrapedRecord."`
*   **5. Configuration Fly.io:**
    *   `- Implémentation:` `flyctl` install, `fly launch`, config secrets (DB, Rails Master Key, LLM Key), adapter `fly.toml`, deploy initial.
    *   `- Log:` (Via `flyctl` et logs Fly.io).

---

### Phase 1 : Cœur Fonctionnel & Revue Interactive (P0 Core + P1 Review Flow)

*   **1. Service d'Interaction LLM (`LlmClient`):**
    *   `- Implémentation (P0 - Méthode `analyze_for_schema_and_selectors`):` Prend HTML et le `purpose` (obligatoire, depuis `ScrapeUrl.purpose`). Prompt demande schema/sélecteurs guidé par cet objectif initial.
    *   `- Log (P0 - Méthode `analyze_...`):`
        *   `Rails.logger.debug "[LlmClient] Calling analyze_for_schema_and_selectors..."`
        *   `Rails.logger.info "[LlmClient] Using purpose '#{purpose.truncate(50)}' to guide analysis."`
        *   `Rails.logger.info "[LlmClient] Sending request to LLM API for schema/selector analysis. Model: #{configured_model}, Prompt length approx: #{prompt.length}"`
        *   `Rails.logger.info "[LlmClient] Received response from LLM API. Status: #{response.code}"`
        *   `Rails.logger.error "[LlmClient] LLM API Error: #{error.message}. Response: #{response.body}"`
        *   `Rails.logger.debug "[LlmClient] analyze_for_schema_and_selectors completed. Found item selector: '#{result[:item_container_selector]}'. Columns: #{result[:columns]&.keys&.join(', ')}"`
    *   `- Implémentation (P0 - Méthode `extract_data_for_item`):` Prend HTML item, schema. Prompt fallback.
    *   `- Log (P0 - Méthode `extract_...`):`
        *   `Rails.logger.debug "[LlmClient] Calling extract_data_for_item for a single item..."`
        *   `Rails.logger.info "[LlmClient] Sending request to LLM API for fallback data extraction. Item HTML length: #{item_html.length}"`
        *   (Logs après appel similaires à `analyze_...`)
    *   `- Implémentation (P0 - Méthode `diagnose_scraping_issue`):` Prend logs erreur, HTML. Prompt diagnostic.
    *   `- Log (P0 - Méthode `diagnose_...`):`
        *   `Rails.logger.debug "[LlmClient] Calling diagnose_scraping_issue..."`
        *   (Logs avant/après appel similaires)
        *   `Rails.logger.info "[LlmClient] LLM diagnosis received: #{diagnosis_text}"`
    *   `- Implémentation (P1 - Méthode `refine_schema_from_feedback`):` Prend HTML original, `proposed_data` (`ScrapeUrl`), et `user_feedback` (optionnel, depuis `ScrapeUrl.user_feedback` - pour les *corrections*). Si `user_feedback` est présent, nouveau prompt LLM pour ajuster la proposition initiale basé sur les corrections demandées. Retourne structure JSON finale (soit raffinée, soit l'originale si pas de feedback).
    *   `- Log (P1 - Méthode `refine_...`):`
        *   `Rails.logger.debug "[LlmClient] Calling refine_schema_from_feedback..."`
        *   `Rails.logger.info "[LlmClient] Sending request to LLM API for schema refinement. Proposed keys: #{proposed_data.keys}, Corrections Feedback Provided: #{user_feedback.present?}"`
        *   `Rails.logger.info "[LlmClient] Received response from LLM API (refinement). Status: #{response.code}"` (Seulement si appelé)
        *   `Rails.logger.error "[LlmClient] LLM API Refinement Error: #{error.message}. Response: #{response.body}"` (Seulement si appelé et erreur)
        *   `Rails.logger.debug "[LlmClient] refine_schema_from_feedback completed. Final schema columns: #{result[:columns]&.keys&.join(', ')}"`

*   **2. Modifications Modèle `ScrapeUrl` (P1):**
    *   `- Implémentation (P1):`
        *   Créer une migration pour ajouter les colonnes `proposed_data:jsonb`, `user_feedback:text` (ce champ est pour les *corrections optionnelles*).
        *   Mettre à jour la définition des statuts pour inclure `analyzing`, `pending_review`, `building_final`, `needs_rebuild`.
        *   Mettre à jour le code du modèle (`enum` ou constantes) pour refléter les nouveaux statuts.
    *   `- Log (P1 - Migration):`
        *   `Rails.logger.info "Database schema migrated successfully (P1 ScrapeUrl additions: proposed_data, user_feedback for corrections, new statuses)."`

*   **3. Job "Build Initial Structure" (`BuildStructureJob` - P1):**
    *   `- Implémentation (P1):`
        *   Input: `scrape_url_id`.
        *   Récupère `ScrapeUrl` (incluant le champ `purpose`).
        *   Change status `scrape_url` -> `analyzing`.
        *   Fetch HTML via `FetcherService`.
        *   Appel `LlmClient.analyze_for_schema_and_selectors(html, scrape_url.purpose)`.
        *   Parse la réponse LLM.
        *   Stocke la réponse complète (schéma, sélecteurs, etc.) dans `scrape_url.proposed_data`.
        *   *Optionnel mais recommandé:* Tenter d'extraire 1-2 items d'exemple et les stocker dans `scrape_url.proposed_data['examples']`.
        *   Change status `scrape_url` -> `pending_review`.
        *   Broadcast le statut `pending_review` via Turbo Streams.
        *   Termine le job.
    *   `- Log (P1):`
        *   `Rails.logger.info "[BuildStructureJob(#{job.job_id})] Starting P1 initial analysis for ScrapeUrl ID: #{scrape_url_id}"`
        *   `Rails.logger.info "[BuildStructureJob(#{job.job_id})] Using Purpose: '#{scrape_url.purpose.truncate(80)}'"`
        *   `Rails.logger.info "[BuildStructureJob(#{job.job_id})] Fetching initial HTML for URL: #{scrape_url.url}"`
        *   `Rails.logger.info "[BuildStructureJob(#{job.job_id})] HTML fetched (Size: #{raw_html.size} bytes). Status: #{response_status}"` (Ou `.error`)
        *   `Rails.logger.info "[BuildStructureJob(#{job.job_id})] Calling LLMClient.analyze_for_schema_and_selectors..."`
        *   `Rails.logger.info "[BuildStructureJob(#{job.job_id})] LLM analysis complete."` (Ou `.error`)
        *   `Rails.logger.info "[BuildStructureJob(#{job.job_id})] Storing proposed schema and examples in ScrapeUrl ID: #{scrape_url_id}. Proposed columns: #{proposed_data['columns']&.keys&.join(', ')}"`
        *   `Rails.logger.info "[BuildStructureJob(#{job.job_id})] Setting status to 'pending_review' for ScrapeUrl ID: #{scrape_url_id}."`
        *   `Rails.logger.info "[BuildStructureJob(#{job.job_id})] Broadcasting 'pending_review' status via Turbo Streams."`
        *   `Rails.logger.info "[BuildStructureJob(#{job.job_id})] P1 initial analysis job completed successfully."` (Ou `.error` dans `rescue`)

*   **4. Service de Fetch (`FetcherService` - P0):**
    *   `- Implémentation (P0):` Méthode `fetch(url)`. `Mechanize`, gestion erreurs.
    *   `- Log (P0):`
        *   `Rails.logger.debug "[FetcherService] Fetching URL: #{url} using Mechanize..."`
        *   `Rails.logger.info "[FetcherService] Successfully fetched URL: #{url}. Status: #{response.code}, Size: #{body.size}"`
        *   `Rails.logger.error "[FetcherService] Failed to fetch URL: #{url}. Error: #{error.class} - #{error.message}"`

*   **5. Job "Finalize Structure" (`FinalizeStructureJob` - P1):**
    *   `- Implémentation (P1):`
        *   Input: `scrape_url_id`.
        *   Change status `scrape_url` -> `building_final`.
        *   Récupère `scrape_url` (chargeant `proposed_data` et `user_feedback` (corrections)).
        *   Récupère le HTML original.
        *   Si `scrape_url.user_feedback.present?`, appel `LlmClient.refine_schema_from_feedback(html, proposed_data, scrape_url.user_feedback)`. Sinon, utiliser `proposed_data` directement comme structure finale.
        *   Parse la structure finale obtenue.
        *   Crée ou met à jour le `ProjectSchema` du projet associé (incrémente `schema_version`).
        *   Change status `scrape_url` -> `active` (si succès) ou `error` (si échec LLM ou parsing).
        *   Broadcast le statut final (`active` ou `error`) via Turbo Streams.
        *   Termine le job.
    *   `- Log (P1):`
        *   `Rails.logger.info "[FinalizeStructureJob(#{job.job_id})] Starting schema finalization for ScrapeUrl ID: #{scrape_url_id}."`
        *   `Rails.logger.info "[FinalizeStructureJob(#{job.job_id})] User corrections provided: #{scrape_url.user_feedback.present?}"`
        *   `Rails.logger.info "[FinalizeStructureJob(#{job.job_id})] Calling LLMClient.refine_schema_from_feedback..."` (Si feedback présent)
        *   `Rails.logger.info "[FinalizeStructureJob(#{job.job_id})] LLM refinement complete."` (Si appelé et succès)
        *   `Rails.logger.debug "[FinalizeStructureJob(#{job.job_id})] Parsed final LLM response. Validation passed."` (Si appelé et succès)
        *   `Rails.logger.info "[FinalizeStructureJob(#{job.job_id})] Saving FINAL ProjectSchema version: #{new_schema_version} for Project ID: #{project.id}"`
        *   `Rails.logger.info "[FinalizeStructureJob(#{job.job_id})] Setting status to '#{final_status}' for ScrapeUrl ID: #{scrape_url_id}."`
        *   `Rails.logger.info "[FinalizeStructureJob(#{job.job_id})] Broadcasting final status '#{final_status}' via Turbo Streams."`
        *   `Rails.logger.info "[FinalizeStructureJob(#{job.job_id})] Schema finalization job completed successfully."` (Ou `.error` dans `rescue`)

*   **6. Job "Scrape Now" (`ScrapeJob` - P0 / adapté P1):**
    *   `- Implémentation (P0):` Logique hybride (sélecteurs + fallback LLM), gestion erreurs répétitives/timeout global, déduplication, broadcast progression.
    *   `- Implémentation (Adaptations P1):`
        *   Avant de traiter une `ScrapeUrl`, vérifier son `status`. Ne traiter que si `active`. Si autre (`pending`, `pending_review`, `analyzing`, `building_final`, `error`, `needs_rebuild`), logger et ignorer pour ce run.
        *   Si la logique d'auto-adaptation détecte une dérive (> X% selector failures):
            *   Changer le `scrape_url.status` à `needs_rebuild`.
            *   Enqueue `BuildStructureJob.perform_later(scrape_url.id)`.
    *   `- Log (P0/P1):`
        *   _...(Conserver tous les logs P0 définis précédemment)..._
        *   `Rails.logger.warn "[ScrapeJob(#{job.job_id})] Skipping URL ID: #{scrape_url.id}. Status is '#{scrape_url.status}', expected 'active'."`
        *   `Rails.logger.warn "[ScrapeJob(#{job.job_id})] High selector failure rate (...) for URL ID: #{scrape_url.id}. Marking as 'needs_rebuild' and enqueuing BuildStructureJob."`

---

### Phase 2 : Interface Utilisateur & Fonctionnalités de Base (MVP UI - P0 + Review UI - P1)

*   **1. Authentification UI (P0):**
    *   `- Implémentation (P0):` `rails g devise:views`. Style. Routes.
    *   `- Log (P0):` (Logs Devise).
*   **2. Gestion des Projets (CRUD Basique - P0):**
    *   `- Implémentation (P0):` `ProjectsController` (new, create, show, index). Vues Hotwire.
    *   `- Log (P0):`
        *   `Rails.logger.info "[ProjectsController] User #{current_user&.id || 'Guest'} accessed #{action_name} action."`
        *   (Create/Update/Destroy) `Rails.logger.info "[ProjectsController] User #{current_user&.id} #{action_name}d Project ID: #{@project.id}"`
*   **3. Page `projects#show` (Affichage Initial & Actions - P0/P1):**
    *   `- Implémentation (P0):`
        *   Afficher détails projet.
        *   Formulaire `form_with` (dans Turbo Frame?) pour ajouter `ScrapeUrl` au projet:
            *   Champ URL (obligatoire).
            *   Champ `textarea` pour `purpose` (obligatoire) avec libellé: "Quel est le but principal du scraping de cette URL ? Ex: Extraire les offres d'emploi, les prix des produits, ...".
        *   Lister les `ScrapeUrl` associées (url, status P0).
        *   Bouton "Lancer Scrape Manuel" (déclenche `ScrapeJob` pour le projet).
        *   Bouton "Analyser Structure" par URL (déclenche `BuildStructureJob` P1).
    *   `- Implémentation (P1 - Routes & Controller Action):`
        *   `config/routes.rb`: Ajouter `resources :scrape_urls, only: [] do member { post :submit_review } }`.
        *   Créer/Modifier `ScrapeUrlsController` pour avoir une action `submit_review`:
            *   Trouve `ScrapeUrl` par `params[:id]`.
            *   Met à jour `scrape_url.user_feedback` avec `params[:user_feedback]` (ou un nom similaire du formulaire).
            *   Enqueue `FinalizeStructureJob.perform_later(scrape_url.id)`.
            *   Répond avec succès (ex: `head :ok` ou Turbo Stream).
    *   `- Log (P1 - Controller `submit_review`):`
        *   `Rails.logger.info "[ScrapeUrlsController#submit_review] User #{current_user&.id} submitted corrections feedback for ScrapeUrl ID: #{params[:id]}."`
        *   `Rails.logger.info "[ScrapeUrlsController#submit_review] Stored corrections feedback length: #{scrape_url.user_feedback.length}"`
        *   `Rails.logger.info "[ScrapeUrlsController#submit_review] Enqueuing FinalizeStructureJob for ScrapeUrl ID: #{params[:id]}."`
    *   `- Implémentation (P1 - Logique d'Affichage `projects#show` - Affichage Statuts & Review):`
        *   La section listant les `ScrapeUrl` doit afficher les statuts P1 (`analyzing`, `pending_review`, `building_final`, `needs_rebuild`, etc.) avec des badges/textes clairs.
        *   Utiliser `turbo_frame_tag` pour chaque `ScrapeUrl` listée (`dom_id(scrape_url)`).
        *   Pour chaque `ScrapeUrl`, si `scrape_url.status == 'pending_review'`:
            *   Afficher une section "Revue de la Structure" à l'intérieur du `turbo_frame_tag`.
            *   Bloc de Révision contient :
                *   Le schéma proposé (`scrape_url.proposed_data['columns']`).
                *   Les sélecteurs proposés (`...['item_container_selector']`, etc.).
                *   Les exemples extraits (`...['examples']`).
                *   Un formulaire (`form_with model: scrape_url, url: submit_review_scrape_url_path(scrape_url), method: :post`) contenant :
                    *   Un champ `textarea` pour `user_feedback` avec le libellé : "**Corrections ou champs manquants ? (Optionnel)** Ex: Ajouter le salaire, Ignorer la date, ...".
                    *   Un bouton "Confirmer et Finaliser la Structure".
        *   Assurer l'écoute des **Turbo Streams** broadcastés par les jobs pour mettre à jour le contenu des `turbo_frame_tag`.
    *   `- Log (P0/P1 - Controller Actions pour `BuildStructureJob`):`
        *   `Rails.logger.info "[ProjectsController] User #{current_user&.id} triggered BuildStructureJob for ScrapeUrl ID: #{scrape_url.id}"`
    *   `- Log (P0 - Controller Actions pour `ScrapeJob`):`
        *   `Rails.logger.info "[ProjectsController] User #{current_user&.id} triggered ScrapeJob for Project ID: #{project.id}"`

*   **4. Visualisation des Données (Table Basique - P0):**
    *   `- Implémentation (P0):` Table paginée `ScrapedRecord`, tri simple.
    *   `- Log (P0):` (Logs erreurs rendu).
*   **5. Page de Détail d'Enregistrement (P0):**
    *   `- Implémentation (P0):` Route, `ScrapedRecordsController#show`, vue page complète.
    *   `- Log (P0):` (Logs Controller).
*   **6. Sélection & Suppression en Masse (P0):**
    *   `- Implémentation (P0):` Checkboxes, Stimulus, `ScrapedRecordsController#bulk_delete`, Turbo Stream.
    *   `- Log (P0):` (Logs Controller).
*   **7. Option Déduplication (UI - P0):**
    *   `- Implémentation (P0):` Checkbox `projects#edit`.
    *   `- Log (P0):` (Logs Controller).

---

### Phase 3 : Améliorations Haute Priorité (Fonctionnalités P1 Post-Review)

*   **1. Planification Simple (Scheduling - Daily - P1):**
    *   `- Implémentation:` UI `schedule`, tâche cron GoodJob, `ScheduledScrapeLauncherJob`.
    *   `- Log (Scheduler Job):`
        *   `Rails.logger.info "[ScheduledScrapeLauncherJob(#{job.job_id})] Starting scheduled scrape check..."`
        *   `Rails.logger.info "[ScheduledScrapeLauncherJob(#{job.job_id})] Found #{projects_to_scrape.count} projects scheduled for daily scraping."`
        *   `Rails.logger.info "[ScheduledScrapeLauncherJob(#{job.job_id})] Enqueued ScrapeJob for Project ID: #{project.id}"`
*   **2. Tableau de Bord (Dashboard - P1):**
    *   `- Implémentation:` `DashboardController`, calcul stats, cache, vue.
    *   `- Log (Controller):`
        *   `Rails.logger.info "[DashboardController] User #{current_user&.id} accessed dashboard."`
        *   `Rails.logger.debug "[DashboardController] Calculating dashboard stats... Cache hit: #{cache_hit}"`
        *   `Rails.logger.info "[DashboardController] Dashboard stats calculation complete."`
*   **3. Personnalisation de la Table (Colonnes P1):**
    *   `- Implémentation:` Modèle `UserTablePreference` (`user`, `project`, `visible_columns:jsonb`, `column_order:jsonb`). Interface (dropdown/modale "Colonnes" sur table) avec Stimulus (checkboxes pour visibilité, drag-and-drop pour ordre). POST/PATCH vers `UserTablePreferencesController` pour sauvegarder. Adapter rendu table `projects#show` pour lire préférences.
    *   `- Log (Controller):` `Rails.logger.info "[UserTablePreferencesController] User #{current_user&.id} updated table preferences for Project ID: #{project.id}. Visible: #{prefs.visible_columns.join(',')}, Order: #{prefs.column_order.join(',')}"`
*   **4. Activation / Désactivation d'URL (P1):**
    *   `- Implémentation:` Bouton toggle, `ScrapeUrlsController#update` changeant `status: 'active'/'inactive'`. Turbo Stream. Adapter `ScrapeJob`.
    *   `- Log (Controller):` `Rails.logger.info "[ScrapeUrlsController#update] User #{current_user&.id} updated status of ScrapeUrl ID: #{@scrape_url.id} to '#{@scrape_url.status}'"`
*   **5. Page de Données Étendue ("DB View" - P1):**
    *   `- Implémentation:` Route, controller/action (`ScrapedRecordsController#index` ou dédié), vue table non paginée.
    *   `- Log:` `Rails.logger.warn "[ProjectsController#show] Loading all records for 'Real DB' view for Project ID: #{project.id}. Record count: #{record_count}"`
*   **6. Graphique de Progression (UI P1):**
    *   `- Implémentation:` Zone dédiée, Stimulus abonnée aux Turbo Streams (`ScrapeJob`, `BuildStructureJob`, `FinalizeStructureJob`), màj UI.
    *   `- Log (Stimulus Controller):` `console.debug('[ProgressGraphController] Connected. Subscribed to stream.')`, `console.debug('[ProgressGraphController] Received stream message:', event.detail)`, `console.debug('[ProgressGraphController] Updated progress to X%, status: Y')`

---

### Phase 4 : Fonctionnalités Moyenne Priorité (Fonctionnalités P2)

*   **1. API d'Intégration (REST - P2):**
    *   `- Implémentation:` Namespace API, Controllers, Serializers, Auth token, Actions CRUD/déclenchement, Doc OpenAPI.
    *   `- Log (Tagged Logging API):`
        *   `Rails.logger.tagged("API", "V1") { logger.info "Request: #{request.method} #{request.path} from IP: #{request.remote_ip} User/Token: #{current_api_user&.id || 'token_present?'}" }`
        *   `Rails.logger.tagged("API", "V1") { logger.info "Response: Status #{response.status}" }`
*   **2. Webhooks (Notifications Push API - P2/P3):**
    *   `- Implémentation:` Modèle `WebhookEndpoint`, UI config, `WebhookNotifierService`, `WebhookSendJob`, Signature HMAC.
    *   `- Log (WebhookSendJob):`
        *   `Rails.logger.info "[WebhookSendJob(#{job.job_id})] Sending '#{event_name}' to endpoint ID: #{endpoint.id} (URL: #{endpoint.target_url})"`
        *   `Rails.logger.info "[WebhookSendJob(#{job.job_id})] Webhook response status: #{response.code}"`
        *   `Rails.logger.error "[WebhookSendJob(#{job.job_id})] Webhook failed for endpoint ID: #{endpoint.id}. Error: #{error.message}"`
*   **3. Export CSV (P2):**
    *   `- Implémentation:` Bouton, `CsvExportJob`, génération CSV, ActiveStorage, Notification lien.
    *   `- Log (CsvExportJob):`
        *   `Rails.logger.info "[CsvExportJob(#{job.job_id})] Starting export for Project ID: #{project_id} / User: #{user_id}"`
        *   `Rails.logger.info "[CsvExportJob(#{job.job_id})] Exported #{row_count} rows successfully."`
        *   `Rails.logger.info "[CsvExportJob(#{job.job_id})] Job completed. File available at: #{file_url}"`
*   **4. Journal d'Activité (P2):**
    *   `- Implémentation:` Gem `public_activity` ou Modèle `ActivityLog`, enregistrement actions, page consultation.
    *   `- Log (Création activité):` `Rails.logger.debug "[ActivityLogger] Recording activity: '#{action}' by User #{user_id} on #{target&.class&.name || 'System'} ##{target&.id}"`
*   **5. Préférences Fuseau Horaire (P2):**
    *   `- Implémentation:` Champ `user.time_zone`, form settings, `around_action`.
    *   `- Log (ApplicationController):` `Rails.logger.debug "[ApplicationController] Setting time zone to '#{Time.zone.name}' for User #{current_user.id}"`
*   **6. Filtrage Table (Basique P2) / Recherche Contextuelle (P2):**
    *   `- Implémentation:` Formulaire simple, adaptation query, Turbo Frame.
    *   `- Log:` `Rails.logger.debug "[ProjectsController#show] Applying basic filter: Column '#{col}', Query: '#{query}'"`
*   **7. Enrichissement Logos (Emplois - P2):**
    *   `- Implémentation:` Condition 'Emploi', Job ou Service (`LogoEnrichmentJob`/`Service`), APIs Google/Clearbit, stockage URL logo, affichage.
    *   `- Log (Job/Service):`
        *   `Rails.logger.info "[LogoEnricher(#{job.job_id})] Starting for ScrapedRecord ID: #{record.id}"`
        *   `Rails.logger.info "[LogoEnricher(#{job.job_id})] Attempting Google Favicon API..."`
        *   `Rails.logger.info "[LogoEnricher(#{job.job_id})] Attempting Clearbit Logo API..."`
        *   `Rails.logger.info "[LogoEnricher(#{job.job_id})] Logo URL found and saved: #{logo_url || 'None'}"`
*   **8. Programmation Avancée (Scheduling - Weekly, Monthly - P2):**
    *   `- Implémentation:` Options `schedule`, UI, Adaptation `ScheduledScrapeLauncherJob`.
    *   `- Log (Scheduler Job):` Distinguer daily/weekly/monthly.
*   **9. Sélection Plage de Dates Scraping (P2):**
    *   `- Implémentation:` Champs date projet, filtrage post-extraction.
    *   `- Log (ScrapeJob):` `Rails.logger.info "[ScrapeJob(#{job.job_id})] Applying date range filter: #{start_date} - #{end_date}. Records before filter: #{count1}, after: #{count2}."`

---

### Phase 5 : Fonctionnalités Avancées & Basse Priorité (Fonctionnalités P3)

*   **1. Gestion & Stockage des Images (Optionnel P3):**
    *   `- Implémentation:` Option projet, `ImageProcessingJob`, téléchargement, redim., upload ActiveStorage, stockage réf.
    *   `- Log (ImageProcessingJob):`
        *   `Rails.logger.info "[ImageProcessor(#{job.job_id})] Starting for image URL: #{image_url} (Record ID: #{record.id})"`
        *   `Rails.logger.info "[ImageProcessor(#{job.job_id})] Image downloaded. Resizing..."`
        *   `Rails.logger.info "[ImageProcessor(#{job.job_id})] Uploading to ActiveStorage/S3..."`
        *   `Rails.logger.info "[ImageProcessor(#{job.job_id})] Image processing complete. Reference stored."`
*   **2. Filtres Avancés (Type Coda.io - P3):**
    *   `- Implémentation:` UI complexe règles, backend query JSONB/Ransack, Turbo Frame.
    *   `- Log:` `Rails.logger.debug "[ProjectsController#show] Applying advanced filters for Project ID: #{project.id}. Filter rules: #{filter_params.to_json}"`
*   **3. Recherche Globale (P3):**
    *   `- Implémentation:` Barre recherche, controller, `pg_search`/`searchkick`, résultats groupés.
    *   `- Log:` `Rails.logger.info "[GlobalSearchController] User #{current_user&.id} searched for: '#{query}'. Engine: #{search_engine}. Found: #{results.count} results."`
*   **4. Tags & Statuts (P3):**
    *   `- Implémentation:` Gem `acts-as-taggable-on` ou `tags:jsonb`, champ `status`, UI modification (modale/masse), Turbo Streams.
    *   `- Log (Controller):` `Rails.logger.info "[ScrapedRecordsController] User #{current_user&.id} updated tags/status for Record ID: #{record.id}. New tags: #{record.tags_list.join(',')}. New status: #{record.status}"`
*   **5. Support Multi-Utilisateurs (Si requis - P3):**
    *   `- Implémentation:` Modèle `Organization`, relations, scoping, Pundit.
    *   `- Log:` Inclure `organization_id`.
*   **6. Notifications Email (Envoi P3):**
    *   `- Implémentation:` `ActionMailer`, `deliver_later` fin `ScrapeJob`.
    *   `- Log (Callbacks Mailer):`
        *   `Rails.logger.info "[ActionMailer] Email '#{mail.subject}' queued for delivery to #{mail.to.join(', ')} for Job ID: #{job_id}."`
        *   `Rails.logger.info "[ActionMailer] Email '#{mail.subject}' delivered successfully to #{mail.to.join(', ')}."`
*   **7. Modèles de Scraping Parallèles (Optimisation Avancée P3):**
    *   `- Implémentation:` Lancer Mechanize + Selenium, comparer/fusionner.
    *   `- Log:` Spécifiques.
*   **8. Personnalisation des Champs (Modale - P3):**
    *   `- Implémentation:` Stocker prefs user (modèle ou JSON user), adapter rendu modale détail.
    *   `- Log (Controller sauvegarde):` `Rails.logger.info "[UserModalPreferencesController] User #{current_user&.id} updated modal preferences for Project ID: #{project.id} / Global. Visible fields: #{prefs.visible_fields.join(',')}"`

---

## Considérations Clés Finales:

*   **Niveaux de Log:** `.debug`, `.info`, `.warn`, `.error`. Ajustable par env.
*   **Sensibilité:** **PAS** de clés API, mots de passe, PII dans les logs. Utiliser des **Secrets**
*   **Performance Prod:** Logs `info`+ en prod. Services agrégation (Fly Log Drain). Format JSON?
*   **Contexte Logs:** Inclure IDs (Job, User, Project, URL, Record) !
*   **Indexation DB:** CRUCIAL pour scalabilité. Index FK, `unique_hash`, potentiellement GIN sur `data`.
*   **Tests:** Couvrir logique métier, extraction sélecteurs, fallback LLM, gestion erreurs/timeouts.