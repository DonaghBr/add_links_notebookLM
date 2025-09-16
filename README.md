# NotebookLM Automation Tools

Automate the process of extracting documentation URLs and adding them as sources to NotebookLM notebooks using browser automation.

## Scripts Overview

The `scrape_add_links_nblm_script.py` script enhances URL extraction and Notebook management. The details of the script include:
- **Based on** This code is adapted from https://github.com/sshnaidm/notebooklm/blob/master/automation/add_links_script.py   
- **Extract URLs** from documentation sites with version support
- **Combined workflows** - run extraction, authentication, and notebook loading in one command
- **Smart resource combination** - automatically combines scraped URLs with static CQA resources
- **Add URLs to NotebookLM** with authentication management
- **All-in-one solution** for extraction and notebook loading

## Features

- **URL Extraction**: Scrape documentation hierarchies with smart version detection and support
- **Combined Workflows**: Run extract → login → add in single command
- **Static Resource Integration**: Automatically includes `CQA_res.txt` static links (optional with `--skip-cqa`)
- **Consistent File Handling**: Always uses `urls.txt` for predictable behavior
- **Version Support**: Auto-detects versions in URLs or specify versions like 2.19, 2.20 (defaults to "latest")
- **Authentication Management**: Persistent Google login sessions
- **Bulk URL Loading**: Add multiple URLs to NotebookLM automatically
- **Error Handling**: Comprehensive error messages and recovery options
- **YouTube & Website Support**: Handles both content types
- **Smart Version Detection** The script automatically detects version numbers in URLs and handles them intelligently:

    | URL Format | `--versions` Flag | Behavior |
    |------------|------------------|----------|
    | `https://docs.example.com/product/3.2` | Not specified | Uses detected version `3.2` |
    | `https://docs.example.com/product/3.2` | `--versions 2.21,latest` | Ignores detected `3.2`, uses specified versions |
    | `https://docs.example.com/product` | Not specified | Uses default `latest` |
    | `https://docs.example.com/product` | `--versions 2.21,latest` | Uses specified versions |

- **Supported Version Patterns**: Includes `/latest`, `/3.2`, `/v3.2`, `/2.21.1`
- **Consistent File Handling**: The script uses **predictable file handling** for easier workflows:
  - **Extraction**: Always saves to `urls.txt` (unless `--toc-output` specified)
  - **Notebook Mode**: Always reads from `urls.txt` (unless `--links-file` specified)
  - **Resource Combination**: Always includes `CQA_res.txt` static resources (unless `--skip-cqa` is used)
  - **No Guessing**: Clear, consistent behavior every time

## Installation

### Prerequisites
- Python 3.7+
- A NotebookLM account

### Procedure

1. OPTIONAL - if Project Root is different to script directory
  From the script directory, navigate to where the virtual environment is located if it is different to where the ADD_LINKS_NOTEBOOK files are stored:
  ```bash
  # Navigate to project root (where .venv is located) 
  cd ../add_links_notebook
  ```

2. Create a virtual environment or activate an existing environment:
  ```bash
  # If using this for the first time, create a new virtual environment
  python3 -m venv .venv
  ```

  ```bash
  # Activate the existing virtual environment
  source .venv/bin/activate
  ```
3. Install dependencies:
  ```bash
  python3 -m pip install -r requirements.txt
  ```

4. Install browser binaries for Playwright - assuming you haven't installed it by running requirements.txt.
  ```bash
  python3 -m playwright install
  ```

5. Install Playwright Browser:
  ```bash
  # Check Playwright is installed
  playwright --version
  ```
6. Install Chromium browser for automation - needed for authentication
  ```bash
  playwright install chromium
  ```

7. OPTIONAL - if your Project Root is different to script directory, return to Script Directory
  ```bash
  # Navigate back to script directory
  cd ../add_scrapped_links_notebooklm
  ```

**Note**: Always ensure the virtual environment is active (you should see `(.venv)` in your terminal prompt) before running the scripts.

## Different workflows to run the script

