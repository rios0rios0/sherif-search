# Contributing

> **This project was discontinued around 2013-2014 and is no longer actively maintained.**
> The repository is preserved as a historical reference. No new features or bug fixes are planned.

## Historical Build Information

This project was built using the following tools and technologies:

- **Language:** Object Pascal (Delphi 7)
- **IDE:** Borland Delphi 7
- **UI Framework:** VCL (Visual Component Library)
- **Libraries:** `TIEParser` (IE COM-based HTML parser), `TIdHTTP` (Indy), WinINet API
- **Threading:** Windows API `CreateThread`

### Build Steps (Historical)

1. Open `SS.dpr` in Borland Delphi 7 (or compatible IDE)
2. Ensure `IEParser` and `Indy` component packages are installed
3. Compile the project (`Ctrl+F9`)
4. Run `SS.exe` on a Windows machine with internet connection

> **Note:** `Clear.bat` cleans Delphi build artifacts.
