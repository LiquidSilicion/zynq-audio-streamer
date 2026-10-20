# Zynq Audio Streamer

A high-bandwidth, low-latency telemetry tool for streaming 16-bit audio data from the ARM core of a Xilinx Zynq SoC (like the ZedBoard) to a Host PC via UART. 

Designed to bypass the bandwidth limitations of standard serial communication, this tool enables real-time plotting and high-fidelity `.wav` recording of FPGA/ARM processed audio directly on a PC.

## 🌟 Features
- **High-Speed UART:** Configured for up to 3,000,000 baud (supports 48kHz stereo audio streaming).
- **Robust Binary Protocol:** Uses a sync-byte packet structure to prevent data desynchronization.
- **Real-Time Host Processing:** Python script unpacks binary data, saves to standard `.wav` files, and plots the audio in real-time using `matplotlib`.
- **Hardware Agnostic:** The C firmware is modular. Simply replace the `sine_lut.h` test data with your own AXI DMA / ADC / PDM microphone buffers.

## 📡 The UART Protocol
To ensure data integrity at high speeds, audio is sent in structured packets. 

| Byte 0 | Byte 1 | Byte 2 | Bytes 3 to N |
| :--- | :--- | :--- | :--- |
| **Sync** (`0xAA`) | **Channel ID** (`0x01` to `0xFF`) | **Payload Length** (Bytes) | **Raw 16-bit PCM Data** (Little-Endian) |

*Note: The current implementation limits payload length to 255 bytes (127 samples) to keep the header compact. For larger blocks, expand the Length field to 2 bytes.*

## 🛠️ Hardware & Software Requirements
- **Hardware:** Zynq-7000 SoC board (e.g., Digilent ZedBoard, Zybo, Pynq-Z2).
- **Software (Embedded):** Xilinx Vivado & Vitis (Standalone/Bare-Metal BSP).
- **Software (Host):** Python 3.8+

## Repository File structure

```text
zynq-audio-streamer/
│
├── README.md                 # The main documentation (provided below)
├── .gitignore                # Keeps Xilinx junk out of your repo (provided below)
│
├── firmware/                 # Vitis C/C++ code for the Zynq ARM core
│   ├── src/
│   │   ├── main.c            # Entry point and main loop
│   │   ├── uart_config.c     # UART initialization (baud rate, etc.)
│   │   ├── packet_tx.c       # The binary packet builder and sender
│   │   └── sine_lut.h        # The 64-point sine wave lookup table
│   └── README.md             # Brief instructions on how to import into Vitis
│
└── host/                     # Python code for the Host PC
    ├── audio_receiver.py     # Listens to UART, unpacks, saves WAV, plots
    └── requirements.txt      # Python dependencies (pyserial, matplotlib)
```

## 🚀 Quick Start Guide

### 1. Host PC (Python)
1. Navigate to the `host/` directory.
2. Install dependencies:
   ```bash
   pip install -r requirements.txt
