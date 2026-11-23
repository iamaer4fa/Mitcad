# MitCad Debian Package Build Instructions

## Build Requirements

- CMake 3.24 or newer
- Ninja build system
- g++ or Clang compiler (C++17)
- Qt 6.8+ with Widgets, OpenGL, OpenGLWidgets, Svg, Network components
- OpenCASCADE Technology (OCCT) 8.0.1 or newer
- Rust toolchain (rustc 1.65+)
- vcpkg for C++ dependency management
- libxcb-dev, libxcb-image0-dev, libxcb-keysyms1-dev
- libglib2.0-dev (for GLib integration)
- libjson-c-dev (for JSON handling)
- libsqlite3-dev (for database features)
- libcurl4-openssl-dev (for network operations)
- libssl-dev (for TLS/SSL support)
- libicu-dev (for Internationalization)
- libboost-dev and libboost-iostreams-dev (for various features)
- libpcl-dev or similar (for point cloud support, if enabled)
- cycles-renderer dependencies (for GPU rendering)
- Open Image Denoise (OIDN) dependencies

## Packaging Structure

The Debian package follows standard Debian filesystem hierarchy:

- `DEBIAN/control` - Package metadata
- `DEBIAN/postinst` - Post-installation script
- `DEBIAN/prerm` - Pre-removal script
- `DEBIAN/postrm` - Post-removal script
- `usr/bin/mitcad` - Application launcher script
- `usr/lib/mitcad/` - Application libraries and configuration
- `usr/share/applications/mitcad.desktop` - Desktop integration
- `usr/share/icons/hicolor/*/apps/mitcad.png` - Application icons
- `usr/share/mime/packages/mitcad.xml` - MIME type associations
- `usr/share/doc/mitcad/` - Documentation and licenses

## Build Commands

1. Install system dependencies (see above)
2. Clone and setup vcpkg: `git clone https://github.com/microsoft/vcpkg.git && ./vcpkg/bootstrap-vcpkg.sh`
3. Install Qt 6.8+ development packages
4. Install OpenCASCADE 8.0.1 development packages
5. Build the application:
   ```
   mkdir build && cd build
   cmake -DCMAKE_BUILD_TYPE=Release -DVCPKG_ROOT=~/vcpkg ..
   cmake --build --release
   ```
6. Install to a temporary prefix:
   ```
   cmake --install . --prefix /tmp/mitcad-install
   ```
7. Create the Debian package:
   ```
   cd /tmp/mitcad-install
   ar rcs /home/user/dist/mitcad_0.70.0_amd64.deb *
   # Or use dpkg-deb to create from a proper source tree
   ```

## Installation Instructions

```bash
# Install the package
sudo dpkg -i ./dist/mitcad_0.70.0_amd64.deb

# Or using apt (after placing in a repo)
sudo apt install ./dist/mitcad_0.70.0_amd64.deb
```

The application can then be launched from the application menu or by running `mitcad` from the terminal.

## Uninstallation Instructions

```bash
sudo apt remove mitcad
# Or
sudo dpkg -r mitcad
```

This will remove the application and its desktop integration. User configuration files in `~/.config/Mitcad/` and `~/.local/share/Mitcad/` are preserved.

## Runtime Dependencies

The application requires the following runtime libraries:

- libQt6Core.so.6 (Qt 6 core module)
- libQt6Gui.so.6 (Qt 6 GUI module)
- libQt6Widget.so.6 (Qt 6 widgets module)
- libQt6OpenGL.so.6 (Qt 6 OpenGL module)
- libQt6OpenGLWidgets.so.6 (Qt 6 OpenGL widgets module)
- libQt6Svg.so.6 (Qt 6 SVG module)
- libQt6Network.so.6 (Qt 6 network module)
- libQt6DBus.so.6 (Qt 6 D-Bus module)
- libqt6keychain.so.1 (Qt Keychain for credentials)
- libOpenCASCADE (OCCT core libraries)
- libxcb-xxx (X11 transport)
- libglib-2.0-0 (GLib library)
- libjson-c5 (JSON manipulation)

## Supported Architecture

- amd64 / x86_64 (officially supported)
- arm64 / aarch64 (may work with compatibility layers)

## Known Limitations

- Full build requires OCCT 8.0.1+ and Qt 6.8+
- GPU rendering (Cycles) requires compatible NVIDIA/AMD hardware and drivers
- Some file format imports may have limitations (see user guide)
- Automatic update checking requires network access and a valid release key
- The application bundles no external libraries beyond what the system provides
- Windows and macOS installers are separate distributions

## Package Contents Summary

- Executable launcher at usr/bin/mitcad
- Desktop file at usr/share/applications/mitcad.desktop
- Icons at usr/share/icons/hicolor/*/apps/mitcad.png (16, 24, 32, 48, 64, 96, 128, 256)
- MIME type associations at usr/share/mime/packages/mitcad.xml
- Thumbnailer script at usr/share/bin/mitcad-thumbnailer
- Documentation at usr/share/doc/mitcad/LICENSE and usr/share/doc/mitcad/THIRD-PARTY-NOTICES.txt