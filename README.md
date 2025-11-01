```markdown
# Growtopia Private Server (GTPS) — C++ Server Implementation

> **LyxTech Studio** presents a modern, modular, and extensible C++ implementation of a **Growtopia Private Server (GTPS)** with full gRPC integration, Redis caching, MariaDB persistence, and an event-driven architecture.

---

## 📌 Overview

This project is a **high-performance, cross-platform Growtopia server** written in **C++17**, designed for private server operators who demand:

- **Full protocol compliance** with Growtopia client
- **Modular architecture** (commands, events, listeners, permissions)
- **gRPC microservice support** for world/player/server communication
- **Redis-backed session & cache layer**
- **MariaDB** for persistent data (players, items, guilds, etc.)
- **Event-driven system** with cancellable/pre/post hooks
- **Extensible command system** with permission nodes
- **Cross-platform** (Windows & Linux)

---

## 🧰 Requirements

### 📦 System Dependencies

| Platform | Dependencies |
|---------|--------------|
| **Windows** | Visual Studio 2022+, CMake ≥ 3.15, vcpkg |
| **Linux** | GCC ≥ 9 or Clang ≥ 10, CMake ≥ 3.15, pkg-config |

### 🔌 External Libraries (via vcpkg or system package manager)

| Library | Purpose |
|--------|--------|
| **Boost** | Utilities, ASIO (future), filesystem |
| **Protobuf** | Protocol buffer serialization |
| **gRPC** | Inter-service communication (world ↔ server) |
| **ENet** | UDP networking (Growtopia uses ENet) |
| **MariaDB Connector/C++** | Database access |
| **hiredis + redis++** | Redis client for sessions/cache |
| **yaml-cpp** | Configuration file parsing |
| **fmt** | Modern string formatting |
| **httplib** | Built-in HTTP server (for webhooks, status) |
| **argon2** | Secure password hashing |
| **D++ (Linux only)** | Optional Discord bot integration |

> ✅ All libraries are linked **statically** on Windows (via vcpkg). On Linux, use system packages or build from source.

---

## 🛠️ Build Instructions

### 🪟 Windows (with vcpkg)

1. **Install vcpkg**:
   ```powershell
   git clone https://github.com/Microsoft/vcpkg.git
   .\vcpkg\bootstrap-vcpkg.bat
   ```

2. **Install dependencies**:
   ```powershell
   .\vcpkg\vcpkg install enet fmt yaml-cpp httplib protobuf grpc hiredis argon2 mariadb-connector-cpp redis-plus-plus --triplet x64-windows
   ```

3. **Configure & Build**:
   ```powershell
   mkdir build && cd build
   cmake .. -DCMAKE_TOOLCHAIN_FILE="C:/path/to/vcpkg/scripts/buildsystems/vcpkg.cmake" -A x64
   cmake --build . --config Release
   ```

### 🐧 Linux (Ubuntu/Debian)

1. **Install system dependencies**:
   ```bash
   sudo apt update
   sudo apt install build-essential cmake git libmariadb-dev libhiredis-dev libprotobuf-dev protobuf-compiler libgrpc++-dev libfmt-dev libyaml-cpp-dev libargon2-dev libenet-dev
   ```

2. **Build redis++** (if not available):
   ```bash
   git clone https://github.com/sewenew/redis-plus-plus.git
   cd redis-plus-plus
   mkdir build && cd build
   cmake .. -DCMAKE_BUILD_TYPE=Release
   make -j && sudo make install
   ```

3. **Build GTPS**:
   ```bash
   mkdir build && cd build
   cmake .. -DCMAKE_BUILD_TYPE=Release
   make -j
   ```

> 💡 Output binary: `./gtps` (Linux) or `Release/gtps.exe` (Windows)

---

## 🗂️ Project Structure

```
.
├── CMakeLists.txt          # Main build config
├── Main.cc                 # Entry point
├── Config/                 # Runtime configs (YAML)
├── gRPC/
│   ├── Protos/             # .proto definitions
│   ├── Protos/Generated/   # Auto-generated gRPC/Protobuf code
│   ├── NetServer/          # gRPC server for world logic
│   └── NetStub/            # gRPC client stubs
├── Misc/                   # Utilities (Logger, Random, Hash, etc.)
├── Packet/                 # Growtopia packet structures
├── Player/                 # Player class, inventory, clothing
├── World/                  # World simulation, tiles, objects
├── Server/                 # Main ENet server loop, HTTP status server
├── DB/                     # MariaDB abstraction
├── Redis/                  # Redis session/cache manager
├── EventSystem/            # Event bus with listeners
├── CommandSystem/          # Command executor & handlers
├── Permissions/            # Role-based permission system
├── Item/                   # Item definitions & manager
├── Guild/                  # Guild system
└── Sequencers/             # Animation sequencers (e.g., tile effects)
```

---

## ⚙️ Configuration

Edit `Config/config.yaml`:

```yaml
server:
  name: "LyxTech GTPS"
  ip: "0.0.0.0"
  port: 17091
  max_players: 500

database:
  host: "127.0.0.1"
  port: 3306
  username: "root"
  password: "@reugrowtopia01"
  database: "gtps"

redis:
  host: "127.0.0.1"
  port: 6379

grpc:
  world_service_port: 50051
```

> 🔐 **Never commit passwords!** Use environment variables in production.

---

## 🧪 Running the Server

```bash
./gtps
```

- ENet server starts on `port: 17091`
- HTTP status server on `http://localhost:8080`
- gRPC service on `localhost:50051`

---

## 🧩 Extending the Server

### ➕ Add a New Command

1. Create `CommandSystem/Commands/MyCommand.hh/cc`
2. Register in `CommandHandler.cc`
3. Use `PERMISSION_NODE("mycmd")` for access control

### ➕ Add a New Event

1. Define event in `EventSystem/Events/MyEvent.hh`
2. Create listener in `EventSystem/Listeners/MyListener.hh/cc`
3. Emit via `EventManager::getInstance().call(event);`

### ➕ Add gRPC Method

1. Add RPC to `gRPC/Protos/Arcane.proto`
2. Re-run `protobuf_generate()` in CMake
3. Implement in `gRPC/NetServer/NetServer.cc`

---

## 📦 Protocol Notes

- Uses **ENet** over UDP (port 17091)
- Packets are **Growtopia-compatible** (NetPacket, Variant, GameUpdatePacket)
- All game logic is **server-authoritative**
- **No client modification required**

---

## 📜 License

This project is **private-source** and intended for **educational and private server use only**.  
Growtopia is a registered trademark of **Ubisoft**. This project is **not affiliated** with Ubisoft or Robinson Technologies.

> ⚠️ **Do not use for commercial public servers without legal consultation.**

---

## 🤝 Contact

- **Developer**: Willy (Theo Willy)
- **Studio**: [LyxTech Studio](https://lyxtech.xyz)
- **Instagram**: [@lyytechid](https://instagram.com/lyytechid), [@theok_Willy](https://instagram.com/theok_Willy)
- **Email**: lyytechid@gmail.com

---

## 🚀 Happy Coding & Server Hosting!
```
