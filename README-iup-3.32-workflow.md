# Build IUP 3.32 (MSVC optimized) — GitHub Actions

This workflow builds **the core Windows IUP GUI library from official upstream IUP 3.32 source** on `windows-2022` using **Microsoft Visual C++ 2022** (x64). It does **not** copy a prebuilt IUP DLL.

## Installation

Copy `.github/workflows/build-iup-3.32-msvc.yml` into your GitHub repository at the same path. Commit and push, then open **Actions → Build IUP 3.32 (MSVC optimized) → Run workflow**. By default manual runs enable AVX2; you can uncheck it for broader x64 compatibility.

## Build process

1. Download `iup-3.32_Sources.zip` from IUP's official SourceForge release.
2. Verify SHA-256: `f0445c4200bdf0531cd47d6c84196464f378e3152e27fabd472e7e8936ac32e3` (SourceForge's published hash).
3. Initialize Visual Studio 2022 x64 tools. Install GNU Make; use the Tecmake Makefile in `iup/src`, so only the core IUP library is compiled (no Lua, CD or IM needed).
4. Compile `dll17_64`, with MSVC flags `/O2 /Oi /GL /Gy /GF`; optional `/arch:AVX2`; link with `/LTCG /OPT:REF /OPT:ICF /INCREMENTAL:NO`.
5. Check that `iup.dll` is x64 and exports `IupOpen` and `IupVersion`; compile/link a minimal test program against the generated import library.
6. Publish **`iup-3.32-msvc-x64`**, containing the ZIP:
   - `bin/iup.dll`
   - `lib/iup.lib`
   - `include/` (public IUP headers)
   - `BUILD-INFO.txt`

Use `bin/iup.dll` alongside Clumsy's executable. **If recompiling Clumsy against this DLL, use the matching header and import library (`iup.lib`) from this same artifact.** Ensure you are using x64 on both sides. A Visual C++ runtime redistributable may be required on the target machine.

## Notes

- AVX2 builds may not run on older CPUs. Turn off the **avx2** input for generic x64.
- This workflow builds only core `iup.dll`; optional IUP libraries such as `iupimglib`, Lua bindings, CD, IM, OpenGL, and web controls require additional steps and/or dependencies.
- The workflow runs when manually triggered and when its YAML is pushed.
- This workflow has **not yet been executed on a GitHub-hosted Windows runner**. The build command follows the official Tecmake/Windows documentation; compiler/source compatibility will be verified on the first run.

Upstream links:
- https://www.tecgraf.puc-rio.br/iup/en/
- https://sourceforge.net/projects/iup/files/3.32/Docs%20and%20Sources/
- https://www.tecgraf.puc-rio.br/iup/en/buildwin.html
