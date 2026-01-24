# Building .deb Package - Debian 13

## Prerequisites

```bash
# Install system dependencies
./build_linux.sh -u

# Add missing GStreamer dependency
sudo apt-get install -y libgstreamer-plugins-base1.0-dev
```

## Build Dependencies

```bash
# Build vendored dependencies (20-30 min, first time only)
./build_linux.sh -d
```

This creates `deps/build/`. Our FHS build will reuse it.

## Build and Package

```bash
# Configure with FHS (reuses deps/build from build_linux.sh)
cmake -S . -B build-deb \
    -G "Ninja Multi-Config" \
    -DCMAKE_BUILD_TYPE=Release \
    -DSLIC3R_FHS=ON \
    -DCMAKE_INSTALL_PREFIX=/usr \
    -DSLIC3R_PCH=ON \
    -DORCA_TOOLS=ON \
    -DDEP_BUILD_DIR="${PWD}/deps/build"

# Build
cmake --build build-deb --config Release --target OrcaSlicer
# Compiles translation files for internationalization (i18n)
./scripts/run_gettext.sh

# Generate .deb package
cd build-deb && cpack -G DEB -C Release && cd ..
```

Package location: `build-deb/orcaslicer_*.deb` (~136 MB)

## Install

```bash
sudo dpkg -i build-deb/orcaslicer_*.deb
```
