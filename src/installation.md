# Installing Beethoven

This guide will walk you through installing and setting up the Beethoven framework for hardware acceleration.

## Prerequisites

Before installing Beethoven, ensure you have the following:

- A UNIX-based operating system
- CMake build system
- C++ development environment
- [AWS FPGA SDK](https://github.com/aws/aws-fpga) (for AWS F1 deployment only)

## System Requirements

Beethoven supports multiple platforms:
- AWS F1 instances (Xilinx Alveo U200)
- Xilinx Zynq FPGA family
- ASIC development through ChipKIT
- Simulation environments (Synopsys VCS and Verilator)

## Installation Steps

### 1. Basic Installation

```bash
cmake -B build -S .
cd build
sudo make install
```

> **Note**: This requires root permissions. For user-mode installation, see the next section.

### 2. User-Mode Installation

To install without root permissions:

```bash
cmake -B build -S . -DCMAKE_INSTALL_PREFIX=<your_directory>
cd build
make install
```

Remember to make your installation directory accessible to:
- The linker
- CMake

### 3. Using Beethoven in Your Project

#### With CMake (Recommended)

Add the following to your `CMakeLists.txt`:

```cmake
find_package(beethoven REQUIRED)
target_link_libraries(<your_target> PUBLIC APEX::beethoven)
```

#### Without CMake

If you're not using CMake, ensure:
1. Your compiler can find the include path
2. Add `-lbeethoven` flag during linking

## Platform-Specific Setup

### AWS F1 Setup

1. Install the AWS FPGA SDK:
   ```bash
   git clone https://github.com/aws/aws-fpga
   ```

2. The library will automatically detect AWS support during build
   - CMake will warn you if it can't find the SDK
   - Non-AWS features will still work without the SDK

### Simulation Environment

Beethoven supports two simulation backends:
- Synopsys VCS
- Verilator

No additional setup is required for simulation support.

## Verifying Installation

To verify your installation:

1. Create a new test project
2. Include the Beethoven headers:
   ```cpp
   #include <rocc.h>
   #include <fpga_handle.h>
   ```

3. Try a basic command:
   ```cpp
   fpga_handle_real_t handle;
   auto flush_cmd = rocc::flush_cmd();
   handle.send(flush_cmd);
   ```
