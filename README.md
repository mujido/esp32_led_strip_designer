# ESP32 LED Matrix Driver

This directory contains an example ESP32 implementation of the LED Matrix Designer WebSocket API. The ESP32 serves the web interface and implements the WebSocket protocol to control LED strips.

## Features

- **WebSocket API Implementation**: Full implementation of the LED Matrix Designer protocol
- **Web Interface**: Serves the React web application from SPIFFS
- **LED Control**: Controls WS2812B LED strips via GPIO
- **WiFi Connectivity**: Creates access point and connects to existing networks
- **SPIFFS Storage**: Web files stored in ESP32's SPIFFS filesystem
- **CMake Integration**: Automatic web build integration with ESP-IDF

## WebSocket API Implementation

This ESP32 implementation supports all commands defined in the LED Matrix Designer protocol:

- **0x01**: Set LED Color
- **0x02**: Set Multiple LEDs
- **0x03**: Clear All LEDs
- **0x04**: Set Brightness
- **0x05**: Get LED Count
- **0x06**: LED Count Response

The WebSocket server runs on port 8080 and accepts connections at `ws://[esp32-ip]:8080/ws`.

## Quick Start

### Prerequisites

1. **ESP-IDF Development Environment**
   - Install ESP-IDF v5.0 or later
   - Source the ESP-IDF environment:
     ```bash
     source $HOME/esp/esp-idf/export.sh
     ```

2. **Node.js and npm** (for web build)
   ```bash
   node --version  # Should be v18+
   npm --version   # Should be v8+
   ```

3. **ESP32 Hardware**
   - ESP32 development board
   - USB cable for programming
   - LED strip (WS2812B or compatible)

### Build and Deploy

1. **Source ESP-IDF Environment**
   ```bash
   source $HOME/esp/esp-idf/export.sh
   ```

2. **Build Everything (CMake Integration)**
   ```bash
   idf.py build
   ```

3. **Deploy to ESP32**
   ```bash
   idf.py flash
   ```

4. **Monitor and Configure**
   ```bash
   idf.py monitor
   ```

## Build Options

### Option 1: Integrated CMake Build (Recommended)
```bash
source $HOME/esp/esp-idf/export.sh
idf.py build          # Automatically includes web build
idf.py flash          # Flash firmware and web files
```

### Option 2: Manual Build Process
```bash
source $HOME/esp/esp-idf/export.sh
cd web && npm install && npm run build  # Build web only
idf.py build                            # Build firmware only
idf.py flash                            # Flash everything
```

## Development

### Web Development
The web application is in the `web/` submodule. For web development:
```bash
cd web
npm install
npm run dev          # Development server
npm run build        # Production build
```

### Firmware Development
```bash
source $HOME/esp/esp-idf/export.sh
idf.py build         # Build firmware (includes web)
idf.py app-flash     # Flash just the application code
idf.py monitor       # Monitor output
```

### Configuration
```bash
source $HOME/esp/esp-idf/export.sh
idf.py menuconfig    # Configure WiFi, LED count, GPIO pin
```

## Configuration

### WiFi Setup
The ESP32 will create an access point on first boot. Connect to it and configure your WiFi credentials through the web interface.

### LED Configuration
Configure LED settings through `idf.py menuconfig`:
- **Number of LEDs**: Set the total number of LEDs in your strip
- **GPIO Pin**: Set the GPIO pin connected to the LED data line

### Partition Table
The project uses a custom partition table (`partitions.csv`):
- **Factory**: 1MB for firmware
- **SPIFFS**: ~3MB for web files
- **NVS**: 24KB for configuration

## Development Workflow

### Quick Development Cycle
```bash
# Build and flash just the application (faster)
idf.py build
idf.py app-flash
idf.py monitor
```

### Full Deployment
```bash
# Build and flash everything
idf.py build
idf.py flash
idf.py monitor
```

## Project Structure

```
esp32_led_strip_designer/
├── main/                  # ESP32 firmware
│   ├── main.cpp          # Main firmware code
│   ├── websocket_server.cpp  # WebSocket API implementation
│   ├── led_controller.cpp    # LED strip control
│   ├── web/              # Web files for SPIFFS (generated)
│   └── CMakeLists.txt    # Firmware build config
├── web/                  # React web application (submodule)
│   ├── src/              # Source code
│   ├── dist/             # Built web files (generated)
│   └── package.json      # Web dependencies
├── partitions.csv        # ESP32 partition table
└── CMakeLists.txt        # Project configuration
```

## CMake Integration

The project includes seamless CMake integration that automatically:
- Detects Node.js and npm availability
- Builds the web application during firmware build
- Copies web files to the ESP32 project
- Handles dependencies between web and firmware builds

### CMake Targets
- `web-build`: Builds and copies web files
- `web-clean`: Cleans web build artifacts
- `idf.py build`: Automatically includes web build

## Troubleshooting

### Build Issues
- **ESP-IDF not found**: Make sure to source the ESP-IDF environment first
- **Node.js not found**: Install Node.js and npm
- **CMake build fails**: Check that ESP-IDF is properly sourced
- **Web submodule not found**: Ensure the web submodule is properly initialized with `git submodule update --init`

### Flash Issues
- **Device not found**: Check USB connection and drivers
- **Flash failed**: Try erasing flash first: `idf.py erase-flash`
- **SPIFFS mount failed**: Check partition table configuration

### Runtime Issues
- **WiFi not connecting**: Check credentials and signal strength
- **Web interface not loading**: Verify SPIFFS flash was successful
- **LEDs not responding**: Check GPIO pin configuration and wiring

### Web Build Issues
- **Dependencies missing**: Run `cd web && npm install`
- **Build errors**: Check for TypeScript/ESLint errors in the web submodule

## Performance

- **Web Bundle**: ~700KB (compressed to ~200KB)
- **SPIFFS Usage**: ~700KB of 3MB available
- **Memory**: Optimized for ESP32's limited RAM
- **Network**: Efficient static file serving over WiFi
- **Build Time**: ~3 seconds for web, ~30 seconds for firmware
- **WebSocket Latency**: <10ms for LED updates

## File Locations

After building, the following files will be generated:
- **Firmware**: `build/led-pattern-lab-firmware.bin`
- **Web Files**: `main/web/` (copied from `web/dist/`)
- **Partition Table**: `build/partition-table.bin`
- **Bootloader**: `build/bootloader/bootloader.bin`

## Security Considerations

- WiFi credentials are stored in NVS (non-volatile storage)
- Web interface is served over HTTP (not HTTPS)
- No authentication required for web interface
- Consider adding security measures for production use

## Technologies Used

### ESP32 Implementation
- **ESP-IDF** framework
- **ESP32 HTTP Server** for web interface
- **WebSocket Server** for real-time communication
- **SPIFFS** for file storage
- **WiFi** connectivity
- **FastLED** library (planned)

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test with `idf.py build`
5. Submit a pull request

## Credits

This project was developed with the assistance of:
- **[lovable.dev](https://lovable.dev)** - AI-powered development platform that helped create the initial React web interface
- **[Cursor](https://cursor.sh)** - AI-powered IDE that hosted the development environment and AI assistant
- **Claude Sonnet 4** - AI assistant that helped with project organization, documentation, and WebSocket API design

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Support

For issues and questions:
1. Check the troubleshooting section above
2. Review ESP-IDF documentation
3. Open an issue on GitHub
