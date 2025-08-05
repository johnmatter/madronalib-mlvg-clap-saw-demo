# CLAP Saw Demo Plugin

A complete CLAP audio plugin demonstrating madronalib DSP and MLVG GUI integration.

## Features

- CLAP Specification Compliant: Full CLAP plugin with parameter automation, MIDI input, and GUI
- madronalib DSP: Sawtooth oscillator, lowpass filter, and ADSR envelope
- MLVG GUI: Vector graphics interface with embedded fonts and responsive layout
- Polyphonic: Multi-voice synthesis with proper voice management
- Cross-Platform: Works in Reaper, Bitwig, and other CLAP hosts

## Architecture

```
CLAP Host ↔ CLAPExport.h ↔ ClapSawDemo (SignalProcessor)
                ↓
           PlatformView ↔ ClapSawDemoGUI (MLVG Interface)
```

### Core Components
- `ClapSawDemo`: Main DSP processor with polyphonic voice management
- `ClapSawDemoGUI`: MLVG-based GUI with centralized styling and layout
- `CLAPExport.h`: Generic CLAP wrapper providing full specification compliance
- Embedded Resources: Fonts and assets compiled into plugin binary

## Quick Start

### Build Commands
```bash
# Initialize and update submodules (required for first-time setup)
git submodule update --init --recursive

# Configure and build
cmake -Bbuild -DCMAKE_BUILD_TYPE=Release -DCOPY_AFTER_BUILD=ON
cmake --build build -j --target clap-saw-demo
```

### Installation
The plugin is automatically installed to:
- macOS: `~/Library/Audio/Plug-Ins/CLAP/`
- Linux: `~/.clap/`

## Development

### Project Structure
```
src/
├── clap-saw-demo.h          # Main processor header
├── clap-saw-demo.cpp        # DSP implementation
├── clap-saw-demo-gui.h      # GUI header
├── clap-saw-demo-gui.cpp    # MLVG GUI implementation
└── clap-saw-demo-entry.cpp  # CLAP entry point

libs/
├── madronalib/              # DSP library
├── mlvg/                    # Vector graphics GUI
├── clap/                    # CLAP specification
└── clap-helpers/            # CLAP utilities
```

### Key Patterns

#### Parameter Definition
```cpp
void buildParameterDescriptions() {
  auto& params = getParameterTree().descriptions;
  params.push_back(std::make_unique<ml::ParameterDescription>(ml::WithValues{
    {"name", "f0"},
    {"range", {10.0f, 10000.0f}},
    {"plaindefault", 1000.0f},  // Real value, not normalized
    {"units", "Hz"}
  }));
}
```

#### GUI Widget Creation
```cpp
void makeWidgets() {
  _view->_widgets.add_unique<DialBasic>("f0", ml::WithValues{
    {"bounds", {_drawingProperties.getFloatProperty("left_col_x"), // these can be literals, macros, whatever you prefer
                _drawingProperties.getFloatProperty("top_row_y"),
                _drawingProperties.getFloatProperty("large_dial_width"),
                _drawingProperties.getFloatProperty("large_dial_height")}},
    {"log", true},
    {"visible", true},
    {"draw_number", true},
    {"text_size", _drawingProperties.getFloatProperty("dial_text_size")},
    {"param", "f0"}
  });
}
```

#### Embedded Font Loading
```cpp
void initializeResources(NativeDrawContext* nvg) {
  // Load embedded fonts from memory
  _resources.fonts["d_din"] = std::make_unique<ml::FontResource>(
    nvg, "d_din", resources::D_DIN_otf, resources::D_DIN_otf_size, 0);
}
```
## Contributing

This plugin is an attempt to demonstrate best practices for CLAP development with madronalib and MLVG. Contributions are welcome!

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Acknowledgments

- madronalib: DSP framework by Madrona Labs
- MLVG: Vector graphics GUI library by Madrona Labs
- CLAP: Open audio plugin standard
- NanoVG: Vector graphics rendering
