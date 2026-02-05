# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Click'n'Translate is a Windows desktop application for instant screen translation and OCR. Users capture text from any screen area via global hotkeys, then translate or copy it to clipboard.

**Tech Stack**: Python 3, PyQt5, Windows APIs (winrt, ctypes)

## Commands

```bash
# Run from source
pip install -r requirements.txt
python main.py

# Build executable (creates dist/ClicknTranslate/)
build_release.bat
```

## Architecture

### Core Modules

- **main.py** - Main window, global hotkey handling, configuration management, history tracking
- **ocr.py** - OCR engine abstraction layer (Windows OCR, Tesseract, RapidOCR)
- **translater.py** - Translation engine abstraction (Google, Argos, MyMemory, Lingva, LibreTranslate)
- **settings_window.py** - Settings UI with bilingual support (Russian/English)

### Key Patterns

**Configuration Caching**: All modules cache config file reads with modification time tracking to reduce disk I/O.

**Hotkey Threading**: Global hotkeys are registered via `ctypes.windll.user32.RegisterHotKey`. Callbacks run in separate threads and use `_HotkeyDispatcher` (PyQt5 signal) to safely dispatch to the Qt main thread.

**History Storage**: Uses file locking (`msvcrt` on Windows) for thread-safe JSON history writes.

**Engine Abstraction**: Both OCR and translation support multiple engines with automatic fallbacks.

### Resource Paths

- App directory: `sys._MEIPASS` (PyInstaller) or script directory
- Data directory: `{APP_DIR}/data/` (auto-created, contains config.json and history files)
- Icons: `icons/` subdirectory

### Global Hotkeys (default)

- `Ctrl+Alt+C` - Quick Copy Mode (OCR + clipboard)
- `Ctrl+Alt+T` - Quick Translate Mode (OCR + translate)

## Platform Constraints

- **Windows-only** - Uses winrt APIs, ctypes for keyboard simulation, Windows Registry for autostart
- Windows OCR requires language packs installed via Windows Settings
