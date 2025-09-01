# NotebookLM Automation Tools

Automate the process of extracting documentation URLs and adding them as sources to NotebookLM notebooks using browser automation.

## Scripts Overview

### `scrape_add_links_nblm_script.py` - Enhanced URL Extraction & Notebook Management
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
