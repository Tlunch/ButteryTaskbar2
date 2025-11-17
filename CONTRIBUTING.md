# Contributing to Buttery Taskbar 2

This guide explains how the Buttery Taskbar 2 repository works and how to create releases.

## Table of Contents

- [Repository Overview](#repository-overview)
- [Technology Stack](#technology-stack)
- [Project Structure](#project-structure)
- [Build System](#build-system)
- [Development Setup](#development-setup)
- [Building the Project](#building-the-project)
- [Version Management](#version-management)
- [Creating a Release](#creating-a-release)
- [Testing and Debugging](#testing-and-debugging)

## Repository Overview

Buttery Taskbar 2 is a Windows application written in the [Jai programming language](https://github.com/BSVino/JaiPrimer/blob/master/JaiPrimer.md) that provides advanced taskbar auto-hide functionality. The application runs in the system tray and manages the Windows taskbar visibility based on user interaction patterns.

### Key Features

- Taskbar hiding controlled by Windows key press
- Mouse scroll activation at screen edge
- Graphical configuration interface
- Automatic update checking
- Auto-start at login option
- Configuration persistence

## Technology Stack

- **Language**: Jai (Jonathan Blow's programming language)
- **Platform**: Windows (Win32 API)
- **Graphics**: Simp library (Jai graphics library)
- **Build System**: Jai metaprogramming-based build system

## Project Structure

```
ButteryTaskbar2/
├── first.jai           # Build system entry point
├── main.jai            # Main application code
├── config.jai          # GUI configuration interface
├── shared.jai          # Shared constants (version, URLs)
├── windows-extra.jai   # Additional Windows API bindings
├── winhttp.jai         # HTTP client for update checking
├── out/                # Build output directory (gitignored)
├── README.md           # User documentation
├── CHANGELOG.md        # Version history
└── CONTRIBUTING.md     # This file
```

### Key Files

- **`first.jai`**: Contains the build script that compiles the application. This is the entry point for the Jai compiler.
- **`main.jai`**: Main application logic including window management, keyboard/mouse hooks, and system tray icon.
- **`config.jai`**: GUI implementation for the settings menu using the Simp graphics library.
- **`shared.jai`**: Defines the application version (`app_version`) and other shared constants.
- **`windows-extra.jai`**: Extended Windows API functions not in standard Jai modules.
- **`winhttp.jai`**: HTTP client implementation for checking GitHub releases.

## Build System

The build system is implemented in `first.jai` using Jai's metaprogramming capabilities. The build script:

1. Parses command-line arguments to determine build mode
2. Configures compiler options based on build type (debug/release)
3. Injects build-time constants (version, build date, release mode flag)
4. Handles icon embedding and manifest generation
5. Optionally runs the built executable

### Build Modes

- **Debug mode** (`jai first.jai`): Creates `out/debug.exe` with full debugging symbols
- **Release mode** (`jai first.jai - release`): Creates `out/buttery-taskbar.exe` with optimizations
- **Dev mode** (`jai first.jai - dev`): Builds and immediately runs the debug build

### Build Arguments

- `debug`: Enable debug mode
- `dev`: Build debug version and run it
- `release`: Build optimized release version
- `norun`: Don't auto-run after building

### Release Mode Optimizations

When building in release mode, the build system applies aggressive optimizations:

- Uses LLVM backend for better optimization
- Disables all runtime checks (bounds, null pointer, overflow)
- Enables dead code elimination
- Removes stack traces and crash backtraces
- Nullifies all `print`, `log`, and `log_error` statements to eliminate overhead

## Development Setup

### Prerequisites

1. **Jai Compiler**: You need access to the Jai programming language compiler. As of writing, Jai is in closed beta and requires access from Jonathan Blow.

2. **Windows Development Environment**: The project targets Windows and requires Windows SDK headers.

3. **Icon File** (optional): If you want to use a custom icon, create a `local_environment.jai` file:
   ```jai
   LOCAL_ENV_ICON_FILE_PATH :: "path/to/icon.bmp";
   ```
   This file is gitignored, so it won't be committed.

### Cloning the Repository

```bash
git clone https://github.com/LuisThiamNye/ButteryTaskbar2.git
cd ButteryTaskbar2
```

## Building the Project

### Debug Build

To build a debug version:

```bash
jai first.jai
```

This creates `out/debug.exe` with debugging symbols and runtime checks enabled.

### Release Build

To build the release version:

```bash
jai first.jai - release
```

This creates `out/buttery-taskbar.exe` with full optimizations.

### Build and Run

To build and immediately run the debug version:

```bash
jai first.jai - dev
```

### Output

All build artifacts are placed in the `out/` directory, which is gitignored. The release build produces a single executable file (`buttery-taskbar.exe`) with no external dependencies.

## Version Management

### Version Number

The version is defined in `shared.jai`:

```jai
app_version :: "2.3.1";
```

### Version Update Checklist

When preparing a new release, update the version in these locations:

1. **`shared.jai`**: Update the `app_version` constant
2. **`CHANGELOG.md`**: Add a new entry describing the changes

The build system automatically embeds the version string and build date into the compiled executable.

## Creating a Release

Follow these steps to create a new release:

### 1. Update Version Information

Update `shared.jai`:
```jai
app_version :: "2.4.0";  // Update to new version
```

### 2. Update CHANGELOG.md

Add a new entry at the top of `CHANGELOG.md`:
```markdown
## 2.4.0
[Date]

- Feature: Description of new feature
- Fix: Description of bug fix
- etc.
```

### 3. Commit Version Changes

```bash
git add shared.jai CHANGELOG.md
git commit -m "Bump version to 2.4.0"
git push origin main
```

### 4. Build the Release Binary

```bash
jai first.jai - release
```

This creates `out/buttery-taskbar.exe`.

### 5. Test the Release Build

Before releasing, thoroughly test the built executable:

1. Run `out/buttery-taskbar.exe`
2. Verify the tray icon appears
3. Test taskbar hiding functionality
4. Open the settings menu (right-click tray icon)
5. Verify version number is correct in the settings menu
6. Test all configuration options
7. Verify update checking works (should show "up to date" for current version)

### 6. Create Git Tag

```bash
git tag 2.4.0
git push origin 2.4.0
```

### 7. Create GitHub Release

1. Go to https://github.com/LuisThiamNye/ButteryTaskbar2/releases/new
2. Choose the tag you just created (e.g., `2.4.0`)
3. Set the release title to the version number (e.g., `2.4.0`)
4. Copy the changelog entry into the release description
5. Upload `out/buttery-taskbar.exe` as the release asset
6. Publish the release

### 8. Verify Update Mechanism

After publishing:

1. Run an older version of Buttery Taskbar
2. Open the settings menu
3. Verify it detects the new version and shows an update button

## Testing and Debugging

### Manual Testing

Since this is a system utility, testing is primarily manual:

1. **Taskbar Hiding**: Verify taskbar hides when not in use
2. **Windows Key**: Press and hold Windows key, verify taskbar shows
3. **Start Menu**: Open Start menu, verify taskbar remains visible
4. **Scroll Activation**: Scroll at bottom of screen, verify Start menu opens
5. **Settings Menu**: Right-click tray icon, verify menu appears
6. **Configuration Persistence**: Change settings, restart app, verify settings persist
7. **Keyboard Shortcut**: Test Ctrl+Win+F11 toggle (if enabled)

### Debug Mode

For debugging, use the debug build which includes:

- Full logging output (print/log statements active)
- Runtime checks (bounds, null pointer, overflow)
- Memory debugger
- Assert statements
- Stack traces on crash

### Common Issues

- **Icon not showing**: Ensure `local_environment.jai` points to valid bitmap file
- **Build failures**: Check that Jai compiler is up to date
- **Runtime errors**: Build and run debug version for detailed error messages

## Configuration File

Buttery Taskbar stores settings in:
```
%APPDATA%\Roaming\Buttery Taskbar\config
```

The configuration is a binary file containing:
- Version number
- Enable/disable state
- Keyboard shortcut enable state
- Scroll activation enable state
- Auto-start setting
- Windows auto-hide preference

## Update Checking Mechanism

The application checks for updates by:

1. Making an HTTP GET request to GitHub API: `/repos/LuisThiamNye/ButteryTaskbar2/releases/latest`
2. Parsing the response to extract the latest version tag
3. Comparing with the current `app_version`
4. Displaying update notification in settings menu if newer version exists

The update check runs in a background thread and does not block the application.

## Performance Considerations

Since Buttery Taskbar runs continuously in the background:

- Release builds disable all logging to reduce overhead
- Window polling is optimized to minimize CPU usage
- Memory allocations are carefully managed
- Dead code elimination removes unused functionality

## Additional Resources

- [Jai Primer](https://github.com/BSVino/JaiPrimer/blob/master/JaiPrimer.md) - Introduction to Jai language
- [Original Buttery Taskbar](https://github.com/CrypticButter/ButteryTaskbar) - Previous version and rationale
- [Windows API Documentation](https://docs.microsoft.com/en-us/windows/win32/) - For understanding Win32 API usage

## License

This project is licensed under the MIT License - see the LICENSE file for details.
