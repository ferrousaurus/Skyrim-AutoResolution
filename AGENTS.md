# Repository guidance

## Build and verification
- This is a Windows x64 SKSE DLL, not a standalone application. The CMake project/target and DLL name are `PreSSEDisplayTweaks`; the README still contains template setup instructions.
- Use an x64 Visual Studio developer shell with `cl.exe` and Windows SDK `rc.exe` available. Presets use Ninja, C++23, and the static MSVC runtime / `x64-windows-static` vcpkg triplet. `mise.toml` pins CMake 3.31 and Ninja 1.13 but does not set up MSVC.
- Run from the repository root, configuring before building:
  ```powershell
  cmake --preset release
  cmake --build build/release --target PreSSEDisplayTweaks --parallel 4
  ```
  For Debug, substitute `debug` in both commands. There are configure presets only, so do not use `cmake --build --preset release`. The `mise` build task also assumes `build/release` has already been configured.
- Configure requires Git and initially network access: `cmake/dependencies.cmake` clones pinned CommonLibSSE-NG and vcpkg revisions into ignored `external/` directories, initializes submodules, and bootstraps vcpkg. Existing clones at other revisions or incomplete directories cause a fatal error; inspect them before replacing anything. Dependency pins live in that file; the package baseline and dependencies live in `vcpkg.json`.
- No repository test suite or lint/typecheck task is defined; CMake forces dependency `BUILD_TESTS` off. Verify by building, then use Skyrim/SKSE for runtime checks. Logs are written to SKSE's log directory as `PreSSEDisplayTweaks.log`.

## Code and runtime constraints
- `src/plugin.cpp` is the entrypoint: logging setup → `SKSE::Init` → `GetINISettings` → `ReadWriteDisplayTweaksINI`. Actual behavior is in `src/Settings.cpp`; Events/Hooks/Manager/Utils `.cpp` files are empty scaffolding, not active subsystems.
- Sources and headers are explicitly registered in `cmake/sourcelist.cmake` and `cmake/headerlist.cmake`; update these when adding files. `include/PCH.h` is a required precompiled header supplying CommonLib/SKSE types and the `logger` alias.
- Runtime paths are relative to the game's working directory, not the DLL directory. The plugin creates/updates `Data/SKSE/Plugins/PreSSEDisplayTweaks.ini` (`[Settings] fRatio`), then edits `[Render] Resolution` in an existing SSE Display Tweaks INI. `SSEDisplayTweaks_custom.ini` takes precedence over `SSEDisplayTweaks.ini`; neither is created if absent.
- Preserve physical-pixel resolution detection via `EnumDisplaySettingsW(nullptr, ENUM_CURRENT_SETTINGS, ...)`, not `GetSystemMetrics` (the fork's core fix). `fRatio` is clamped to `[0.1, 1.0]`, non-finite values fall back to `1.0`, and scaled dimensions truncate to integers. Failed display queries must leave the Display Tweaks INI unchanged.
- For resolution changes, manually check custom/default INI selection, ratio clamping/scaling, and missing-INI behavior; a build alone does not exercise Windows display queries or file writes.
- Keep plugin versions aligned in `CMakeLists.txt` and `vcpkg.json`; Windows version resources are generated from `cmake/version.rc.in`, not edited in the build directory.
