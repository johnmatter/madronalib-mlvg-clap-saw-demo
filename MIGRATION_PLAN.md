# CLAP Plugin Migration Plan

This document outlines the comprehensive plan for moving the CLAP plugin example from this repository into the mlvg repository under `examples/clap-plugin`.

## Current Status Assessment

Submodule Status:
- madronalib: using `johnmatter/madronalib:header-cleanup` (contains necessary changes)
- mlvg: using upstream `madronalabs/mlvg:main` (Randy's recent commits fix the same issues as my fork)
- Clean build verified and working

## Key Architectural Decisions

1. Dependency Management: mlvg expects madronalib to be installed as a system library (`/usr/local/include/madronalib`, `/usr/local/lib`), not as a submodule
2. Example Integration: Follow mlvg's pattern of adding a new option (like `BUILD_SDL2_APP`) for the CLAP plugin
3. CMake Structure: Integrate directly into mlvg's top-level CMakeLists.txt rather than separate CMake files

## Migration Plan

### Phase 1: Prepare Dependencies

mlvg's madronalib dependency means we have to give it access to the `johnmatter/madroanlib:header-cleanup` branch

This requires either:
- Option A (Recommended): Merge your `header-cleanup` branch into `madronalabs/madronalib:master`
- Option B: Modify mlvg to expect a custom madronalib build location

We'll go with Option A.

### Phase 2: Directory Structure Migration

Move files to `mlvg/examples/clap-plugin/`:

```
mlvg/examples/clap-plugin/
├── src/
│   ├── clap-saw-demo.cpp
│   ├── clap-saw-demo.h
│   ├── clap-saw-demo-gui.cpp
│   ├── clap-saw-demo-gui.h
│   └── clap-saw-demo-entry.cpp
├── cmake/
│   └── clap-saw-demo.plist.in
├── scripts/
│   ├── test-plugin.sh
│   ├── debug-plugin.sh
│   └── ...
└── README.md
```

### Phase 3: CMake Integration

1. Add option to mlvg's CMakeLists.txt:
   ```cmake
   option(BUILD_CLAP_EXAMPLE "Build CLAP plugin example" OFF)
   ```

2. Add CLAP plugin target section (similar to the SDL2 app section at lines 428-528):
   ```cmake
   if(BUILD_CLAP_EXAMPLE)
       # Add CLAP dependencies (clap, clap-helpers, readerwriterqueue)
       # Create plugin target
       # Set up CLAP-specific properties and linking
   endif()
   ```

### Phase 4: Dependency Integration

Since the current project includes several libraries as submodules that mlvg doesn't have:

1. clap & clap-helpers: Add as git submodules to mlvg or include as part of the example
2. readerwriterqueue: Add as submodule or include source
3. clap-validator, clap-host, clap-info: Add as optional tools for testing (scoped to examples/clap-plugin/tools/)

### Phase 5: Resource Management

1. Move font resources and handle the `create_resources` call
2. Ensure generated resource files are properly included

## Recommended Implementation Steps

### Step 1: Fork and Branch

```bash
# Fork madronalabs/mlvg to your GitHub account
# Clone your fork and create a feature branch
git clone https://github.com/johnmatter/mlvg.git
cd mlvg
git checkout -b add-clap-example
```

### Step 2: Add CLAP Dependencies

```bash
# Add required submodules to mlvg (following mlvg's external/ structure)
git submodule add https://github.com/free-audio/clap.git source/external/clap
git submodule add https://github.com/free-audio/clap-helpers.git source/external/clap-helpers
git submodule add https://github.com/cameron314/readerwriterqueue.git source/external/readerwriterqueue

# Optional testing tools (scoped to CLAP example)
git submodule add https://github.com/free-audio/clap-validator.git examples/clap-plugin/tools/clap-validator
git submodule add https://github.com/free-audio/clap-host.git examples/clap-plugin/tools/clap-host
git submodule add https://github.com/free-audio/clap-info.git examples/clap-plugin/tools/clap-info
```

### Step 3: Migrate Source Files

```bash
mkdir -p examples/clap-plugin/src
mkdir -p examples/clap-plugin/cmake
mkdir -p examples/clap-plugin/scripts
mkdir -p examples/clap-plugin/tools
# Copy files from current repo
```

### Step 4: Modify mlvg's CMakeLists.txt

Add the CLAP plugin build logic similar to how the SDL2 app is integrated, including:
- Option definition
- Source file globbing
- Target creation with proper dependencies
- Platform-specific settings (bundle on macOS, etc.)
- Resource compilation
- Testing targets

### Step 5: Handle madronalib Dependency

Option A (Recommended): Work with Randy to merge your `header-cleanup` branch into upstream madronalib, then use standard system installation.

Option B: Add CMake option to specify custom madronalib location:
```cmake
option(MADRONALIB_ROOT "Path to custom madronalib installation" "")
```

## Files to Migrate

### Source Files
- `src/clap-saw-demo.cpp`
- `src/clap-saw-demo.h`
- `src/clap-saw-demo-gui.cpp`
- `src/clap-saw-demo-gui.h`
- `src/clap-saw-demo-entry.cpp`

### Build System Files
- `cmake/clap-saw-demo.plist.in`
- Key portions of `CMakeLists.txt` (integrated into mlvg's)

### Testing Infrastructure
- `scripts/test-plugin.sh`
- `scripts/debug-plugin.sh`
- `scripts/test-plugin.bat`
- `scripts/debug-plugin.bat`

### Dependencies (as submodules)
- `source/external/clap`
- `source/external/clap-helpers`
- `source/external/readerwriterqueue`
- `examples/clap-plugin/tools/clap-validator`
- `examples/clap-plugin/tools/clap-host`
- `examples/clap-plugin/tools/clap-info`

### Resources
- Font resources from `libs/mlvg/examples/app/resources`

## Benefits of This Approach

1. Clean Integration: Follows mlvg's established patterns
2. Optional Building: CLAP example is optional, doesn't affect other mlvg builds
3. Proper Dependencies: Uses mlvg's existing dependency management
4. Maintainable: Changes are contained and well-structured
5. Extensible: Easy to add more CLAP examples in the future

## Potential Challenges

1. madronalib Dependency: Requires coordination with upstream to include your changes
2. Build Complexity: Need to handle CLAP-specific dependencies that mlvg doesn't currently have
3. Testing: Need to ensure CI/CD works with new dependencies
4. Documentation: Need to update mlvg's README with CLAP build instructions

## Timeline Estimate

- Phase 1: 1-2 days (coordinate madronalib merge)
- Phase 2: 0.5 days (file migration)
- Phase 3: 1-2 days (CMake integration)
- Phase 4: 1 day (dependency integration)
- Phase 5: 0.5 days (resource handling)
- Testing & Polish: 1-2 days

Total: ~1-2 weeks depending on coordination with upstream

## Next Steps

1. Coordinate with Randy about merging the `header-cleanup` branch into upstream madronalib
2. Fork mlvg repository and create feature branch
3. Begin Phase 2 file migration
4. Implement CMake integration following mlvg patterns
5. Test build and functionality
6. Create pull request to upstream mlvg

## References

- [mlvg repository](https://github.com/madronalabs/mlvg)
- [madronalib repository](https://github.com/madronalabs/madronalib)
