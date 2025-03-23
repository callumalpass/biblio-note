# biblio-note

**Script for Bibliographic Information Collection and Reading Note Generation**

## Description

`biblio-note` is a bash script designed to streamline the process of collecting bibliographic information, organizing resources, and creating reading notes. It leverages the [Zotero Translation Server](https://github.com/zotero/translation-server) for retrieving bibliographic metadata from various sources and [fzf](https://github.com/junegunn/fzf) for interactive selections.

The created literature notes can be easily scraped for the creation of CSL-valid json bibliography files for later use with `pandoc`. 

**Key Features:**

*   **Bibliographic Data Retrieval:** Fetches bibliographic information from web searches, web pages, templates, BibTeX, CSL-JSON, and section information using the Zotero Translation Server.
*   **Resource Directory Creation:** Automatically creates organized directories for each bibliographic resource in your designated library folder.
*   **Reading Note Generation:** Generates Markdown, HTML, or PDF reading notes with YAML frontmatter containing bibliographic metadata, author links, tags, and attachment links, ready for note-taking applications like Obsidian or similar.
*   **Interactive Selection with fzf:** Uses `fzf` for fuzzy-finding and interactive selection of templates and identifiers.
*   **Configurable:**  Settings like editor, bibliography library directory, and log file path can be customized via a configuration file.
*   **Translation Server Submodule:**  Includes the necessary [Zotero Translation Server](https://github.com/zotero/translation-server) as a Git submodule, simplifying setup.

## Dependencies

Before running `biblio-note`, ensure you have the following dependencies installed on your system:

*   **Command-line Tools:**
    *   [`curl`](https://curl.se/): For making HTTP requests to the translation server.
    *   [`jq`](https://stedolan.github.io/jq/): For processing JSON data.
    *   [`yq`](https://github.com/mikefarah/yq): For processing YAML data.
    *   [`fzf`](https://github.com/junegunn/fzf): For interactive fuzzy finder.
    *   [`nnn`](https://github.com/jarun/nnn): (Optional, but recommended) For interactive file selection when choosing attachments.
    *   [`ebook-convert`](https://calibre-ebook.com/): (Part of Calibre) For converting EPUB files to PDF.
    *   [`npm`](https://www.npmjs.com/): Node Package Manager, required for the translation server.
    *   [`git`](https://git-scm.com/): For managing the translation server submodule.
*   **Text Editor:**
    *   `nvim` (default) or your preferred text editor (configurable in the config file).


**Zotero Translation Server Submodule:**

This script relies on the [Zotero Translation Server](https://github.com/zotero/translation-server). It is included as a Git submodule within the `vendor/translation-server` directory.

**After cloning this repository, you must initialize and update the submodule:**

```bash
git submodule init
git submodule update
```

This will download the translation server code into the `vendor/translation-server` directory.

## Configuration

`biblio-note` uses a configuration file to customize its behavior.

1.  **Configuration File Location:** The configuration file is located at `~/.config/biblio-note`.

2.  **Creating the Configuration File:** If the configuration file doesn't exist when you run the script for the first time, `biblio-note` will automatically create a default configuration file with empty fields at `~/.config/biblio-note` and exit with an error message.

3.  **Editing the Configuration File:** Open `~/.config/biblio-note` with your text editor and adjust the following settings as needed:

    ```bash
    # biblio-note configuration file
    #
    # Edit this file to set your preferred paths and settings.
    # Lines starting with '#' are comments and are ignored.
    #
    # Leave values empty if you want to use the script's internal defaults
    # (though for essential paths, it's recommended to set them here).

    # Path to your preferred text editor (e.g., nvim, vim, code).
    # If empty, defaults to 'nvim'.
    EDITOR="nvim"

    # Path to the translation server directory.
    # This is usually the 'vendor/translation-server' submodule directory.
    # If empty, defaults to './vendor/translation-server'.
    TRANSLATION_SERVER_DIR="./vendor/translation-server"

    # Path to your bibliography library directory where resource folders are created.
    # If empty, defaults to '$HOME/Dropbox/notes/biblib'.
    BIBLIB_DIR="$HOME/Dropbox/notes/biblib"

    # Path to the log file for biblio-note script.
    # If empty, defaults to '$HOME/.script_logs/biblio-note.log'.
    LOG_FILE="$HOME/.script_logs/biblio-note.log"
    ```

    *   **`EDITOR`:** Path to your preferred text editor (e.g., `nvim`, `vim`, `code`, `emacs`).
    *   **`TRANSLATION_SERVER_DIR`:** Path to the `translation-server` directory. If you are using the submodule as intended, the default value `./vendor/translation-server` should work.
    *   **`BIBLIB_DIR`:** Path to your main bibliography library directory where resource folders will be created (e.g., `$HOME/Dropbox/notes/biblib`).
    *   **`LOG_FILE`:** Path to the log file for script execution logs (e.g., `$HOME/.script_logs/biblio-note.log`).

4.  **Permissions:** Ensure the configuration file has appropriate permissions (e.g., `600` for read/write access only by the user):

    ```bash
    chmod 600 ~/.config/biblio-note
    ```

## Usage

```bash
biblio-note [options]
```

**Options:**

*   `-h`, `--help`: Show help message and exit.
*   `-v`, `--version`: Show script version and exit.

**Running the Script:**

1.  **Navigate to the script's directory in your terminal.**
2.  **Make sure the script is executable:**

    ```bash
    chmod +x biblio-note
    ```

3.  **Run the script:**

    ```bash
    ./biblio-note
    ```

    The script will then guide you through the following steps:

    *   **Start Translation Server:**  The script will automatically start the translation server in the background.
    *   **Select Identifier Kind:** You will be prompted to choose the type of identifier you want to use to retrieve bibliographic information using `fzf`:
        *   `search`:  Enter a search query.
        *   `web`: Enter a URL of a webpage containing bibliographic information.
        *   `template`: Select a CSL-JSON template file from `./csl_templates` using `fzf`.
        *   `bibtex`: Enter BibTeX data directly in the terminal.
        *   `csl`: Enter CSL-JSON data directly in the terminal.

    *   **Enter Identifier:**  Enter the identifier based on the selected kind (search query, URL, etc.).
    *   **Review Bibliographic Information:** The script will retrieve bibliographic information and open it in your configured text editor for review and modification.
    *   **Enter Citekey:**  Enter a citekey for the resource. This will be used for the resource directory and note filenames.
    *   **Select Attachment (Optional):** You will be prompted to select an attachment file using `nnn`. If you select an EPUB file, it will be converted to PDF using `ebook-convert`.
    *   **Reading Note Generation:** A Markdown reading note  will be generated in your `$HOME/Dropbox/notes` directory (e.g., `@citekey.md`). The note will be opened in your configured editor.
    *   **Bibliography Update:** The script will attempt to rebuild your bibliography using `./bibbuild` (requires these scripts to be configured).
    *   **Translation Server Stop:** When the script finishes or exits, it will automatically stop the translation server.

## Getting Started

1.  **Clone the repository:**
    ```bash
    git clone <repository-url> biblio-note
    cd biblio-note
    ```
2.  **Initialize and update the translation server submodule:**
    ```bash
    git submodule init
    git submodule update
    ```
3.  **Install dependencies** (see "Dependencies" section).
4.  **Run the script:**
    ```bash
    ./biblio-note
    ```
5.  **Configure the script** when prompted, or edit `~/.config/biblio-note` directly.
6.  **Follow the prompts** to collect bibliographic information and generate your reading note.

