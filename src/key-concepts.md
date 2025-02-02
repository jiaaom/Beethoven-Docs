# Beethoven Key Concepts

## Core Architecture

### System Hierarchy

Beethoven uses a hierarchical structure for organizing accelerator designs:

1. **Core** 
   - The basic functional unit in Beethoven
   - Implements specific acceleration functionality
   - Can range from simple operations to complex pipelines
   - Has its own memory and command interfaces

2. **System**
   - A group of identical Cores
   - Manages multiple instances of the same Core
   - Provides unified communication and memory interfaces
   - Can be scaled by adjusting the number of Cores

3. **Multi-System Design**
   - Multiple Systems can coexist on the same accelerator
   - Each System can implement different functions
   - Supports heterogeneous acceleration needs

## Communication Infrastructure

### Command System (RoCC Interface)

Beethoven uses the Rocket Custom Co-processor (RoCC) instruction format for communication:

1. **RoCC Commands** (`rocc_cmd`)
   - **Function Types**:
     - `flush_cmd()`: Waits for all in-flight instructions to complete
     - `start_cmd()`: Initiates kernel execution
     - `addr_cmd()`: Sets up memory addressing
   
   - **Key Fields**:
     - `system_id` (4 bits): Identifies target System
     - `core_id` (8 bits): Specifies Core within System
     - `rd` (5 bits): Destination register or special purpose register

2. **RoCC Responses** (`rocc_response`)
   - Contains:
     - 56-bit return value
     - Core ID and System ID
     - Destination register information

### Memory Management

1. **Memory Primitives**
   - **Reader**: Streams data from memory
     - Supports prefetching
     - Configurable data width
     - Manages parallel read operations

   - **Writer**: Streams data to memory
     - Handles memory write operations
     - Configurable buffer sizes

   - **Scratchpad**: On-chip memory
     - Fast local storage
     - Initialization capabilities
     - Platform-specific optimizations

2. **Memory Access Patterns**
   - Primarily supports contiguous access
   - Starting address provided per channel
   - 512-bit access width
   - Hardware-managed address incrementation

## Hardware Integration

### Multi-Die Support

1. **SLR Awareness**
   - Automatic core placement across Super Logic Regions (SLRs)
   - Optimized routing between SLRs
   - Resource-aware memory mapping

2. **Network Generation**
   - Automatically generates on-chip networks
   - Handles memory, host, and intra-accelerator communication
   - SLR-aware buffering and timing optimization

### Platform Support

1. **FPGA Platforms**
   - AWS F1 (Xilinx Alveo U200)
   - Xilinx Zynq FPGA family
   - Automatic resource management

2. **ASIC Development**
   - Support through ChipKIT
   - ASAP7 and Synopsys Academic PDK compatibility
   - Memory compiler utilities

3. **Simulation**
   - Synopsys VCS support
   - Verilator integration
   - Cycle-accurate DRAM simulation (DRAMSim3)

## Software Interface

### Runtime Management

1. **FPGA Handle Types**
   - `fpga_handle_sim_t`: For simulation environments
   - `fpga_handle_real_t`: For physical FPGA deployment

2. **Core Operations**
   - `send()`: Transmit commands
   - `get_response()`: Receive responses
   - `flush()`: Clear in-flight operations

### Memory Monitoring

Special-purpose registers for AXI-4 bus statistics:
- Address read/write counts
- Data read/write counts
- Write response counts
- Read/write wait cycles

## Programming Model

### Design Flow

1. **Core Development**
   - Define accelerator functionality
   - Configure memory interfaces
   - Implement command handling

2. **System Configuration**
   - Set number of cores
   - Configure memory interfaces
   - Define communication patterns

3. **Integration**
   - Automatic C++ binding generation
   - Platform-specific optimization
   - Resource management

### Best Practices

1. **Memory Access**
   - Use Readers/Writers for streaming data
   - Consider Scratchpads for frequent access
   - Align with platform memory architecture

2. **Core Design**
   - Keep cores modular and reusable
   - Consider scalability for multi-core deployment
   - Use appropriate memory primitives

3. **System Integration**
   - Start with single-core verification
   - Scale gradually to multi-core
   - Let Beethoven handle cross-core communication

## Performance Considerations

1. **Memory Optimization**
   - Configure appropriate burst lengths
   - Use multiple AXI IDs for better parallelism
   - Consider SLR placement for memory interfaces

2. **Resource Utilization**
   - Automatic resource balancing across SLRs
   - Dynamic memory cell allocation (BRAM/URAM)
   - Platform-specific optimizations

3. **Communication Efficiency**
   - Optimized network generation
   - Automatic buffering for cross-SLR communication
   - Efficient command routing
