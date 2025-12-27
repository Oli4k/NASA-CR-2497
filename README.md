# NASA CR-2497 Flight Dynamics Simulation

A complete C++ implementation of the kinematic model from **NASA Contractor Report 2497: "A Standard Kinematic Model for Flight Simulation at NASA–Ames"** (1975). This library provides a robust, modular flight dynamics model (FDM) suitable for aircraft simulation, flight training, and research applications.

## Overview

This project implements the NASA CR-2497 kinematic model as a modern C++17 library with:

- **Complete FDM Implementation**: All major equations and subsystems from the original report
- **Modular Architecture**: Clean separation of concerns (frames, dynamics, atmosphere, etc.)
- **Realistic Flight Behavior**: Natural instabilities, stall modeling, sideslip effects
- **Extensible Design**: Callback-based architecture for custom aircraft models
- **3D Visualization**: Python-based OpenGL viewer for real-time simulation visualization
- **Comprehensive Documentation**: API reference, implementation notes, and verification results

## Features

### Core FDM Capabilities

- **Frame Transformations**: Earth, Local (NED), and Body frame conversions with proper Euler angle handling
- **Velocity Calculations**: Correct handling of V_E (inertial) vs V_EE (Earth-relative) velocities
- **Atmospheric Model**: Standard atmosphere properties (density, pressure, temperature)
- **Aerodynamics**: Lift, drag, side force, and moment calculations with stall and sideslip effects
- **Propulsion**: Engine force and moment modeling via callbacks
- **Landing Gear**: Ground contact and friction modeling
- **Turbulence**: Dryden turbulence model for realistic atmospheric disturbances
- **Integration**: Adams-Bashforth (velocities/angular rates) and Trapezoidal (Euler angles) methods

### Advanced Features

- **Control Surfaces**: Built-in support for ailerons, elevators, rudders, flaps, spoilers, and throttle
- **Trimming**: Automatic trim calculation for steady-state flight conditions
- **Earth Rotation**: Proper correction for Earth's rotation in position updates
- **Angle of Attack/Sideslip Rates**: Real-time calculation of α̇ and β̇ for advanced control systems
- **Wind Modeling**: Wind velocity and trend support

## Building

### Requirements

- CMake 3.15 or higher
- C++17 compatible compiler (GCC 7+, Clang 5+, MSVC 2017+)
- Python 3.7+ (for visualization, optional)

### Build Instructions

```bash
mkdir build
cd build
cmake ..
make
```

### Build Options

- `BUILD_EXAMPLES`: Build example programs (default: ON)
- `BUILD_TESTS`: Build test programs (default: OFF)

## Examples

The library includes several example programs demonstrating different use cases:

- **`example_paper_plane`**: Realistic unpowered paper plane glider with natural instabilities
- **`example_simulation`**: Basic simulation setup and usage
- **`example_propulsion`**: Engine and propulsion system integration
- **`example_with_controls`**: Control surface usage and aircraft control

### Running Examples

```bash
cd build
./example_paper_plane
./example_simulation
./example_propulsion
./example_with_controls
```

## Visualization

A Python-based 3D viewer is available for real-time visualization:

```bash
cd visualization
python viewer.py --paper-plane    # View paper plane simulation
python viewer.py                  # View simple glide demo
python viewer.py --debug          # Enable debug output
```

The viewer supports:
- Real-time 3D aircraft visualization
- Camera controls (mouse drag, arrow keys, zoom)
- HUD display (ground speed, altitude)
- Interactive controls (for controllable aircraft)
- Automatic reset on landing

## Documentation

Comprehensive documentation is available in the `documentation/` directory:

- **[API Reference](documentation/API_REFERENCE.md)**: Complete API documentation
- **[C++ Implementation Guide](documentation/README_CPP.md)**: Usage and architecture overview
- **[Timing and Ordering](documentation/TIMING_AND_ORDERING.md)**: Simulation pipeline execution order
- **[Aircraft Definition Architecture](documentation/AIRCRAFT_DEFINITION_ARCHITECTURE.md)**: How to define aircraft properties
- **[FDM Missing Elements](documentation/FDM_MISSING_ELEMENTS.md)**: Implementation status tracking
- **[Verification Notes](documentation/FDM_VERIFICATION.md)**: Validation and testing results

