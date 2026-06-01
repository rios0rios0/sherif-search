# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Sherif Search is a **discontinued** Windows desktop reconnaissance tool written in **Borland Delphi 7** (Object Pascal), preserved as a historical reference (~2013–2014). It harvests and deduplicates URLs across six search engines and brute-forces admin directory paths against a target URL. See `README.md` for the full feature list and `.github/copilot-instructions.md` for an equivalent assistant briefing.

## Build / run

There is **no CLI, package manager, or CI**. The project compiles only inside the Delphi IDE on Windows.

- Build: open `SS.dpr` in Delphi 7 (or a compatible Embarcadero RAD Studio), then **Ctrl+F9**. Requires the `IEParser` and `Indy` component packages installed in the IDE.
- Run: `SS.exe` on Windows with internet access.
- Clean: `Clear.bat` deletes Delphi intermediates (`*.dcu`, `*.opt`, `*.dof`, etc.). Commit only sources (`*.dpr/.pas/.dfm/.res/.bat`, `Imgs/`); never compiled output.
- Tests: none exist. There is no test framework — verification is manual on Windows.

## Architecture

- **Single unit, single form.** All logic lives in `USS.pas` (~1100 lines) on `TForm1`; `USS.dfm` is its visual definition. There are no service, data, or model layers — behavior is VCL event handlers (`btnpesquisarClick`, `btnscanClick`, radio `OnClick`, menu handlers).
- **Search runs on a raw Windows thread.** `btnpesquisarClick` launches `ThreadSearch` via the Win32 `CreateThread` (not `TThread`). It builds engine-specific URL templates, fetches pages, and feeds them to the `TIEParser` HTML parser, whose `IPrsrAnchor` callback fires per `<a href>` to append deduplicated, engine-filtered URLs.
- **Admin scan** (`btnscanClick` → `Concat` → `CheckUrl`) loops a wordlist of paths against the target, classifying each by HTTP status via `InternetOpenUrl`/`HttpQueryInfo` (WinINet). `CheckConexao` (`InternetGetConnectedState`) gates every operation.

## Conventions a reader gets wrong

- **Portuguese identifiers and UI strings throughout** (`btnpesquisar`, `grpmecanismo`, `lststrings`, `Cancel2`, error messages like `'Lista de Strings Vazia'`). This is intentional — preserve it; do not anglicize.
- **Engine dispatch is name-encoded, not a lookup table.** `CheckRButton` returns the checked `TRadioButton`'s `Name` (`Rb1`..`Rb6`), and `ThreadSearch` derives the engine with `Op := StrToInt(Copy(CheckRButton(Form1), 3, 1))` (the digit after `Rb`). Adding an engine means adding `Rb<N>` in `USS.dfm` **and** a matching `case Op` branch — the two must stay in sync, and the radio name's trailing digit is load-bearing.
- **Engine URLs are hardcoded** for each engine's ~2013 query format and HTML layout. Stale results mean an engine changed its markup, not a bug in the parser.
- **Cancellation is via shared global `BOOL` flags** `Cancel1` (admin scan) and `Cancel2` (search), polled inside the loops to `Break`. There is no thread synchronization beyond these flags.
- **Discontinued — archival edits only.** Do not introduce new component packages or restructure into multiple units.
