# Places Search — Python and Streamlit

A small portfolio application that searches for local businesses through the
Google Places API, filters the returned records and exports a table to XLSX.
It demonstrates HTTP/JSON integration, basic pandas operations and a Streamlit
interface. The interface and spreadsheet column names are in Polish.

## Features

- Search by industry and city using Google Places Text Search (New).
- Request between 1 and 20 records per search.
- Filter returned records by minimum review count and website availability.
- Display business details and counts of results, businesses without a website
  and businesses with a phone number.
- Export the filtered table to a single `Leady` worksheet using pandas and
  openpyxl. The export includes a default contact status and an empty notes
  column for later manual use.

![Search form before submitting a query](docs/screenshots/search-form.jpg)

The screenshot shows the initial search form with no results. It was captured
locally using an invalid placeholder key; no Google API request was made.

## Stack

Python, Streamlit, pandas, requests and openpyxl. Application code is in
[`leadfinder.py`](leadfinder.py); dependencies are in
[`requirements.txt`](requirements.txt).

## Run locally

Python 3.12 was used for the installation and offline checks described below.
Git and Python with `venv`/`pip` are required.

### 1. Clone and install

```bash
git clone https://github.com/Hackobe/places-search-streamlit.git
cd places-search-streamlit
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
```

On Windows PowerShell, replace the virtual environment commands with:

```powershell
py -3.12 -m venv .venv
.\.venv\Scripts\Activate.ps1
```

Then run the same `python -m pip install -r requirements.txt` command.

### 2. Configure your own Google API key

Live searches require a Google Cloud project with billing and **Places API
(New)** enabled, plus your own API key. Searches can incur charges in that
project. Follow Google's [Places API setup guide](https://developers.google.com/maps/documentation/places/web-service/get-api-key)
and configure appropriate API restrictions and usage limits.

Create a `.streamlit` directory in the repository and a file named
`.streamlit/secrets.toml`. Add this setting, then enter your own key between
the quotes in your local editor:

```toml
GOOGLE_API_KEY = ""
```

The application reads `GOOGLE_API_KEY` from `st.secrets`; there is no API-key
input in the UI. The file must exist before running the application. An empty
value stops the app with a configuration message. The local secrets file is
excluded by `.gitignore` and must not be committed.

See [Streamlit secrets management](https://docs.streamlit.io/develop/concepts/connections/secrets-management)
for the configuration format. A real key is needed to perform a search; the
placeholder used for the screenshot cannot authenticate requests.

### 3. Start the application

From the repository directory, with the virtual environment active:

```bash
python -m streamlit run leadfinder.py --secrets.files .streamlit/secrets.toml
```

Open the local URL printed by Streamlit, normally `http://localhost:8501`.
The explicit `--secrets.files` argument selects the project's local key file.

Enter an industry and city, optionally adjust the minimum review count and
result limit, and select **Szukaj leadów**. The checkbox limits the displayed
results to businesses without a website. When results remain after filtering,
**Pobierz bazę leadów Excel** downloads the XLSX file.

## Scope and limitations

- This is a small portfolio tool, not a complete CRM or production data pipeline.
- A search requests at most 20 records and does not paginate. Filters are applied
  after retrieval, so fewer records may remain. Results are not an exhaustive
  list of businesses in a location.
- Phone numbers, websites and ratings depend on the fields returned by Google;
  some can be missing.
- There is no database, account system, saved search history or background job.
- Network error handling is basic: HTTP errors are displayed, but there is no
  timeout, retry policy or rate limiting in the application.
- XLSX export writes tabular values. It does not create Excel formulas, pivot
  tables or Power BI reports. The status/notes columns are not a saved workflow.

## Verification

Checked locally on macOS with Python 3.12.14 on 4 November 2026:

- Installation from `requirements.txt` in a fresh virtual environment.
- Python syntax compilation.
- Initial form rendering without making a search or contacting Google Places.
- The application's XLSX export function with synthetic rows, then reopening
  the generated workbook to verify its sheet and values.

Resolved versions were Streamlit 1.66.1, pandas 3.1.1, requests 2.34.2 and
openpyxl 3.1.5. Dependencies are currently unpinned, so later installations
may resolve different versions. Live search results and Google billing were
not tested. There is no automated test suite or CI workflow in this repository.