### Option 1: Single command workflows to run the script

**Full Combined Workflow (One Command)**
```bash
# Extract URLs, authenticate, and add to notebook in one command
python3 scrape_add_links_nblm_script.py --extract-toc "https://docs.redhat.com/en/documentation/red_hat_openshift_ai_self-managed" --login --notebook "https://notebooklm.google.com/notebook/YOUR_NOTEBOOK_ID"
```
**Add links other than docs.redhat.com links (One Command)**
```bash
python3 scrape_add_links_nblm_script.py --login --notebook "https://notebooklm.google.com/notebook/YOUR_NOTEBOOK_ID" --links https://www.redhat.com/en/blog/red-hat-ai-inference-server-technical-deep-dive https://www.youtube.com/watch?v=b9BWbr_7xs8
```

**If you've already logged in, extract URLs and add to notebook in one command**
```bash
python3 scrape_add_links_nblm_script.py --login --notebook "https://notebooklm.google.com/notebook/YOUR_NOTEBOOK_ID" --links https://www.redhat.com/en/blog/red-hat-ai-inference-server-technical-deep-dive https://www.youtube.com/watch?v=b9BWbr_7xs8
```

### Option 2: Step-by-Step workflow to run the script

1. Extract URLs from the latest version or specific versions from your documentation: 
```bash
# Extract URLs from the latest version (saves to urls.txt)
python3 scrape_add_links_nblm_script.py --extract-toc "https://docs.redhat.com/en/documentation/red_hat_openshift_ai_self-managed"
```

```bash
# Extract URLs from specific versions
python3 scrape_add_links_nblm_script.py --extract-toc "https://docs.redhat.com/en/documentation/red_hat_openshift_ai_self-managed" --versions "latest,2.21,2.20"
```

2. Authenticate with Google (first time only)
```bash
python3 scrape_add_links_nblm_script.py --login
```
  - Opens the browser window.
  - Log in to Google manually.
  **IMPORTANT: Close the browser window after logging in** (saves session).