## Quick Start

### Basic Usage

```cpp
#include "nasa_cr_2497/nasa_cr_2497.hpp"

// Define simulation parameters
Simulation::Parameters params;
params.mass = 0.01;  // slugs
params.dt = 0.01;   // seconds
// ... set inertia, etc.

// Create simulation
Simulation sim(params);

// Set initial state
Simulation::State state;
state.h = 50.0;  // altitude (feet)
state.V_N = 20.0;  // velocity components
// ... set other state variables

sim.set_state(state);

// Define aerodynamic callback
auto aero_callback = [](const Simulation::State& state, 
                        const Simulation::ControlSurfaces& controls,
                        Vector3& F_A, Vector3& M_A) {
    // Calculate forces and moments based on state
    // ...
};

sim.set_aerodynamic_callback(aero_callback);

// Run simulation loop
while (sim.get_state().h > 0.0) {
    sim.step();
    // Process state, log data, etc.
}
```

See the examples directory for complete working code.

## Architecture

The library is organized into distinct modules:

- **`Frames`**: Coordinate frame transformations
- **`Velocity`**: Velocity vector calculations
- **`Atmosphere`**: Atmospheric property calculations
- **`Dynamics`**: Force and moment calculations
- **`Propulsion`**: Engine modeling support
- **`LandingGear`**: Ground contact modeling
- **`Turbulence`**: Atmospheric turbulence (Dryden model)
- **`Integration`**: Numerical integration methods
- **`Trimming`**: Trim calculation utilities
- **`Simulation`**: Main simulation class and state management

## Implementation Status

The implementation follows NASA CR-2497 closely and includes:

✅ **Fully Implemented:**
- Earth rotation velocity correction (Equation 3.8)
- V_EE vs V_E distinction (Equation 3.19)
- Landing gear integration
- Euler angle integration (Trapezoidal method, Equation 3.28)
- Body-axis acceleration calculations
- Angle of attack/sideslip rate calculations (Equations 3.31, 3.32)
- Wind trend support (Equation 3.9)

See [FDM_MISSING_ELEMENTS.md](documentation/FDM_MISSING_ELEMENTS.md) for detailed status.

## Design Philosophy

This implementation follows these principles:

1. **FDM as Pure Physics Engine**: The FDM computes forces and integrates state; aircraft properties are defined externally
2. **No Hardcoded Optimizations**: Flight behavior is determined by aircraft definition (aerodynamics, mass, geometry), not FDM tuning
3. **Realistic Behavior**: Natural instabilities, stall, and sideslip effects are properly modeled
4. **Extensibility**: Users provide aerodynamic models via callbacks, allowing any aircraft configuration

## Citation

If you use this implementation in research, please cite the original NASA report:

```
McFarland, R. E. (1975). A Standard Kinematic Model for Flight Simulation at NASA–Ames 
(No. NASA CR-2497). National Aeronautics and Space Administration.
```

## Related Resources

- **[NASA Technical Reports Server (NTRS)](https://ntrs.nasa.gov/citations/19750006408)**: Original PDF source
- **[Robert Heffley's Flight Simulation Documentation](https://robertheffley.com/docs/Sim_modeling/)**: Original PDF source (OCR)

## License

**Original Work:** NASA CR-2497 A Standard Kinematic Model for Flight Simulation at NASA–Ames is a work of the U.S. Government (NASA). As such, it is in the public domain and not subject to copyright protection in the United States.

## Acknowledgments
- **Original Author:** Richard E. McFarland, Computer Sciences Corporation
- **Original Publisher:** NASA Ames Research Center
- **PDF Source:** Robert Heffley (for hosting the scanned OCR document)

**This Implementation:** This C++ implementation is dedicated to the public domain and released under **CC0 1.0 Universal (CC0 1.0) Public Domain Dedication**.

---

*This project is not affiliated with or endorsed by NASA. It is an independent implementation of the NASA CR-2497 kinematic model.*
