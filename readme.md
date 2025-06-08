# Business Search & Export Tool

A powerful web-based application for generating targeted business mailing lists within specified geographic areas, developed by <a href="https://mytech.today" target="_blank">myTech.Today</a>, a leading Managed Service Provider (MSP) in Barrington, IL.

**This project has graduated from Beta (`v0.x`) to Production (`v1.0`). Future updates will follow [Semantic Versioning](https://semver.org/).**

---

## Purpose & Description

This web application exists to solve a critical business development challenge: **quickly and efficiently identifying potential clients within a specific geographic area**. By leveraging OpenStreetMap's comprehensive business database through the Overpass API, users can generate targeted mailing lists without the cost and complexity of traditional business directory services or Google API dependencies.

**What this webpage does for users:**

- **Define a geographic search radius** (in miles) around a central U.S. ZIP code.
- **Select from a set of predefined business categories** (e.g., “lawyers,” “florists,” “hospitals,” etc.).
- **Add custom categories** (key:value pairs) when needed.
- **Fetch business data from OpenStreetMap’s Overpass API** (no API key required).
- **Parse results into XLXS files** containing fields such as name, address, phone, email, and website.
- **Download individual XLSXs** or bundle all XLSXs into a single ZIP file via the JSZip library.
- Quickly assemble mailing lists of potential clients for direct marketing, outreach, or data analysis purposes.

## User Personas & Use Cases

### 1. **MSP Sales Representatives**
*Primary target audience*
- **Use Case**: Generate prospect lists for IT service offerings within their service area
- **Workflow**: Search for "lawyers," "medical practices," "accounting firms" within 30 miles of their office
- **Benefit**: Identify businesses that likely need IT support, cybersecurity, and cloud services

### 2. **Marketing Agencies**
*Secondary target audience*
- **Use Case**: Build client prospect databases for targeted marketing campaigns
- **Workflow**: Export multiple business categories to create comprehensive market analysis
- **Benefit**: Develop data-driven marketing strategies with accurate business contact information

### 3. **Sales Development Representatives (SDRs)**
*Frequent users*
- **Use Case**: Create cold outreach lists for B2B sales campaigns
- **Workflow**: Filter by specific business types and geographic proximity to optimize territory management
- **Benefit**: Increase conversion rates through targeted, location-based prospecting

### 4. **Local Service Providers**
*Growing user segment*
- **Use Case**: Identify potential customers for services like cleaning, landscaping, or maintenance
- **Workflow**: Search for office buildings, retail stores, and professional services in their coverage area
- **Benefit**: Build sustainable local customer base through geographic targeting

### 5. **Business Consultants**
*Professional services users*
- **Use Case**: Research market opportunities and competitive landscapes
- **Workflow**: Export comprehensive business data for market analysis and strategic planning
- **Benefit**: Make data-driven recommendations with accurate local business intelligence

### 6. **Real Estate Professionals**
*Specialized use case*
- **Use Case**: Identify commercial property prospects and market trends
- **Workflow**: Map business density and types to assess commercial real estate opportunities
- **Benefit**: Understand local business ecosystems for property development and leasing

---

## Step-by-Step Usage Instructions

### Getting Started
1. **Open the Application**: Open `index.html` in any modern browser
2. **Verify Default Settings**:
   - Check the version displayed in the header (should show current version)
   - Default location: ZIP code 60010 (Barrington, IL)
   - Default search radius: 30 miles
   - Default export format: XLSX

### Configuring Your Search
3. **Set Your Location**:
   - Enter your desired ZIP code in the input field
   - Press Enter or click away to automatically update coordinates
   - Verify the coordinates display shows your intended location

4. **Set Search Radius**:
   - Adjust the radius input field (1-100 miles)
   - Default is 30 miles - suitable for most regional business searches
   - Larger radius = more results but potentially less relevant

5. **Select Business Categories**:
   - **Individual Selection**: Check specific categories like "lawyers" or "medical_practices"
   - **Group Selection**: Use group headers (e.g., "office", "amenity") to select all categories in that group
   - **Select All**: Use the master checkbox to select/deselect all categories at once

### Adding Custom Categories
6. **Add New Groups** (if needed):
   - Click the "+" button next to "Select/Unselect All Categories"
   - Enter a group name (e.g., "healthcare", "professional_services")
   - This creates a new category group for organization

7. **Add Custom Business Types**:
   - Click the "+" button within any group section
   - Enter the business description (e.g., "veterinary clinic", "tax preparation")
   - Click "Ask ChatGPT for OSM tags" for help finding the correct OpenStreetMap tags
   - The system will generate appropriate search parameters

### Running Your Export
8. **Execute the Search**:
   - Click "Run Export" button
   - Monitor the log area for real-time progress updates
   - Wait for all selected categories to complete processing
   - Each category processes sequentially to avoid overwhelming the API

9. **Review Results**:
   - Check the log for number of businesses found per category
   - Note any categories that returned zero results
   - Verify the coordinate updates were successful

### Downloading Your Data
10. **Download Individual Files**:
    - Click individual download links for specific business categories
    - Files are available in your selected format (XLSX - default, XLS, CSV, Google Sheets, or Apple Numbers)
    - Files are named descriptively (e.g., "lawyers.xlsx", "medical_practices.xlsx")

11. **Download Complete Dataset**:
    - Click "Download all [#] files as a zip file" for a bundled download
    - The ZIP file contains all exported files in your selected format
    - This option only becomes available after running an export

### Modifying Settings
12. **Change Export Format**:
    - Use the format toggle switches in the footer settings
    - Choose from: XLSX (default), XLS, CSV, Google Sheets, or Apple Numbers
    - Settings are automatically saved to your browser

13. **Configure API Settings**:
    - Use the Settings panel in the footer to modify API keys
    - Switch between OpenStreetMap (free) and Google Maps (requires API key)
    - API keys are stored locally in your browser

### Version Management
14. **To bump the version**:
    - Open your browser's developer tools:
      - **Windows/Linux**: Press `F12`, `Ctrl+Shift+I`, or right-click → **Inspect**
      - **macOS**: Press `Cmd+Option+I` or right-click → **Inspect Element**
    - Select the **Console** tab
    - Type and run the following command:
      ```js
      localStorage.setItem("appVersion", "X.Y.Z");
      ```
      replacing `"X.Y.Z"` with your desired semantic version number (e.g., `"1.0.1"`)
    - Press **Enter** to save the new version
    - Reload the page (press `F5` or run `location.reload()` in the console)
    - The new version will appear in the header and trigger upgrade notifications

15. **Troubleshooting**:
    - Consult the browser console (F12 → Console tab) for error messages
    - Check network connectivity for API access
    - Verify ZIP code format (5 digits) for coordinate lookup
    - Clear browser cache if experiencing persistent issues

---

## How It Works

1. **User Input**
   - User specifies a ZIP code (default: 60010, Barrington, IL) and a radius in miles (default: 30 mi).
   - The page uses the Census Geocoding API to convert ZIP codes to latitude/longitude coordinates automatically.

2. **Category Selection**
   - A list of checkboxes is generated from a JavaScript object called `CATEGORIES` (preset categories) and `ADDITIONAL_CATEGORIES`.
   - Each category consists of a filename (e.g., `lawyers.xlsx`) and one or more filter objects (e.g., `{ key: "office", value: "lawyer" }`).
   - Users can select or unselect entire “key groups” (e.g., all `office` categories) or individual categories.
   - A “Select/Unselect All” toggle checkbox is provided at the top of the category list.

3. **Custom Category Addition**
   - An “Add Custom Category” form allows the user to specify:
     - `Key` (e.g., `amenity`, `shop`, `office`)
     - `Value` (e.g., `cafe`, `bakery`)
   - On submission, the page validates that all fields are present and that the filename ends with `.xlsx`.
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

6. **File Export Generation**
   - **Default Format**: XLSX files are generated by default, with support for multiple export formats (XLSX, XLS, CSV, Google Sheets, Apple Numbers).
   - **Header Row Definition**: A header row is defined as:
     ```
     Name,Street Number,Street Name,City,State,ZIP+4,Phone Number,Fax Number,Email Address,Website,Contact Name(s)
     ```
   - **Data Processing**: Each valid table element becomes a data row with values escaped via `escapeCSV()` to handle commas, quotes, or newlines.
   - **Empty File Handling**: If only the header exists, file generation is skipped and the system logs `"Skipping empty CSV generation."`
   - **File Creation Process**: Otherwise, the system:
     1. Creates a `Blob` from the data using the selected export format
     2. Generates a download link (`<a download="filename.xlsx">` or appropriate extension)
     3. Appends the link to the DOM with the correct filename
     4. Ensures any previous links are removed or disabled before subsequent file builds
   - **Format Support**: Files are generated using format-specific libraries (SheetJS for XLSX, native CSV generation) with appropriate MIME types and file extensions.

7. **ZIP Bundling**
   - After all individual file download links are rendered, the user can click “Download All as ZIP.”
   - Using [JSZip](https://stuk.github.io/jszip/), the script fetches each Blob URL, adds it to a ZIP archive, generates the ZIP blob (`generateAsync`), and auto-triggers a download of `business_data.zip`.
   - The ZIP contains files in the user's selected export format (XLSX, XLS, CSV, Google Sheets, or Apple Numbers).

8. **Logging & UI Feedback**
   - A `<div id="log">` displays timestamps and status messages for each step (querying Overpass, number of elements retrieved, errors, completion).
   - Buttons are disabled/enabled appropriately (e.g., “Run Export” is disabled while running, “Download All” is disabled until after export completes).

9. **“The Story of This Page” Popup**
   - A branded overlay modal (“The Story of This Page”) includes a chronological list of development steps, ChatGPT/Augment iterations, GitHub link, and the author’s signature/contact info.
   - This dialog traps focus, can be closed via ESC or “X” button, and is toggled by clicking “The story of this Page” in the top navigation.

---

## Technology Stack

### Core Technologies

#### **Frontend Framework**
- **HTML5**: Modern semantic markup with accessibility features
  - Single-page application architecture
  - Responsive design principles
  - ARIA compliance for screen readers
  - Form validation and user input handling

#### **Styling & Layout**
- **CSS3**: Advanced styling with modern layout techniques
  - Flexbox for responsive grid layouts
  - Custom CSS variables for consistent theming
  - Branded color scheme (`#004B8D` - myTech.Today blue)
  - Mobile-responsive design patterns
  - Modal overlay and popup styling
  - Hover effects and interactive elements

#### **JavaScript Engine**
- **Vanilla JavaScript (ES6+)**: No framework dependencies
  - Async/await for API calls
  - Modular IIFE (Immediately Invoked Function Expressions)
  - DOM manipulation and event handling
  - Local storage management
  - Error handling and validation
  - Dynamic UI generation

### External APIs & Services

#### **Data Sources**
- **OpenStreetMap Overpass API**
  - Primary business data source
  - No API key required (free service)
  - Endpoint: `https://overpass-api.de/api/interpreter`
  - Supports complex geographic queries
  - Returns comprehensive business metadata

- **U.S. Census Geocoding API**
  - ZIP code to coordinate conversion
  - No authentication required
  - Endpoint: `https://geocoding.geo.census.gov/geocoder/`
  - Provides accurate latitude/longitude data
  - Fallback error handling for invalid ZIP codes

### Dependencies & Libraries

#### **Client-Side Libraries**
- **JSZip (v3.10.1)**
  - Client-side ZIP file generation
  - Loaded via CDN: `https://cdnjs.cloudflare.com/ajax/libs/jszip/3.10.1/jszip.min.js`
  - Enables bulk file download functionality (all supported formats)
  - No server-side processing required

#### **Browser APIs**
- **LocalStorage API**: Persistent user category storage
- **Blob API**: Multi-format file generation and download
- **URL API**: Dynamic download link creation
- **Fetch API**: HTTP requests to external services
- **File API**: Client-side file handling

### Architecture & Design Patterns

#### **Application Structure**
- **Single HTML File**: Self-contained application
  - Embedded CSS and JavaScript
  - No build process required
  - Easy deployment and distribution
  - Offline-capable after initial load

#### **Data Flow**
1. **User Input** → ZIP code and radius validation
2. **Geocoding** → Coordinate lookup and caching
3. **Category Selection** → Dynamic checkbox generation
4. **Query Building** → Overpass QL construction
5. **API Requests** → Sequential data fetching with rate limiting
6. **Data Processing** → Multi-format file generation and validation
7. **File Download** → Individual and bulk export options

#### **Performance Optimizations**
- **Rate Limiting**: 1-second delays between API calls
- **Caching**: LocalStorage for user preferences
- **Lazy Loading**: Dynamic content generation
- **Error Recovery**: Graceful fallbacks for API failures

### Development & Deployment

#### **Development Tools**
- **VS Code**: Primary development environment
- **Augment AI**: Code generation and refactoring
- **ChatGPT**: Initial prototyping and documentation
- **Git**: Version control and collaboration

#### **Deployment Strategy**
- **Static Hosting**: No server-side requirements
- **CDN Compatible**: Single file deployment
- **GitHub Pages**: Current hosting solution
- **Browser Compatibility**: Modern browsers (ES6+ support)

### Security & Privacy

#### **Data Handling**
- **Client-Side Processing**: No data sent to third-party servers
- **API Security**: Read-only access to public datasets
- **Local Storage**: User preferences only (no sensitive data)
- **HTTPS**: Secure connections to all external APIs

#### **Privacy Compliance**
- **No User Tracking**: No analytics or tracking scripts
- **No Data Collection**: No personal information stored
- **Transparent Operations**: All API calls visible in browser dev tools

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
   - ZIP code input is validated with `pattern="[0-9]{5}"` and geocoded using the Census Geocoding API:
     - When a valid 5-digit ZIP is entered, coordinates are automatically fetched.
     - User-friendly status messages indicate success or failure of geocoding.
     - If geocoding fails, the application falls back to default coordinates with a clear error message.

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

1. ~~**Geocoding Integration**~~
   - ✓ Implemented: Census Geocoding API now automatically converts ZIP codes to coordinates.
   - ✓ Implemented: User feedback shows geocoding status with clear success/error messages.

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

## Installation

### Prerequisites
- Modern web browser with ES6+ support (Chrome 60+, Firefox 55+, Safari 12+, Edge 79+)
- Internet connection for API access
- No additional software installation required

### Quick Start
1. **Download**: Clone or download the repository
   ```bash
   git clone https://github.com/mytech-today-now/business_search.git
   ```

2. **Open**: Launch `index.html` in your web browser
   - Double-click the file, or
   - Right-click → "Open with" → your preferred browser

3. **Use**: No build process or server setup required - the application runs entirely in your browser

### Alternative Access
- **GitHub Pages**: Visit the live demo at [https://mytech-today-now.github.io/business_search/](https://mytech-today-now.github.io/business_search/)
- **Local Server**: For development, use any local server (Live Server extension, Python's `http.server`, etc.)

---

## Usage

### Basic Workflow
1. **Set Location**: Enter your ZIP code and desired search radius
2. **Select Categories**: Choose business types from the organized category groups
3. **Add Custom Types**: Use the "+" buttons to add specialized business categories
4. **Run Export**: Click "Run Export" and monitor progress in the log area
5. **Download Data**: Get individual files in your selected format or download all as a ZIP bundle

### Advanced Features
- **Group Management**: Organize categories by type (office, amenity, shop, etc.)
- **Custom Categories**: Add business types not in the preset list
- **Bulk Operations**: Select/deselect entire category groups at once
- **Data Validation**: Automatic filtering of incomplete business records
- **Error Recovery**: Graceful handling of API timeouts and network issues

---

## Contributing

### Development Setup
1. **Fork** the repository on GitHub
2. **Clone** your fork locally
3. **Create** a feature branch: `git checkout -b feature-name`
4. **Make** your changes and test thoroughly
5. **Commit** with descriptive messages: `git commit -m "Add feature description"`
6. **Push** to your fork: `git push origin feature-name`
7. **Submit** a pull request with detailed description

### Code Standards
- **JavaScript**: ES6+ syntax, consistent formatting
- **CSS**: BEM methodology preferred, mobile-first approach
- **HTML**: Semantic markup, accessibility compliance
- **Documentation**: Update README for any new features

### Testing
- Test across multiple browsers and devices
- Verify API functionality with various ZIP codes
- Validate export file formats and content (XLSX, XLS, CSV, Google Sheets, Apple Numbers)
- Check accessibility with screen readers

---

## License

This project is licensed under the **MIT License** - see the [LICENSE](LICENSE) file for details.

### Commercial Use
- ✅ Commercial use permitted
- ✅ Modification and distribution allowed
- ✅ Private use encouraged
- ⚠️ No warranty provided

---

## Support

### Getting Help
- **Documentation**: This README contains comprehensive usage instructions
- **Issues**: Report bugs or request features via [GitHub Issues](https://github.com/mytech-today-now/business_search/issues)
- **Discussions**: Join community discussions in [GitHub Discussions](https://github.com/mytech-today-now/business_search/discussions)

### Professional Support
For business inquiries, custom development, or professional support:

**myTech.Today**
📧 [sales@mytech.today](mailto:sales@mytech.today)
📞 [(847) 767-4914](tel:18477674914)
🌐 [https://mytech.today](https://mytech.today)
📍 Barrington, IL

---

## Acknowledgments

### Technology Partners
- **OpenStreetMap**: Providing comprehensive global business data
- **U.S. Census Bureau**: Reliable geocoding services
- **JSZip**: Client-side file compression capabilities

### Development Tools
- **Augment AI**: Advanced code generation and refactoring
- **ChatGPT**: Initial prototyping and documentation assistance
- **GitHub**: Version control and project hosting
- **VS Code**: Primary development environment

### Community
- **Contributors**: Thank you to all who have contributed code, documentation, and feedback
- **Users**: Your feedback and feature requests drive continuous improvement
- **Open Source Community**: For the tools and libraries that make this project possible

---

## Versioning and Release Notes {#versioning-section}

### Version Management System

This project follows [Semantic Versioning](https://semver.org/) principles and includes a robust version management system that:

- **Tracks Application Version**: Automatically detects version changes and manages localStorage updates
- **User Notifications**: Shows upgrade notifications when new versions are deployed
- **Version Badge**: Displays current version (v1.0.1) in the application header
- **Persistent Storage**: Remembers user preferences and dismissal states across sessions

### Development Phases

#### **Beta Development** (`v0.x`)

All commits and iterations prior to v1.0 are considered **Beta Development**:

- **v0.1-v0.5**: Initial prototyping and core functionality development
  - Basic OpenStreetMap integration
  - CSV export functionality
  - Category selection system
  - Initial UI/UX design

- **v0.6-v0.9**: Feature enhancement and refinement
  - Google Maps API integration
  - Advanced export formats (XLSX, XLS, Google Sheets, Apple Numbers)
  - Custom category management
  - Responsive design improvements
  - Documentation and user experience enhancements

**Beta Development Workflow:**
1. Open `index.html` in any modern browser
2. Test core functionality with default settings
3. Report issues via GitHub Issues for rapid iteration
4. Expect frequent updates and potential breaking changes
5. Use for testing and feedback purposes only

#### **Production Development** (`v1.0+`)

Starting with v1.0, the application is considered **Production Ready**:

- **v1.0.0**: Production release with comprehensive feature set
  - Stable API integrations
  - Full export format support
  - Robust error handling
  - Complete documentation
  - Version management system

**Production Development Workflow:**
1. Open `index.html` in any modern browser
2. Verify the default version displayed in the header
3. Use the Export controls to download data
4. Modify settings or API keys via the Settings panel
5. Follow semantic versioning for updates
6. Suitable for business-critical lead generation

### How to Upgrade

When new versions are released, the application will automatically:

1. **Detect Version Changes**: Compare stored version with current application version
2. **Update localStorage**: Automatically update the stored version number
3. **Show Notifications**: Display upgrade banner with "What's New" information
4. **Preserve Settings**: Maintain all user preferences and custom categories
5. **Track Analytics**: Log version upgrade events for usage analytics

#### Manual Version Check
Users can manually check their current version by looking at the version badge in the application header (top-right of the myTech.Today logo).

#### Future Versioning
- **Patch Releases** (v1.0.1, v1.0.2): Bug fixes and minor improvements
- **Minor Releases** (v1.1.0, v1.2.0): New features and enhancements
- **Major Releases** (v2.0.0, v3.0.0): Breaking changes or significant architectural updates

---

## Changelog

### Version 2.0.0 (Current)
- ✅ Added comprehensive user personas and use cases
- ✅ Enhanced step-by-step usage instructions
- ✅ Expanded technology stack documentation
- ✅ Improved README structure and organization
- ✅ Added professional support information
- ✅ Updated file export documentation to reflect XLSX default and multi-format support

### Version 1.0.1
- ✅ Enhanced localStorage error handling with user-friendly error messages
- ✅ Added comprehensive localStorage availability detection
- ✅ Improved accessibility with additional ARIA attributes
- ✅ Added detailed error handling documentation in README
- ✅ Enhanced semantic markup while preserving all existing functionality
### Version 1.0.0
- ✅ Initial release with core functionality
- ✅ OpenStreetMap Overpass API integration
- ✅ Multi-format export (XLSX, XLS, CSV, Google Sheets, Apple Numbers) and ZIP bundling
- ✅ Custom category management
- ✅ Geographic search capabilities

---

**Thank you for using the Business Search & Export Tool!**

This application represents the power of modern web technologies combined with open data sources to solve real business challenges. Whether you're an MSP looking for prospects, a marketer building campaigns, or a consultant analyzing markets, we hope this tool accelerates your success.

*Built with ❤️ by [myTech.Today](https://mytech.today) - Your Technology Partner in Barrington, IL*