3. Create a NotebookLM Notebook.
  - Go to [NotebookLM](https://notebooklm.google.com/)
  - Create a new notebook.
  - Copy the notebook URL.
  - Use the copied URL in the next step

4. Add URLs to your Notebook.
```bash
python3 scrape_add_links_nblm_script.py --notebook "https://notebooklm.google.com/notebook/YOUR_NOTEBOOK_ID"
```
**Note**: Automatically combines `urls.txt` (scraped URLs) + `CQA_res.txt` (static resources). Use `--skip-cqa` to exclude CQA_res.txt.

## Generated files after running the script

- **`urls.txt`**: Primary file for scraped URLs (gets overwritten with each extraction)
- **`CQA_res.txt`**: Static CQA resources (always included automatically)
- **Combined**: Script automatically merges both files when adding to notebook

## Detailed usage of the script

### Combined workflow examples
Run any of the following commands based on your use case:
```bash
# Full workflow (extract → login → add):
python3 scrape_add_links_nblm_script.py --extract-toc URL --login --notebook NOTEBOOK_URL

# Extract then add (uses urls.txt automatically):
python3 scrape_add_links_nblm_script.py --extract-toc URL --notebook NOTEBOOK_URL

# Login then add (uses existing urls.txt):
python3 scrape_add_links_nblm_script.py --login --notebook NOTEBOOK_URL
```

### URL extraction mode examples

**_Extract with Default Version (latest)_**
```bash
python3 scrape_add_links_nblm_script.py --extract-toc "BASE_URL"
```
**Note**: Always saves to `urls.txt` unless `--toc-output` specified.

**_Extract with specific versions_**
```bash
python3 scrape_add_links_nblm_script.py --extract-toc "BASE_URL" --versions "2.21,2.22,latest"
```

**_Smart version detection (URLs with versions)_**
```bash
# URL contains version - uses detected version (3.2)
python3 scrape_add_links_nblm_script.py --extract-toc "https://docs.redhat.com/en/documentation/red_hat_ai_inference_server/3.2"

# URL contains version but override with --versions flag
python3 scrape_add_links_nblm_script.py --extract-toc "https://docs.redhat.com/en/documentation/red_hat_ai_inference_server/3.2" --versions "latest,2.21"
```
**Note**: Script automatically detects and strips version from URL, then uses detected version or specified versions.

**_Extract with custom output file_**
```bash
python3 scrape_add_links_nblm_script.py --extract-toc "BASE_URL" --toc-output custom_file.txt
```

### Authentication mode example
```bash
python3 scrape_add_links_nblm_script.py --login
```

### Notebook management mode examples

**_Use default files (Recommended)_**
```bash
python3 scrape_add_links_nblm_script.py --notebook "NOTEBOOK_URL"
```
This command automatically combines:
- `urls.txt` (scraped URLs)
- `CQA_res.txt` (static CQA resources)

**_Use custom links file_**
```bash
python3 scrape_add_links_nblm_script.py --notebook "NOTEBOOK_URL" --links-file custom_links.txt
```
**Note**: Still includes `CQA_res.txt` automatically unless the `--skip-cqa` parameter is used.

**_Add individual URLs_**
```bash
python3 scrape_add_links_nblm_script.py --notebook "NOTEBOOK_URL" --links "https://example.com" "https://youtube.com/watch?v=xyz"
```

**_Skip CQA resources (Use Only Extracted/Custom Links)_**
```bash
# Use only extracted URLs (skip CQA_res.txt)
python3 scrape_add_links_nblm_script.py --notebook "NOTEBOOK_URL" --skip-cqa

# Use only custom file (skip CQA_res.txt)
python3 scrape_add_links_nblm_script.py --notebook "NOTEBOOK_URL" --links-file custom.txt --skip-cqa

# Full workflow with skip CQA
python3 scrape_add_links_nblm_script.py --extract-toc "BASE_URL" --notebook "NOTEBOOK_URL" --skip-cqa
```

## Advanced options to use the script

The command line options include:

**Help file**
- `--help`: Lists all available options

**Extraction Mode**:
- `--extract-toc URL`: Documentation URL to scrape (with or without version)
- `--toc-output FILE`: Output file for extracted links (default: urls.txt)  
- `--versions LIST`: Comma-separated versions (default: detected version or latest)

**Notebook Mode**:
- `--notebook URL`: NotebookLM notebook URL
- `--links-file FILE`: Links file (default: urls.txt, always includes CQA_res.txt)
- `--links URL [URL...]`: Individual URLs to add
- `--skip-cqa`: Skip including CQA_res.txt when using file-based links

**Authentication**:
- `--login`: Run authentication process
- `--profile-path PATH`: Browser profile directory (default: ~/.browser_automation)

**Combined Workflows**:
You can combine any of the three main operations in a single command:
- `--extract-toc` + `--notebook`: Extract then add
- `--login` + `--notebook`: Login then add  
- `--extract-toc` + `--login` + `--notebook`: Full workflow

## Usage examples

### Example 1: Red Hat OpenShift AI Documentation (Full Workflow)
```bash
# One command to do everything
python3 scrape_add_links_nblm_script.py \
  --extract-toc "https://docs.redhat.com/en/documentation/red_hat_openshift_ai_self-managed" \
  --login \
  --notebook "https://notebooklm.google.com/notebook/abc123"
```

### Example 2: Extract Different Documentation, Reuse Login
```bash
# Extract AI Inference Server docs (overwrites urls.txt)
python3 scrape_add_links_nblm_script.py \
  --extract-toc "https://docs.redhat.com/en/documentation/red_hat_ai_inference_server" \
  --notebook "https://notebooklm.google.com/notebook/abc123"
```

### Example 3: Multiple Versions
```bash
# Extract from multiple versions
python3 scrape_add_links_nblm_script.py \
  --extract-toc "https://docs.example.com/product" \
  --versions "v1.0,v2.0,latest" \
  --notebook "https://notebooklm.google.com/notebook/xyz789"
```

### Example 4: Smart Version Detection  
```bash
# URL with version - automatically uses 3.2 (no --versions needed)
python3 scrape_add_links_nblm_script.py \
  --extract-toc "https://docs.redhat.com/en/documentation/red_hat_ai_inference_server/3.2" \
  --notebook "https://notebooklm.google.com/notebook/abc123"

# URL with version but override to get multiple versions
python3 scrape_add_links_nblm_script.py \
  --extract-toc "https://docs.redhat.com/en/documentation/red_hat_ai_inference_server/3.2" \
  --versions "3.2,3.1,latest" \
  --notebook "https://notebooklm.google.com/notebook/abc123"
```

### Example 5: Skip CQA Resources (Use Only Documentation)
```bash
# Extract and add only Red Hat AI Inference Server docs (no CQA_res.txt)
python3 scrape_add_links_nblm_script.py \
  --extract-toc "https://docs.redhat.com/en/documentation/red_hat_ai_inference_server" \
  --notebook "https://notebooklm.google.com/notebook/abc123" \
  --skip-cqa

# Use only custom links file (no CQA_res.txt)
python3 scrape_add_links_nblm_script.py \
  --notebook "https://notebooklm.google.com/notebook/abc123" \
  --links-file my_custom_links.txt \
  --skip-cqa
```

## Troubleshooting

### Playwright Browser Installation Issues
**Error**: "Executable doesn't exist at .../Chromium.app/Contents/MacOS/Chromium"
**Solution**: Install Playwright browsers:
```bash
# Navigate to project root
cd /Users/dobrenna/Documents/NLP_college/sandbox/add_links_notebook

# Activate virtual environment
source .venv/bin/activate

# Install Chromium browser
playwright install chromium

# Return to script directory
cd notebooklm/automation/add_scrapped_links_notebooklm
```

### Virtual Environment Issues
**Error**: "ModuleNotFoundError" or missing packages
**Solutions**:
1. Ensure virtual environment is activated: `source .venv/bin/activate`
2. Check you're in the right directory: should see `(.venv)` in prompt
3. Verify Playwright installation: `playwright --version`

### Browser Lock Issues
**Error**: "ProcessSingleton" errors
**Solution**: Clear browser lock files:
```bash
rm -f ~/.browser_automation/SingletonLock ~/.browser_automation/SingletonCookie ~/.browser_automation/SingletonSocket
```

### No Links File Found
**Error**: "Main links file not found - urls.txt"
**Solutions**:
1. Run `--extract-toc` first to create `urls.txt`
2. Specify `--links-file` with an existing file
3. Provide `--links` with individual URLs

### Login Required Errors
**Error**: "Could not find Add button" (all links fail)
**Solution**: Re-run login process:
```bash
python3 scrape_add_links_nblm_script.py --login
```

### URL Extraction Failures
**Error**: 404 errors during extraction
**Solutions**:
- Verify the base URL is correct
- Check if the versions exist (try "latest" first)
- Ensure the documentation site is accessible

### Timeout Issues
**Error**: Script times out clicking buttons
**Solutions**:
- Clear browser lock files (see above)
- Ensure you closed the browser window after logging in
- Try running the login step again



## Notes

- **Virtual Environment**: Always activate your virtual environment (`source .venv/bin/activate`) before running scripts
- **Browser Installation**: One-time setup with `playwright install chromium`
- **Authentication**: Login session is saved in `~/.browser_automation` directory
- **File Consistency**: Always uses `urls.txt` for extracted URLs for predictable behavior
- **Resource Integration**: Automatically includes static CQA resources from `CQA_res.txt` (use `--skip-cqa` to exclude)
- **Rate Limiting**: Script waits 3 seconds between URLs to avoid overwhelming NotebookLM
- **Browser**: Uses Chromium in visible mode so you can see progress
- **Content Types**: Supports both website URLs and YouTube videos
- **File Format**: All URL files should have one URL per line
- **Combined Workflows**: Can run extraction, authentication, and notebook addition in single command
- **All-in-one solution** for extraction and notebook loading
