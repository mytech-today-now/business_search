# Export Businesses Using Overpass (No Google API)

A web-based tool to generate mailing lists of potential clients within a specified geographic radius, built and maintained by [myTech.Today](https://mytech.today), an MSP in Illinois.

---

## Purpose

This application page allows users to:

- **Define a geographic search radius** (in miles) around a central U.S. ZIP code.
- **Select from a set of predefined business categories** (e.g., “lawyers,” “florists,” “hospitals,” etc.).
- **Add custom categories** (key:value pairs) when needed.
- **Fetch business data from OpenStreetMap’s Overpass API** (no API key required).
- **Parse results into CSV files** containing fields such as name, address, phone, email, and website.
- **Download individual CSVs** or bundle all CSVs into a single ZIP file via the JSZip library.
- Quickly assemble mailing lists of potential clients for direct marketing, outreach, or data analysis purposes.

---

## How It Works

1. **User Input**  
   - User specifies a ZIP code (default: 60010, Barrington, IL) and a radius in miles (default: 30 mi).  
   - The page uses a (hardcoded) latitude/longitude lookup for the default ZIP code. In a production environment, this could be replaced with a geocoding service to validate and translate any valid ZIP code to coordinates.

2. **Category Selection**  
   - A list of checkboxes is generated from a JavaScript object called `CATEGORIES` (preset categories) and `ADDITIONAL_CATEGORIES`.  
   - Each category consists of a filename (e.g., `lawyers.csv`) and one or more filter objects (e.g., `{ key: "office", value: "lawyer" }`).  
   - Users can select or unselect entire “key groups” (e.g., all `office` categories) or individual categories.  
   - A “Select/Unselect All” toggle checkbox is provided at the top of the category list.

3. **Custom Category Addition**  
   - An “Add Custom Category” form allows the user to specify:
     - `Key` (e.g., `amenity`, `shop`, `office`)
     - `Value` (e.g., `cafe`, `bakery`)
     - `Filename` (e.g., `cafes.csv`)  
   - On submission, the page validates that all fields are present and that the filename ends with `.csv`.  
   - If valid, the custom category is stored in `localStorage` under `userCategories` so that it persists across sessions.  
   - The category list is immediately re-rendered to include the new entry.

4. **Building the Overpass Query**  
   - For each selected category, the script:
     - Concatenates all filter clauses into an Overpass QL snippet, e.g.:  
       ```ql
       node["office"="lawyer"](around:48280,42.1543,-88.1362);
       way["office"="lawyer"](around:48280,42.1543,-88.1362);
       relation["office"="lawyer"](around:48280,42.1543,-88.1362);
       ```
     - Wraps these in a complete Overpass query:
       ```ql
       [out:json][timeout:25];
       (
         node["office"="lawyer"](around:48280,42.1543,-88.1362);
         way["office"="lawyer"](around:48280,42.1543,-88.1362);
         relation["office"="lawyer"](around:48280,42.1543,-88.1362);
       );
       out center tags;
       ```

5. **Fetching & Parsing**  
   - The page sends the POST request to the Overpass API endpoint (`https://overpass-api.de/api/interpreter`) with the query string.  
   - It receives a JSON response containing OSM elements (nodes, ways, relations).  
   - For each returned element, the script extracts standard address or contact tags (e.g., `name`, `addr:housenumber`, `addr:street`, `addr:city`, `contact:phone`, `contact:email`, etc.) via the helper function `parseOsmTags()`.  
   - If an element lacks either a `name` or any address details, it is skipped.

6. **CSV Generation**  
   - A header row is defined as:
     ```
     Name,Street Number,Street Name,City,State,ZIP+4,Phone Number,Fax Number,Email Address,Website,Contact Name(s)
     ```
   - Each valid element becomes one CSV row, with values properly escaped via `escapeCSV()` to handle commas, quotes, or newlines.  
   - If no valid rows are found (only header exists), the CSV is not generated, and a log message indicates skipping.  
   - Otherwise, a Blob is created from the CSV string, and a download link (`<a download="filename.csv">`) is appended to the DOM.

7. **ZIP Bundling**  
   - After all individual CSV download links are rendered, the user can click “Download All as ZIP.”  
   - Using [JSZip](https://stuk.github.io/jszip/), the script fetches each Blob URL, adds it to a ZIP archive, generates the ZIP blob (`generateAsync`), and auto-triggers a download of `business_data.zip`.

8. **Logging & UI Feedback**  
   - A `<div id="log">` displays timestamps and status messages for each step (querying Overpass, number of elements retrieved, errors, completion).  
   - Buttons are disabled/enabled appropriately (e.g., “Run Export” is disabled while running, “Download All” is disabled until after export completes).

9. **“The Story of This Page” Popup**  
   - A branded overlay modal (“The Story of This Page”) includes a chronological list of development steps, ChatGPT/Augment iterations, GitHub link, and the author’s signature/contact info.  
   - This dialog traps focus, can be closed via ESC or “X” button, and is toggled by clicking “The story of this Page” in the top navigation.

---

## Tech Stack

- **HTML5**  
  - Semantic markup, accessible `<header>`, `<nav>`, `<div>` containers.
- **CSS3**  
  - Flexbox for layout (`.main-nav`, `.key-groups-container`), scrollable lists, branded colors (`#004B8D` for myTech.Today).  
  - Custom styles for popup overlay, form, buttons, and responsive category grid.
- **JavaScript (ES6+)**  
  - DOM manipulation, event handling, async/await for fetch requests, modular IIFEs (Immediately Invoked Function Expressions).
- **OpenStreetMap Overpass API**  
  - No authentication—simply POST Overpass QL to `https://overpass-api.de/api/interpreter`.
- **JSZip**  
  - Client-side ZIP generation (v3.10.1 via CDN).
- **LocalStorage**  
  - Persistence of user-defined categories under the key `userCategories`.
- **Browser-native Blob & URL APIs**  
  - `new Blob([...], { type: "text/csv" })`, `URL.createObjectURL()` for dynamic CSV download links.
- **ARIA / Accessibility**  
  - `aria-label` for the navigation, focus trapping inside the modal, `role="dialog"` implicit in structure.

---

## Functions & Objects

Below is an organized overview of main JavaScript functions, objects, and their responsibilities.

### 1. Configuration & Constants

- `MILES_TO_METERS`  
  → Numeric conversion constant (1 mi = 1,609.34 m).
- `LAT`, `LNG`, `ZIP_CODE`  
  → Default coordinates and ZIP code for 60010 (Barrington, IL).
- `RADIUS_MILES`, `RADIUS`  
  → Current search radius in miles and meters (initialized to 30 mi → ~48,280 m).
- `CSV_HEADER`  
  → Array defining CSV column headers.
- `CATEGORIES` (Object)  
  → Preset categories (filename → `[ { key, value }, … ]`).  
  - Example:  
    ```js
    "lawyers.csv": [ { key: "office", value: "lawyer" } ]
    ```
- `ADDITIONAL_CATEGORIES` (Object)  
  → Supplemental list of “leisure,” “shops,” “tourism,” etc.

### 2. Storage Helper Functions

- `loadUserCategories()`  
  - Retrieves and parses `localStorage.getItem("userCategories")`.  
  - Returns `{}` if empty or on error.
- `saveUserCategories(userCategories)`  
  - Stores the provided object as JSON under `localStorage.setItem("userCategories", ...)`.  
  - Returns `true`/`false` based on success.

### 3. UI Initialization

- `initializeCategoryCheckboxes()`  
  - Clears `#allCategories` container.  
  - Calls `buildAllCategories()` to merge `CATEGORIES + ADDITIONAL_CATEGORIES + userCategories`.  
  - Groups by key via `groupCategoriesByKey()`.  
  - Renders categories into the DOM through `populateCategoriesByKey()`.  
  - Configures checkbox event listeners via `setupCheckboxEventListeners()`.

- `buildAllCategories()`  
  - Returns a merged object:  
    ```js
    { ...CATEGORIES, ...ADDITIONAL_CATEGORIES, ...loadUserCategories() }
    ```

- `groupCategoriesByKey(categoriesObj)`  
  - Accepts an object of `filename → filters[]`.  
  - Groups entries by the first `filter.key` (e.g., “office,” “amenity,” “shop,” etc.).  
  - Returns an alphabetically sorted object:  
    ```js
    { amenity: [ { filename, filters }, … ], office: [ … ], … }
    ```

- `populateCategoriesByKey(groupedCategories, container)`  
  - Creates a `.key-groups-container` flexbox.  
  - Loops through each `key` (e.g., “amenity”).  
    1. Builds a `.key-group` `<div>`.  
    2. Adds a header with a “Select all” checkbox (data-key attribute).  
    3. Creates `.key-items` container and splits into columns based on item count.  
    4. Inserts individual `.category-item` checkboxes (data-key, data-filename).

### 4. Checkbox Handling

- `setupCheckboxEventListeners()`  
  - “Select All Categories” (`#selectAllCategories`) → checks/unchecks all `.category-checkbox` & `.select-key`.
  - “Select Key” (`.select-key[data-key]`) → checks/unchecks all `.category-checkbox[data-key]`.
  - Individual `.category-checkbox` → calls `updateKeyGroupCheckbox(key)` and `updateSelectAllCheckbox()`.

- `updateKeyGroupCheckbox(key)`  
  - Finds the “group” checkbox for that key (`.select-key[data-key="${key}"]`).  
  - Sets its `checked` and `indeterminate` states based on child `.category-checkbox[data-key="${key}"]`.

- `updateSelectAllCheckbox()`  
  - Updates the top-level “Select All” checkbox’s state based on whether all or some `.category-checkbox` elements are checked.

### 5. Category Addition

- `addNewCategory()`  
  1. Reads values from `#newKeyInput`, `#newValueInput`, `#newFilenameInput`.  
  2. Validates:
     - All fields present.
     - Filename ends with `.csv`.
     - Filename not already defined in merged categories.
  3. If valid, appends the new entry to `userCategories` (via `saveUserCategories()`).  
  4. Clears the form, hides error, re-initializes categories, logs a success message.

- `showError(message)` / `hideError()`  
  - Displays/hides `#addCategoryError` with the provided text.

### 6. Overpass Query & Data Processing

- `buildOverpassQuery(filters)`  
  - Accepts an array of objects: `{ key, value? }`.  
  - Generates a combined filter string:
    - If `value` is present: `["amenity"="cafe"]`  
    - If only `key`: `["aeroway"]`  
  - Wraps in Overpass QL syntax for nodes, ways, and relations within `RADIUS` and `LAT,LNG`. Returns a trimmed multiline string.

- `fetchOverpass(query)` (async)  
  - Sends POST request to `https://overpass-api.de/api/interpreter` with body `data=<query>`.  
  - Returns parsed JSON or throws an Error.

- `parseOsmTags(tags = {})`  
  - Extracts standard tags:
    - `name`  
    - `addr:housenumber`, `addr:street`, `addr:city`, `addr:state`, `addr:postcode`  
    - `contact:phone` / `phone`  
    - `contact:fax` / `fax`  
    - `contact:email` / `email`  
    - `contact:website` / `website`  
  - Returns an object with properties:  
    ```js
    {
      name,
      streetNumber,
      streetName,
      city,
      state,
      postcode,
      phone,
      fax,
      email,
      website,
      contacts: "" // placeholder
    }
    ```

- `escapeCSV(value)`  
  - If `value` contains commas, double quotes, or newlines, it wraps it in quotes and doubles existing quotes.  
  - Returns a safe CSV field string.

- `generateCSVString(rows)`  
  - Joins each array of fields (`rows`) with commas and `\r\n` as row separators.

- `processCategory(filename, filters)` (async)  
  1. Logs “Starting category: `filename`.”  
  2. Builds Overpass QL via `buildOverpassQuery(filters)`.  
  3. Calls `fetchOverpass(query)`.  
  4. Iterates over `data.elements`; for each element:
     - Parses tags via `parseOsmTags()`.  
     - Skips if missing both `name` and address fields.  
     - Pushes a row `[ name, streetNumber, streetName, city, state, postcode, phone, fax, email, website, contacts ]` into `rows`.  
  5. If no data rows beyond header, logs skipping.  
  6. Otherwise:
     - Generates CSV content (`generateCSVString(rows)`).  
     - Creates a `Blob`, obtains a Blob URL, and appends a download link `<a download="filename">` to `#downloads`.  
     - Logs “Finished category: `filename` (`rowCount` rows).”

- `delay(ms)`  
  - Returns a Promise that resolves after `ms` milliseconds (used to throttle Overpass requests).

### 7. Runner & ZIP Creation

- `runExport()` (async)  
  1. Clears `#log` and `#downloads`.  
  2. Collects all checked `.category-checkbox`.  
     - If none, logs a warning and returns.  
  3. Disables “Run Export” and “Download All” buttons.  
  4. Logs “=== Export Started (Overpass) ===”.  
  5. Re-builds merged categories via `buildAllCategories()`.  
  6. Iterates sequentially over each selected checkbox:
     - Extracts `filename` and `filters`.  
     - Calls `await processCategory(filename, filters)`.  
     - Awaits a short delay (`1000 ms`) to avoid hammering Overpass.  
  7. Logs “=== Export Finished. Download links are above. ===”.  
  8. Re-enables “Run Export” and calls `enableDownloadAllButton()`.

- `enableDownloadAllButton()`  
  - Removes the disabled state from the “Download All as ZIP” button.

- `downloadAllFiles()` (async)  
  1. Logs “=== Creating ZIP file with all CSVs ===”.  
  2. Queries all `<a class="download-link">` elements in `#downloads`.  
     - If none, logs a warning and returns.  
  3. Instantiates `const zip = new JSZip()`.  
  4. For each link:
     - Fetches its Blob URL, obtains the Blob via `response.blob()`.  
     - Adds it to `zip` via `zip.file(filename, blob)`.  
  5. Calls `zip.generateAsync({ type: "blob" })`, awaits the resulting ZIP blob.  
  6. Creates a temporary `<a download="business_data.zip">` link, clicks it, then removes it.  
  7. Logs “=== ZIP file downloaded ===”.

### 8. “Story of This Page” Popup

- An independent IIFE handles the modal logic:
  - `openStory(e)`  
    - Removes `hidden-overlay` and adds `visible-overlay`.  
    - Moves focus to the dialog content.  
  - `closeStory()`  
    - Reverses classes, returns focus to the “The story of this Page” link.  
  - Adds event listeners:
    - Click on “openPageStory” → `openStory`.  
    - Click on “closePageStory” → `closeStory`.  
    - Keydown on `document` for `Escape` to close.  
    - Click on overlay background (if click target is overlay) to close.  
    - Focus trapping inside the dialog via Tab key logic.

---

## Preset Categories

Below is a consolidated list of all **preset categories** (filename → OSM filter key:value). User-added categories are stored in `localStorage` and will appear alongside these.

### Primary Categories (`CATEGORIES`)

| Filename                         | Key (OSM Tag)       | Value                                 |
|----------------------------------|---------------------|---------------------------------------|
| `child_care_centers.csv`         | amenity             | childcare                             |
| `lawyers.csv`                    | office              | lawyer                                |
| `auto_mechanic_shops.csv`        | shop                | car_repair                            |
| `therapists.csv`                 | office              | therapist                             |
| `architects.csv`                 | office              | architect                             |
| `construction_companies.csv`     | office              | contractor                            |
| `florists.csv`                   | shop                | florist                               |
| `chiropractors.csv`              | office              | chiropractor                          |
| `liquor_distributors.csv`        | shop                | alcohol                               |
| `manufacturers.csv`              | industrial          | manufacturer                          |
| `hair_salons.csv`                | shop                | hairdresser                           |
| `nail_shops.csv`                 | shop                | beauty                                |
| `auto_body_shops.csv`            | shop                | car_repair                            |
| `montessori_schools.csv`         | amenity             | school<br>school:level=pre_school     |
| `wealth_management.csv`          | office              | financial                             |
| `venture_capital.csv`            | office              | financial                             |
| `stock_brokers.csv`              | office              | financial                             |
| `medical_practices.csv`          | amenity             | clinic                                |
| `hospitals.csv`                  | amenity             | hospital                              |
| `dental_practices.csv`           | amenity             | dentist                               |
| `veterinary_clinics.csv`         | amenity             | veterinary                            |
| `nursing_homes.csv`              | amenity             | nursing_home                          |
| `assisted_living.csv`            | amenity             | social_facility                       |
| `pharmaceutical_companies.csv`    | shop                | pharmacy                              |
| `manufacturing_industrial.csv`    | industrial          | manufacturer                          |
| `auto_dealerships.csv`           | shop                | car                                   |
| `banks_financial.csv`            | amenity             | bank                                  |
| `accounting_firms.csv`           | office              | accountant                            |
| `insurance_agencies.csv`         | office              | insurance                             |
| `real_estate_agencies.csv`       | office              | real_estate                           |
| `educational_institutions.csv`   | amenity             | school                                |
| `daycare_centers.csv`            | amenity             | childcare                             |
| `nonprofits.csv`                 | office              | non_profit_organization               |
| `government_agencies.csv`        | office              | government                            |
| `retail_stores.csv`              | shop                | (any shop)                            |
| `restaurants.csv`                | amenity             | restaurant                            |
| `hotels_motels.csv`              | tourism             | hotel                                 |
| `logistics_transport.csv`        | office              | logistics                             |
| `engineering_firms.csv`          | office              | engineering                           |
| `architecture_design.csv`        | office              | architect                             |
| `consulting_firms.csv`           | office              | consulting                            |
| `marketing_agencies.csv`         | office              | marketing                             |
| `media_production.csv`           | office              | media                                 |
| `telecom_voip.csv`               | office              | telecom                               |
| `energy_utility.csv`             | office              | utility                               |
| `gyms_fitness.csv`               | leisure             | fitness_centre                        |
| `salons_spas.csv`                | shop                | beauty                                |
| `agriculture_farming.csv`        | landuse             | farmland                              |
| `aerospace_aviation.csv`         | aeroway             | (any aeroway element)                 |
| `coworking_spaces.csv`           | office              | coworking                             |

### Additional Categories (`ADDITIONAL_CATEGORIES`)

| Filename             | Key      | Value         |
|----------------------|----------|---------------|
| `breweries.csv`      | craft    | brewery       |
| `wineries.csv`       | craft    | winery        |
| `distilleries.csv`   | craft    | distillery    |
| `cafes.csv`          | amenity  | cafe          |
| `bars.csv`           | amenity  | bar           |
| `pubs.csv`           | amenity  | pub           |
| `nightclubs.csv`     | amenity  | nightclub     |
| `cinemas.csv`        | amenity  | cinema        |
| `art_galleries.csv`  | tourism  | gallery       |
| `museums.csv`        | tourism  | museum        |
| `bookstores.csv`     | shop     | books         |
| `hardware_stores.csv`| shop     | hardware      |
| `electronics_stores.csv` | shop | electronics   |
| `furniture_stores.csv`   | shop | furniture     |
| `pet_stores.csv`         | shop | pet           |
| `bakeries.csv`           | shop | bakery        |
| `convenience_stores.csv` | shop | convenience   |
| `grocery_stores.csv`     | shop | supermarket   |
| `dry_cleaners.csv`       | shop | laundry       |
| `printing_shops.csv`     | shop | printing      |
| `tattoo_parlors.csv`     | shop | tattoo        |
| `car_rental.csv`         | amenity | car_rental  |
| `fuel_stations.csv`      | amenity | fuel        |

---

## Code Analysis

1. **Project Structure & Readability**  
   - The entire page is a single HTML file containing inline CSS and JavaScript. While convenient for distribution, consider modularizing:
     - Separate CSS into `styles.css`.
     - Encapsulate JavaScript into `app.js` (or even multiple modules: `categories.js`, `overpass.js`, `ui.js`).  
   - The code uses **IIFEs** (Immediately Invoked Function Expressions) to avoid polluting the global namespace, which is a best practice for small-scale client-side apps.

2. **Category Grouping Logic**  
   - Grouping categories by `filters[0].key` ensures that “office,” “amenity,” “shop,” etc., appear as sections.  
   - Sorting keys alphabetically improves discoverability.  
   - Dynamically adjusting the number of columns (`columns-1`, `columns-2`, `columns-3`) based on item count scales well as more categories are added.

3. **Accessibility & UX**  
   - The popup overlay uses ARIA‐like patterns (e.g., focus trapping, ESC key to close).  
   - Adding explicit `aria-label` attributes to buttons/links (e.g., `aria-label="Close"`) could further improve screen reader support.  
   - Visual feedback via the `#log` is helpful for debugging, but for large result sets, consider paginating or collapsing log lines.

4. **Error Handling & Validation**  
   - Overpass API requests use `try/catch` to log errors per category.  
   - ZIP code input is a plain text field with `pattern="[0-9]{5}"`. There’s no real geocoding fallback if the user enters an invalid ZIP. In a production version:
     - Integrate a simple geocoding service (e.g., Mapbox, Google Maps Geocoding, or a free USPS API) to translate any 5-digit ZIP to coordinates.  
     - Display a user-friendly error if coordinates are unavailable.

5. **Performance & Rate Limiting**  
   - The code imposes a 1,000 ms delay between Overpass queries to avoid Overpass server rate limits.  
   - If a user selects 30 or more categories, this could take 30 seconds or more. Potential optimizations:
     - Batch multiple filter clauses into one Overpass query when possible (e.g., search for “lawyer” AND “architect” in the same query).  
     - Implement a visual progress bar instead of a simple log.

6. **LocalStorage for Custom Categories**  
   - New categories are persisted under `userCategories` in `localStorage` as a JSON object.  
   - If the user’s browser is private/incognito or `localStorage` is disabled, adding custom categories will silently fail. Consider showing an error if `localStorage` is unavailable.

7. **CSV Generation & Download**  
   - CSV escaping (`escapeCSV()`) correctly handles commas, quotes, and newlines.  
   - Browser Blob and `URL.createObjectURL` is used properly for generating download links for each CSV.  
   - If a category yields zero valid rows, the link is not created, and a log entry is generated—this prevents empty CSVs.

8. **ZIP Creation via JSZip**  
   - Fetching Blob URLs to feed into JSZip is straightforward.  
   - Some browsers (e.g., older versions of Edge) may have quirks around `fetch` on `blob:` URLs. Consider reading the Blob directly from a variable reference instead of re-fetching.  
   - The final ZIP is automatically downloaded; cleanup logic (revoking object URLs) is minimal but could help avoid memory leaks.

9. **“Ask ChatGPT for OSM tags” Link**  
   - The function `updateChatGPTLink()` dynamically constructs a ChatGPT URL (with a prompt) based on the new category’s key/value.  
   - The prompt used is:  
     ```
     Given a business description of <Value>, what is the correct Overpass API (OpenStreetMap) key and value to use when searching for that business? Please reply with a list of valid key:value pairs, without including any code examples.
     ```
   - This workflow is clever for guiding users who don’t know OSM tags; however:
     - If ChatGPT’s interface changes, the link (`https://chat.openai.com/?prompt=…`) may break.  
     - A fallback “OSM Tag Documentation” link (e.g., https://wiki.openstreetmap.org/wiki/Map_Features) could complement this.

10. **Branding & Attribution**  
    - In CSS, `#004B8D` is used as a branded color for myTech.Today (e.g., popup header text).  
    - The “Story of This Page” includes the GitHub URL (`https://github.com/mytech-today-now/business_search`) and the myTech.Today signature with phone and email.  
    - The page header (`Export Businesses`) and popup explicitly credit Kyle Rode / myTech.Today.

---

## Branding

> **This application was created at [myTech.Today](https://mytech.today), an MSP in Illinois.**  
>  
> Licensed & maintained by Kyle Rode  
> Contact: (847) 767–4914 | [sales@mytech.today](mailto:sales@mytech.today)

---

### Potential Next Steps & Improvements

1. **Geocoding Integration**  
   - Incorporate a free geocoding service (e.g., USPS, Mapbox) so that any valid 5-digit ZIP yields accurate coordinates automatically.  
   - Provide user feedback (e.g., “Looking up coordinates for 90210…”) and handle invalid ZIP codes gracefully.

2. **Batch Query Optimization**  
   - Combine multiple categories into a single Overpass call by OR’ing filter clauses when they share the same `key`.  
   - For example, `node[amenity="hospital"]["amenity"="clinic"](around:…); …`  
   - This can dramatically reduce total Overpass requests and speed up large exports.

3. **Pagination & CSV Streaming**  
   - For very large result sets (hundreds or thousands of elements), consider streaming CSV rows directly to a writable stream instead of building a large string in memory.  
   - Or, allow the user to specify a maximum number of results per category.

4. **Enhanced Error Feedback**  
   - Display error banners in the UI if Overpass is unavailable or returns a server error.  
   - Offer retry logic with exponential backoff for transient network issues.

5. **Mobile Responsiveness**  
   - Test on various devices and screen sizes.  
   - The flex-based layout should adapt, but ensure checkboxes and form controls remain accessible and legible on narrow viewports.

6. **Deployment & Versioning**  
   - Host the static files (HTML, CSS, JS) on a CDN or S3 bucket for high availability.  
   - Tag releases in GitHub and provide a CHANGELOG in the repository for feature requests and bug fixes.

---

Thank you for using **Export Businesses Using Overpass**. We hope this tool helps you quickly build mailing lists of potential clients. For further assistance, please reach out to [myTech.Today](https://mytech.today).
