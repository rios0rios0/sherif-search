<h1 align="center">Sherif Search</h1>
<p align="center">
    <a href="https://github.com/rios0rios0/sherif-search/releases/latest">
        <img src="https://img.shields.io/github/release/rios0rios0/sherif-search.svg?style=for-the-badge&logo=github" alt="Latest Release"/></a>
    <a href="https://github.com/rios0rios0/sherif-search/blob/main/LICENSE">
        <img src="https://img.shields.io/github/license/rios0rios0/sherif-search.svg?style=for-the-badge&logo=github" alt="License"/></a>
</p>

A Windows desktop application that automates web searches across six search engines to harvest and deduplicate URLs, and scans target websites for accessible admin directories by brute-forcing paths from a configurable wordlist. Built with Delphi 7 as a penetration testing reconnaissance tool for educational purposes.

**Language:** Object Pascal (Borland Delphi 7) | **Status:** Discontinued | **Version:** 2.0

## Features

### Multi-Engine Search (Search Tab)

- **Six search engine backends** -- Google, Yahoo, Bing, Baidu, Lycos, and Amfibi, each with engine-specific URL construction and parameter handling
- **Configurable search parameters:**
  - Domain filter (e.g., `.com`, `.org`) via dropdown
  - Language filter with engine-specific locale codes (Google uses `hl=`, Yahoo/Bing/Lycos use regional subdomains)
  - Page range selection (start page to end page) via spin edits
  - Results-per-page control (Yahoo)
- **Batch string search** -- processes multiple search queries from a managed string list, iterating each query across the selected page range
- **HTML anchor extraction** -- uses the `TIEParser` (Internet Explorer HTML parser) component to extract all `<a href>` anchors from search result pages
- **URL deduplication and filtering** -- automatically removes duplicate URLs and filters out links to the search engines themselves (Google, Yahoo, Bing, Baidu, Lycos, Amfibi, Microsoft, YouTube, Orkut, MSN)
- **Subdomain stripping** -- optional checkbox to trim extracted URLs down to their base domain by removing paths after the first `/`
- **Threaded search** -- search operations run in a separate thread (`CreateThread`) to keep the UI responsive, with a cancel button to abort mid-search
- **Internet connectivity check** -- validates the network connection via `InternetGetConnectedState` (WinINet API) before starting any operation

### Admin Directory Scanner (Admin Tab)

- **Path brute-forcing** -- iterates through a configurable list of admin path strings (e.g., `admin/`, `wp-admin/`, `cpanel/`) and appends each to a target URL
- **HTTP status code detection** -- checks each URL using `InternetOpenUrl` and `HttpQueryInfo` to retrieve the HTTP status code, categorizing results:
  - 200-206: Directory accessible
  - 300-307: Redirected
  - 401: Authorization required
  - 404: Not found
- **Results displayed in a ListView** with URL and status columns
- **Progress tracking** -- progress bars and status bars show current item count, total items, and current URL being tested

### String List Management

- **Full CRUD operations** on search strings and admin path lists via right-click context menu:
  - Add, modify, remove, and clear individual strings
  - Load string lists from text files
  - Save string lists to text files
- **Persistent save** -- quick-save exports the current list to a text file named after the list component

### UI and UX

- **Tabbed interface** -- `TPageControl` with Search and Admin tabs
- **Dynamic UI visibility** -- radio button selection for search engines toggles relevant filter controls (domain, language, results count)
- **Progress bars** for both search and scan operations
- **Dual status bars** showing found count, page progress, and current operation status
- **Animated window close** -- uses `AnimateWindow` with `AW_HIDE + AW_BLEND` for a fade-out effect on exit
- **Open in browser** -- double-click or right-click any found URL to open it in the default browser via `ShellExecute`

## Technologies

| Component | Technology |
|-----------|------------|
| Language | Object Pascal |
| IDE | Borland Delphi 7 |
| UI framework | VCL (Visual Component Library) |
| HTML parsing | `TIEParser` (Internet Explorer COM-based HTML parser) |
| HTTP client | `TIdHTTP` (Indy HTTP component) with 2-second read timeout |
| Network status | WinINet API (`InternetGetConnectedState`, `InternetOpenUrl`, `HttpQueryInfo`) |
| Threading | Windows API `CreateThread` |
| XP theming | `TXPManifest` component |
| File I/O | `TListBox.Items.SaveToFile` / `LoadFromFile`, `TFileStream` for ListView serialization |

## Project Structure

```
SS.dpr             # Delphi project file (entry point, sets application title "Sherif Search")
USS.pas            # Main unit (~1100 lines) containing all application logic:
                   #   - CheckConexao: internet connectivity check via WinINet
                   #   - CheckRButton: dynamic radio button state detection
                   #   - CheckUrl: HTTP status code retrieval via WinINet
                   #   - ThreadSearch: threaded multi-engine search with URL construction
                   #   - IPrsrAnchor: HTML parser callback for anchor extraction
                   #   - btnscanClick: admin directory brute-force scanner
                   #   - Concat: HTTP GET + status check per admin path
                   #   - SaveListViewToFile: binary serialization of ListView data
                   #   - String list CRUD (Add/Modify/Remove/Clear/Save/Load)
USS.dfm            # Form definition (643x643, fixed border, custom icon)
SS.res             # Compiled resource file (application icon)
Imgs/
  Icon.ico         # Application icon (magnifying glass)
Clear.bat          # Cleanup script for Delphi build artifacts
                   #   (*.dcu, *.opt, *.dsm, *.dsk, *.cfg, *.ddp, *.dof, etc.)
```

## Installation

1. Open `SS.dpr` in Borland Delphi 7 (or a compatible IDE such as Embarcadero RAD Studio)
2. Ensure the `IEParser` and `Indy` component packages are installed
3. Compile the project (Ctrl+F9)
4. Run the generated `SS.exe` on a Windows machine with an internet connection

## Usage

### Searching

1. Select a search engine by clicking its radio button (Google, Yahoo, Bing, Baidu, Lycos, or Amfibi)
2. Add search strings to the list via right-click > "Adicionar String"
3. Configure domain and language filters as needed
4. Set the start and end page range
5. Click "Pesquisar" (Search) to start
6. Results appear in the "Links Encontrados" (Found Links) list, deduplicated automatically
7. Right-click any result to open it in a browser, or save all results to a file

### Admin Scanning

1. Switch to the Admin tab
2. Enter a target URL (must start with `http`) in the target field
3. Add admin path strings to the wordlist (e.g., `admin/`, `login/`, `wp-admin/`)
4. Click "Scan" to start brute-forcing
5. Results appear in the ListView with URL and HTTP status description

## Contributing

Contributions are welcome. See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

## License

This project is licensed under the GNU General Public License v3.0. See the [LICENSE](LICENSE) file for details.
