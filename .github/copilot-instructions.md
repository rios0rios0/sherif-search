# Copilot Instructions for Sherif Search

## Project Overview

Sherif Search is a **discontinued** Windows desktop application built in **Borland Delphi 7** (Object Pascal) around 2013–2014. It is preserved as a historical reference. The application automates web searches across six search engines to harvest and deduplicate URLs, and brute forces admin directory paths on target websites. It was designed as a penetration testing reconnaissance tool for educational purposes.

**Language:** Object Pascal (Borland Delphi 7) | **Status:** Discontinued | **Version:** 2.0

## Repository Structure

```
sherif-search/
├── .github/
│   └── copilot-instructions.md   # This file
├── Imgs/
│   └── Icon.ico                  # Application icon (magnifying glass)
├── Clear.bat                     # Cleans Delphi build artifacts (*.dcu, *.opt, *.dsm, etc.)
├── CONTRIBUTING.md               # Historical build information
├── LICENSE                       # GNU GPL v3.0
├── README.md                     # Project documentation
├── SS.dpr                        # Delphi project file — entry point, sets title "Sherif Search"
├── SS.res                        # Compiled resource file (application icon)
├── USS.dfm                       # Form definition (643×643, fixed border, custom icon)
└── USS.pas                       # Main unit (~1100 lines) with all application logic
```

### Key Source Files

- **`SS.dpr`** — The Delphi project file. Initializes the VCL application, sets the title, and creates `TForm1` from `USS.pas`.
- **`USS.pas`** — All application logic in a single unit. Key routines:
  - `CheckConexao` — internet connectivity check via `InternetGetConnectedState` (WinINet)
  - `CheckRButton` — detects which search engine radio button is active
  - `CheckUrl` — HTTP status code retrieval via `InternetOpenUrl` / `HttpQueryInfo`
  - `ThreadSearch` — threaded multi-engine search with engine-specific URL construction
  - `IPrsrAnchor` — `TIEParser` callback for extracting `<a href>` anchors from HTML
  - `btnscanClick` — admin directory brute-force scanner
  - `Concat` — HTTP GET + status check per admin path
  - `SaveListViewToFile` — binary serialisation of ListView data
  - String list CRUD handlers (Add / Modify / Remove / Clear / Save / Load)
- **`USS.dfm`** — Delphi Form Definition: describes all visual components, positions, and properties.
- **`Clear.bat`** — Deletes Delphi intermediate build artifacts from the project directory.

## Technology Stack

| Component         | Technology                                                                 |
|-------------------|----------------------------------------------------------------------------|
| Language          | Object Pascal                                                              |
| IDE               | Borland Delphi 7 (compatible: Embarcadero RAD Studio)                     |
| UI framework      | VCL (Visual Component Library)                                             |
| HTML parsing      | `TIEParser` — Internet Explorer COM-based HTML parser component           |
| HTTP client       | `TIdHTTP` (Indy) with 2-second read timeout                               |
| Network/HTTP      | WinINet API (`InternetGetConnectedState`, `InternetOpenUrl`, `HttpQueryInfo`) |
| Threading         | Windows API `CreateThread`                                                 |
| XP theming        | `TXPManifest` component                                                    |
| File I/O          | `TListBox.Items.SaveToFile` / `LoadFromFile`, `TFileStream` (ListView)    |

## Build Instructions

> **Prerequisite:** Borland Delphi 7 or a compatible Embarcadero RAD Studio installation on Windows with the `IEParser` and `Indy` component packages installed.

1. Open `SS.dpr` in Borland Delphi 7 (or compatible IDE).
2. Ensure the `IEParser` and `Indy` component packages are installed in the IDE.
3. Compile the project with **Ctrl+F9** (or Project → Compile).
4. Run `SS.exe` on a Windows machine with an internet connection.

### Cleaning Build Artifacts

Run `Clear.bat` to remove Delphi intermediate files (`*.dcu`, `*.opt`, `*.dsm`, `*.dsk`, `*.cfg`, `*.ddp`, `*.dof`, etc.) from the project directory.

