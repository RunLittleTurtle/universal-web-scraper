# LLM Prompt Reference for `LlmClient` (Core Logic Prompt)

**Objective:** This document provides sample prompts designed for interacting with an external LLM API within the `LlmClient` service of the Universal Web Scraper application. These prompts cover the essential functions for structure analysis, fallback data extraction, and error diagnosis defined in Phase 1 of the development plan.

**Usage:** These prompt templates should be used by the Ruby code (within the corresponding `LlmClient` methods) by replacing the placeholders (`[PLACEHOLDER]`) with the appropriate dynamic data (HTML, objectives, errors, etc.) before sending the request to the LLM API.

---

## 1. Prompt for `LlmClient#analyze_for_schema_and_selectors`

### Usage Context

Called by `BuildStructureJob` during the initial analysis of a URL or when re-analysis is requested (status `needs_rebuild`). Aims to dynamically define how to scrape a page.

### Example Calling Code (Conceptual)

```ruby
# Inside BuildStructureJob
raw_html = FetcherService.fetch(scrape_url.url)
user_objective = project.objective
# existing_schema = project.project_schemas.order(version: :desc).first # For updates
prompt = generate_analyze_prompt(raw_html, user_objective, existing_schema) # Uses the template below
response_json = LlmClient.analyze_for_schema_and_selectors(prompt)
# ... (parsing and saving the result)
```

### Prompt Template Text

    # ROLE: Expert Web Scraping Analyst
    
    # TASK:
    # Analyze the provided HTML content for a website where the user's objective is "[USER_OBJECTIVE]".
    # Your goal is to determine the structure of the relevant data and provide the necessary CSS selectors for automated and efficient extraction.
    
    # INPUTS:
    # 1.  User Objective: "[USER_OBJECTIVE]" (E.g., "Job listings", "E-commerce products with prices", "Tech blog articles", "Used cars listed")
    # 2.  HTML Content of the Page:
    #     ```html
    #     [PAGE_HTML]
    #     ```
    #     *(Note: The HTML may be partial or cleaned if the page is very large)*
    # 3.  (Optional - For Updates) Existing Schema:
    #     ```json
    #     [EXISTING_SCHEMA_JSON]
    #     // If provided, consider this schema as a basis for adaptation.
    #     ```
    
    # INSTRUCTIONS:
    # 1.  Identify the Repeating Item Container: Find the most reliable and precise CSS selector that encloses *each individual instance* of the main element to be extracted (e.g., a `div` for a job listing, an `li` for a product).
    # 2.  Define the Data Schema: Propose a simple JSON structure for the relevant data to be extracted, based on the user objective (and the existing schema if provided). Include column names (JSON keys) and their likely data type (e.g., `string`, `number`, `date`, `url`, `image_url`). Focus on the most important information.
    # 3.  Identify Relative Field Selectors: For *each* column defined in your schema, provide the most precise CSS selector that allows extracting this specific data *within* the item container identified in step 1. These selectors MUST be relative to the container.
    
    # EXPECTED OUTPUT FORMAT (Strict JSON):
    # Please provide your response exclusively in the form of a valid JSON object with the following keys:
    # ```json
    # {
    #   "item_container_selector": "CONTAINER_CSS_SELECTOR",
    #   "columns": {
    #     "column_name_1": "data_type_1",
    #     "column_name_2": "data_type_2",
    #     // ... other columns
    #   },
    #   "field_selectors": {
    #     "column_name_1": "RELATIVE_CSS_SELECTOR_1",
    #     "column_name_2": "RELATIVE_CSS_SELECTOR_2",
    #     // ... selectors for each column
    #   }
    # }
    # ```
    
    # IMPORTANT:
    # *   Ensure that the `field_selectors` are indeed relative to the `item_container_selector`.
    # *   Provide only the JSON as the response. No explanatory text before or after.

*(Note: The example HTML and JSON within the prompt text are still shown using triple backticks for clarity *within the prompt itself*, but the entire prompt block is enclosed using 4-space indentation which should render consistently in Typora)*

---

## 2. Prompt for `LlmClient#extract_data_for_item`

### Usage Context

