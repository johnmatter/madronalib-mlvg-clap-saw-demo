# CLAP Development Tools

This directory contains development and testing utilities for CLAP plugin development.

## Available Tools

### clap-info
A utility for inspecting CLAP plugins and displaying their metadata, parameters, and capabilities.

Usage:
```bash
# Build the tool
make build-clap-info

# Inspect your plugin
make inspect-plugin
```

### clap-host  
A minimal CLAP host application for testing plugins. Provides a simple GUI to load and test CLAP plugins.

Usage:
```bash
# Build the tool
make build-clap-host

# Test your plugin
make test-plugin-host
```

## Building Tools

These tools are automatically built when `CLAP_DEMO_TESTS=ON` is set during CMake configuration:

```bash
cmake -Bbuild -DCLAP_DEMO_TESTS=ON
cmake --build build
```

## Available Make Targets

- `build-clap-info` - Build the clap-info inspection tool
- `inspect-plugin` - Run clap-info on the built plugin
- `build-clap-host` - Build the clap-host testing application  
- `test-plugin-host` - Run clap-host with the built plugin

## Dependencies

- clap-info: Minimal dependencies, should build easily
- clap-host: Requires Qt6 and may have additional system dependencies

## Notes

- Tools are built in their respective `tools/*/build/` directories
- The clap-host tool provides keyboard input for testing synthesizer plugins
- Use `make inspect-plugin` to validate your plugin's CLAP compliance 