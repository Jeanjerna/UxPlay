# Windows Installation Guide for UxPlay

This guide explains how to build, install, and run the UxPlay AirPlay server on Microsoft Windows 10 or 11 (64-bit).

---

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Step 1: Install and Update MSYS2](#step-1-install-and-update-msys2)
3. [Step 2: Install Compiler & Toolchain](#step-2-install-compiler--toolchain)
4. [Step 3: Install Dependencies & GStreamer](#step-3-install-dependencies--gstreamer)
5. [Step 4: Build UxPlay](#step-4-build-uxplay)
6. [Step 5: Run and Configure UxPlay](#step-5-run-and-configure-uxplay)
7. [Firewall Configuration](#firewall-configuration)
8. [Troubleshooting](#troubleshooting)

---

## Prerequisites
By default, UxPlay 1.74 includes a self-contained, built-in mDNS responder. **You do not need to install Bonjour or iTunes** unless you specifically choose to compile with Apple's Bonjour SDK (using `-DUSE_DNS_SD=1`).

---

## Step 1: Install and Update MSYS2
UxPlay must be compiled in a Unix-like environment on Windows. We use **MSYS2**:

1. Download and run the installer from the official [MSYS2 website](https://www.msys2.org/).
2. Accept the default installation folder (`C:\msys64`).
3. Once installed, search for **MSYS2 UCRT64** in the Windows Start menu and open it.
4. Run the following command to update MSYS2 packages:
   ```bash
   pacman -Syu
   ```
   *(If prompted, close the terminal window, reopen the **MSYS2 UCRT64** terminal, and run `pacman -Syu` again to finish the update).*

---

## Step 2: Install Compiler & Toolchain
In the MSYS2 UCRT64 terminal, run the following command to install GCC, CMake, Ninja (build system), and Git:
```bash
pacman -S mingw-w64-ucrt-x86_64-cmake mingw-w64-ucrt-x86_64-gcc mingw-w64-ucrt-x86_64-ninja git
```

---

## Step 3: Install Dependencies & GStreamer
Run the following command to install the required libraries (`libplist`, `gstreamer`) and GStreamer plugins:
```bash
pacman -S mingw-w64-ucrt-x86_64-libplist mingw-w64-ucrt-x86_64-gstreamer \
          mingw-w64-ucrt-x86_64-gst-plugins-base mingw-w64-ucrt-x86_64-gst-libav \
          mingw-w64-ucrt-x86_64-gst-plugins-good mingw-w64-ucrt-x86_64-gst-plugins-bad
```

---

## Step 4: Build UxPlay
1. In the MSYS2 UCRT64 terminal, navigate to your cloned UxPlay directory (UCRT64 maps drive letters like `D:` to `/d/`):
   ```bash
   cd "/d/Programming Project/UxPlay"
   ```
2. Create a build directory and enter it:
   ```bash
   mkdir build
   cd build
   ```
3. Configure the build using CMake:
   ```bash
   cmake -G Ninja ..
   ```
4. Build the executable:
   ```bash
   ninja
   ```
   This will generate `uxplay.exe` in the `build` directory.

5. *(Optional)* Install it system-wide inside MSYS2 so it can be run from any terminal directory:
   ```bash
   cmake --install . --prefix $HOME/../../ucrt64
   ```

---

## Step 5: Run and Configure UxPlay
To start the server, open a terminal and run:
```bash
./uxplay.exe
```

### Useful Command Options for Windows:
*   **Audio Session API (WASAPI)** (Recommended for better audio):
    ```bash
    ./uxplay.exe -as wasapisink
    ```
*   **Direct3D 12 Video Output** (Supports toggling full screen with `Alt+Enter`):
    ```bash
    ./uxplay.exe -vs d3d12videosink
    ```
*   **Specify custom Server display name**:
    ```bash
    ./uxplay.exe -n "My Windows Receiver"
    ```

---

## Firewall Configuration
To allow Apple devices to find and stream to your PC, you must allow `uxplay.exe` through the Windows Firewall:
1. When you run `uxplay.exe` for the first time, a Windows Security Alert will pop up.
2. Select **both Private and Public networks**, and click **Allow access**.
3. If you miss this prompt, go to **Windows Settings -> Update & Security -> Windows Security -> Firewall & network protection -> Allow an app through firewall**, and manually allow `uxplay.exe`.

---

## Troubleshooting
*   **No audio or sound is choppy**: Use the WASAPI sound output driver using the `-as wasapisink` option.
*   **Screen rendering lag / latency**: Ensure you are on a 5 GHz Wi-Fi band or ethernet connection. You can disable sync checks if you are only mirroring the screen (not videos):
    ```bash
    ./uxplay.exe -vsync no
    ```
*   **Crash on orientation change (Portrait <-> Landscape)**: Some older graphics cards may crash with `d3d12videosink` during orientation changes. If this happens, fallback to Direct3D 11:
    ```bash
    ./uxplay.exe -vs d3d11videosink
    ```