## Architecture and Design Patterns

- **Single-form, single-unit architecture** — all logic lives in `USS.pas` / `TForm1`. There are no separate data layers, service classes, or unit tests.
- **Event-driven VCL** — UI behaviour is driven by Delphi component event handlers (e.g. `btnpesquisarClick`, `btnscanClick`, radio button `OnClick`).
- **Threaded search via `CreateThread`** — search operations run on a background Windows thread to keep the UI responsive; a cancel button sets a shared flag to abort.
- **Callback-based HTML parsing** — `TIEParser` fires an `OnAnchor` event (`IPrsrAnchor`) for each `<a>` tag found; the callback appends deduplicated URLs to the result list.
- **Engine-specific URL construction** — each radio button maps to a hardcoded URL template for Google, Yahoo, Bing, Baidu, Lycos, or Amfibi.

## Features

### Multi-Engine Search (Search Tab)
- Six search engine backends with engine-specific URL and parameter handling.
- Domain and language filter dropdowns; page-range spin edits.
- Batch search over multiple query strings with URL deduplication and engine-domain filtering.
- Optional subdomain stripping (trim to base domain).

### Admin Directory Scanner (Admin Tab)
- Brute forces a configurable wordlist of admin paths against a target URL.
- Detects HTTP 200–206 (accessible), 300–307 (redirect), 401 (auth required), 404 (not found).
- Results displayed in a `TListView` with URL and status columns; progress bars track progress.

### String List Management
- Right-click context menus on list boxes for Add / Modify / Remove / Clear / Load / Save.
- Persists lists to plain-text files.

## CI/CD Pipeline

There is **no automated CI/CD pipeline**. The project is discontinued and compiled manually in the Delphi IDE on Windows.

## Development Workflow

Since this project is discontinued, the typical workflow for any historical reference or archival changes is:

1. Edit `.pas` or `.dfm` files directly in Borland Delphi 7.
2. Compile and test manually on a Windows machine.
3. Run `Clear.bat` to remove build artifacts before committing.
4. Commit only source files (`*.dpr`, `*.pas`, `*.dfm`, `*.res`, `*.bat`, `Imgs/`). Do **not** commit compiled artifacts (`*.exe`, `*.dcu`, `*.opt`, etc.).

## Coding Conventions

- **Object Pascal style** — follow standard Delphi 7 conventions: `begin`/`end` blocks, `PascalCase` for types and methods, `TPrefix` for types, `F` prefix for private fields.
- **Single-unit design** — all form logic stays in `USS.pas`; avoid splitting into multiple units unless absolutely necessary.
- **Portuguese identifiers** — many variable names and UI labels are in Portuguese (e.g. `btnpesquisar`, `grpmecanismo`, `lblpaginainicial`). Preserve this convention for consistency.
- **No external dependencies beyond Delphi 7 standard library, IEParser, and Indy** — do not introduce new component packages.

## Common Tasks

| Task                          | How to do it                                                      |
|-------------------------------|-------------------------------------------------------------------|
| Build the project             | Open `SS.dpr` in Delphi 7, press **Ctrl+F9**                     |
| Clean build artifacts         | Run `Clear.bat` in the project root                              |
| Add a new search engine       | Add a radio button in `USS.dfm`, extend `CheckRButton` and `ThreadSearch` in `USS.pas` |
| Add new admin paths           | Add entries to the admin path list box at runtime via right-click |
| Open a result in browser      | Double-click or right-click a URL in the results list            |

## Troubleshooting

- **"IEParser component not found"** — install the `TIEParser` package in the Delphi IDE component palette.
- **"Indy components not found"** — install the Indy 9/10 component package compatible with Delphi 7.
- **No results returned** — the target search engine may have changed its HTML structure since 2013. The URL construction and anchor extraction are hardcoded for the engines' layouts at that time.
- **Admin scan hangs** — check internet connectivity; `CheckConexao` validates the network before operations begin.
