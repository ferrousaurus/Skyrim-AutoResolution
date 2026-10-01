# AutoResolution

Fork of [QTR-Modding's AutoResolution](https://github.com/QTR-Modding/AutoResolution) that replaces [GetSystemMetrics](https://learn.microsoft.com/en-us/windows/win32/api/winuser/nf-winuser-getsystemmetrics) with [EnumDisplaySettingsW](https://learn.microsoft.com/en-us/windows/win32/api/winuser/nf-winuser-enumdisplaysettingsw). Credit to [erdtreefaithful](https://www.nexusmods.com/profile/erdtreefaithful) for identifying the issue.

[CommonlibSSE-NG](https://github.com/alandtse/CommonlibSSE-NG) is cloned into `external/alandtse/CommonlibSSE-NG`. [vcpkg](https://github.com/microsoft/vcpkg) is cloned into `external/microsoft/vcpkg`.

## Dependencies
- [cmake](https://cmake.org/)
- [ninja](https://ninja-build.org/)
- [msvc](https://aka.ms/vs/stable/vs_BuildTools.exe)

## CI

Pull requests run a Windows x64 Release build with GitHub Actions. A successful run uploads `PreSSEDisplayTweaks.dll` as the `PreSSEDisplayTweaks-windows-x64-release` artifact for 14 days. To block merges until it passes, make the `Windows x64 Release` check required in the repository's branch protection or ruleset settings. CI verifies compilation only; runtime behavior still needs to be checked in Skyrim/SKSE.

To run the same build locally from an x64 Visual Studio developer shell:

```powershell
cmake --preset release
cmake --build build/release --target PreSSEDisplayTweaks --parallel 4
```

#### THINGS TO EDIT

1. In LICENSE:
- **`YEAR`**
- **`YOURNAME`**
2. CMakeLists.txt
- **`AUTHORNAME`**
- **`MDDNAME`**
- (optional) Your plugin version. Default: `0.1.0.0`
3. vcpkg.json
- **`name`**: Your plugin's name.
- **`version-string`**: Your plugin version. Default: `0.1`
