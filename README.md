# obs-ndi

## YOU HAVE STUMBLED UPON THE DEV BRANCH FOR V5.0.0

Network A/V in OBS Studio with NewTek's NDI (5.0) technology.

[![Twitter](https://img.shields.io/twitter/url/https/twitter.com/fold_left.svg?style=social&label=Follow%20%40LePalakis)](https://twitter.com/LePalakis)
[![Financial Contributors on Open Collective](https://opencollective.com/obs-websocket/all/badge.svg?label=financial+contributors)](https://opencollective.com/obs-websocket)

## Features
- **NDI Input (Source)**: Receive NDI video and audio in OBS
- **NDI Output**: Transmit video and audio from OBS via NDI
- **NDI Filter** (a.k.a NDI Dedicated Output): Transmit a single input (source) or scene via NDI

## Downloads
Binaries for Windows, macOS and Linux are available in the [Releases](https://github.com/Palakis/obs-ndi/releases) section.

## GitHub Actions & CI
The scripts contained in [.github/scripts](.github/scripts) can be used to build and package the plugin and take care of setting up obs-studio as well as its own dependencies. A default workflow for GitHub Actions is also provided and will use these scripts.

### Retrieving build artifacts
Each build produces installers and packages that you can use for testing and releases. These artifacts can be found on the action result page via the "Actions" tab in your GitHub repository.

#### Building a Release
Simply create and push a tag and GitHub Actions will run the pipeline in Release Mode. This mode uses the tag as its version number instead of the git ref in normal mode.

### Signing and Notarizing on macOS
On macOS, Release Mode builds can be signed and sent to Apple for notarization if the necessary codesigning credentials are added as secrets to your repository. **You'll need a paid Apple Developer Account for this.**

- On your Apple Developer dashboard, go to "Certificates, IDs & Profiles" and create two signing certificates:
    - One of the "Developer ID Application" type. It will be used to sign the plugin's binaries
    - One of the "Developer ID Installer" type. It will be used to sign the plugin's installer
- Using the Keychain app on macOS, export these two certificates and keys into a .p12 file **protected with a strong password**
- Encode the .p12 file into its base64 representation by running `base64 YOUR_P12_FILE`
- Add the following secrets in your Github repository settings:
    - `MACOS_SIGNING_APPLICATION_IDENTITY`: Name of the "Developer ID Application" signing certificate generated earlier
    - `MACOS_SIGNING_INSTALLER_IDENTITY`: Name of "Developer ID Installer" signing certificate generated earlier
    - `MACOS_SIGNING_CERT`: Base64-encoded string generated above
    - `MACOS_SIGNING_CERT_PASSWORD`: Password used to generate the .p12 certificate
    - `MACOS_NOTARIZATION_USERNAME`: Your Apple Developer account's username
    - `MACOS_NOTARIZATION_PASSWORD`: Your Apple Developer account's password (use a generated "app password" for this)

## Local build

Windows:
In a **PowerShell 7** terminal, `cd` to the folder you want the code in:
```
PS …> git clone --recursive https://github.com/obsproject/obs-studio.git
PS …> pushd obs-studio
PS …\obs-studio> Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
PS …\obs-studio> git config --global --add safe.directory absolute/path/to/your/repo/obs-studio
PS …\obs-studio> .\CI\build-windows.ps1
PS …\obs-studio> popd
PS …> git clone https://github.com/Palakis/obs-ndi.git
PS …> pushd obs-ndi
PS …\obs-ndi> .\.github\scripts\Build-Windows.ps1
PS …\obs-ndi> xcopy .\release\ ..\obs-studio\build64\rundir\RelWithDebInfo\ /s /y 
```
You only need to run the last two commands when coding the plugin.  
Be sure to close OBS before re-copying files; you can do this with `kill -name obs64`

This plugin can be performance sensitive, so for the best A/V quality you may want to run a
Release build of **both** OBS and the plugin:
```
PS …\obs-studio> .\CI\build-windows.ps1 -BuildConfiguration Release
…
PS …\obs-ndi> .\.github\scripts\Build-Windows.ps1 -Configuration Release
PS …\obs-ndi> xcopy .\release\ ..\obs-studio\build64\rundir\Release\ /s /y 
```

## Fix clang errors

1. Install clang-format https://releases.llvm.org/ and optionally add it to your `PATH`.
2. Launch Visual Studio Code.
3. Install the `Clang-Format` extension and if clang-format is not in your `PATH` then configure clang-format executable path.
4. In a document, press Shift-Alt-F (VS may ask to set your default formatter; set to `Clang-Format`).

You can optionally set Visual Studio Code to auto-format the file when saved.
