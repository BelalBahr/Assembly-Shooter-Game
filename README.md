# Serial Multiplayer Shooter (x86 Assembly)

Real-mode x86 assembly game featuring networked two-player balloon shooting over RS-232 serial (COM1). Implements custom protocol for real-time input synchronization, in-game chat system, and VGA text-mode rendering with direct hardware access.

## Key Technical Components

- **Custom serial communication protocol** - Direct port I/O to COM1 (3F8h-3FDh) with baud rate configuration and line control
- **Real-time input synchronization** - Coordinates keyboard inputs between two DOS machines over serial link
- **VGA text buffer manipulation** - Direct memory access to B800h segment for rendering
- **In-game chat system** - Dual chat boxes with scrolling, cursor management, and message filtering
- **Collision detection** - Position-based hit detection between arrows and descending balloons
- **Game state management** - Score tracking, respawn logic, and turn-based coordination

## Architecture

- **Platform:** Real-mode x86 DOS
- **Assembler:** MASM/TASM syntax
- **Memory model:** COMPACT/SMALL with 1024-byte stack
- **Code size:** ~7,900 lines across main game loop, chat module, and macro library

## Technical Challenges Solved

**Serial protocol timing and handshaking**
Implemented polling-based COM1 communication without hardware flow control. Line Status Register (LSR) polling ensures transmit buffer empty before send and data ready before receive.

**Input synchronization**
Coordinate timing between local keyboard polling and remote input reception to prevent game state divergence. Each machine processes both local and received inputs in the same game loop iteration.

**Frame rate management**
Balance polling overhead (keyboard + serial + rendering) with delay calls (int 15h) to maintain consistent game speed without blocking.

**Chat system integration**
In-game chat module shares serial channel with game inputs. Message framing and character filtering prevent control codes from corrupting game state.

**16-bit arithmetic constraints**
All position calculations, collision detection, and screen offset computation done in 16-bit registers with careful overflow handling.

## Constraints

- 16-bit real-mode (no protected mode features)
- Polling-based I/O (no interrupt service routines for async events)  
- Text mode only (no graphics mode pixel operations)
- Single-threaded execution model
- Direct hardware access (no OS abstractions)

## Game Mechanics

- Two players control characters via keyboard (up/down/shoot)
- Balloons descend down screen at fixed rate
- Players shoot arrows to pop balloons before they reach bottom
- Score tracking with hits and misses counters
- Real-time chat during gameplay
- Serial link multiplexes game inputs and chat messages

## Files

- `main.asm` - Main game loop, input handling, rendering (~7,559 lines)
- `chat.asm` - Chat module with screen region management (~380 lines)
- `flying.asm` - Core game mechanics and collision detection
- `MACROS.INC` - Shared macros for port I/O, screen operations, input handling

## Build & Run

Requires MASM/TASM assembler and DOS environment (DOSBox recommended). Two machines or DOSBox instances connected via null modem serial cable or virtual serial ports.

```
tasm main.asm
tlink main.obj
main.exe
```

---

Built for Microprocessors & Systems course, Cairo University (2020)