Called by `ScrapeJob` when an attempt to extract data using the pre-defined CSS selectors (obtained via prompt #1) fails for a specific HTML item. This is the fallback mechanism.

### Example Calling Code (Conceptual)

```ruby
# Inside ScrapeJob, loop over items
item_html = item_node.to_html
schema_columns = project_schema.columns # E.g., {"title"=>"string", "price"=>"number"}

# If selector extraction fails or is invalid...
prompt = generate_extract_prompt(item_html, schema_columns) # Uses the template below
fallback_data_json = LlmClient.extract_data_for_item(prompt)
# ... (use fallback_data_json for this item)
```

### Prompt Template Text

    # ROLE: Targeted Data Extractor
    
    # TASK:
    # Extract the information specified by the `TARGET_SCHEMA` from the provided `ITEM_HTML_SNIPPET`.
    # This is a fallback attempt because standard extraction via CSS selectors failed for this item.
    
    # INPUTS:
    # 1.  HTML Snippet of the Item:
    #     ```html
    #     [ITEM_HTML_SNIPPET]
    #     ```
    # 2.  Target Schema (Expected columns - JSON format):
    #     ```json
    #     [TARGET_SCHEMA_COLUMNS_JSON]
    #     // Example: {"job_title": "string", "company_name": "string", "location": "string", "posted_date": "date"}
    #     ```
    
    # INSTRUCTIONS:
    # 1.  Analyze the `ITEM_HTML_SNIPPET`.
    # 2.  For each key (column name) present in the `TARGET_SCHEMA_COLUMNS_JSON`, extract the corresponding value from the HTML snippet.
    # 3.  If a value for a specific schema key is not found or cannot be reliably extracted, return `null` for that key.
    
    # EXPECTED OUTPUT FORMAT (Strict JSON):
    # Please provide your response exclusively in the form of a valid JSON object containing the extracted data.
    # The keys of this object must exactly match the keys from `TARGET_SCHEMA_COLUMNS_JSON`.
    # ```json
    # {
    #   "column_name_1": "extracted_value_1 or null",
    #   "column_name_2": "extracted_value_2 or null",
    #   // ... other schema columns
    # }
    # ```
    
    # IMPORTANT:
    # *   Return only the JSON. No explanations.
    # *   Strictly adhere to the keys defined in `TARGET_SCHEMA_COLUMNS_JSON`.

---

## 3. Prompt for `LlmClient#diagnose_scraping_issue`

### Usage Context

Called by `ScrapeJob` within a `rescue` block or after detecting a threshold of repetitive errors (e.g., Timeout, network errors, Nokogiri errors) for a specific URL. Aims to get diagnostic help for the administrator.

### Example Calling Code (Conceptual)

```ruby
# Inside ScrapeJob, rescue block for a URL
rescue Timeout::Error, Net::ReadTimeout => e
  # ... (logic for counting repetitive errors)
  if error_count > MAX_RETRIES
    error_message = e.message + "\n" + e.backtrace.first(5).join("\n")
    html_context = # ... (potentially problematic HTML, if available)
    prompt = generate_diagnose_prompt(scrape_url.url, error_message, html_context) # Uses template below
    diagnosis = LlmClient.diagnose_scraping_issue(prompt)
    # ... (Notify admin with diagnosis, mark URL as error)
  end
end
```

### Prompt Template Text

    # ROLE: Expert Web Scraping Debugger
    
    # TASK:
    # Analyze the provided error information regarding a scraping task that failed repeatedly on a specific URL.
    # Provide a likely diagnosis of the root cause and suggest concrete corrective actions.
    # The suspected issue could potentially be an infinite loop, a repetitive timeout, or a persistent parsing error.
    
    # INPUTS:
    # 1.  Affected URL: "[URL]"
    # 2.  Error Message(s) / Logs:
    #     ```text
    #     [ERROR_MESSAGE_OR_LOG_SNIPPET]
    #     // Example: "Timeout::Error: execution expired after 60 seconds" OR "Nokogiri::XML::SyntaxError: Tag not finished" OR "Net::ReadTimeout with #<TCPSocket:(closed)>" (on multiple attempts)
    #     ```
    # 3.  (Optional) HTML Context (can be the full page or problematic section if identified):
    #     ```html
    #     [HTML_CONTEXT]
    #     // If not provided, indicate "Not available".
    #     ```
    
    # INSTRUCTIONS:
    # 1.  Examine the error messages and HTML context (if provided) in relation to the URL.
    # 2.  Identify the most likely cause of the repetitive failure. Consider possibilities such as:
    #     *   Misconfigured CSS selectors (container or fields) leading to loops or missing data.
    #     *   Anti-scraping protection (CAPTCHA, IP blocking, behavioral detection, unhandled dynamic loading).
    #     *   Content rendered heavily by JavaScript that is not captured by the current fetch tool (e.g., Mechanize).
    #     *   Invalid or severely malformed HTML structure causing recurring parsing errors.
    #     *   Significant changes on the target page since the last structure analysis.
    #     *   Intermittent network issues or restrictive server configurations on the target side.
    # 3.  Provide a clear and concise explanation of the most likely diagnosis.
    # 4.  Suggest concrete technical steps to resolve the issue or investigate further (e.g., "Check/modify CSS selector '[suspected_selector]', it seems too generic.", "Attempt using a browser-based fetcher (Selenium/Puppeteer) for this URL as complex JS rendering is suspected.", "Manually inspect the HTML structure at the URL via browser Developer Tools.", "Increase the timeout for this URL / Add delays between requests.", "Consider User-Agent or Proxy rotation.", "Flag the URL for a full structure re-analysis via `BuildStructureJob`.").
    
    # EXPECTED OUTPUT FORMAT (Clear Text):
    # Respond in plain text, clearly structuring your diagnosis and suggestions.
    # Example Response Structure:
    #
    # Likely Diagnosis:
    # [Explain the most probable cause of the repetitive error here, based on the provided information.]
    #
    # Suggestions for Correction / Investigation:
    # 1.  [Specific action / check 1]
    # 2.  [Specific action / check 2]
    # 3.  [Other relevant action or debugging step]




# 