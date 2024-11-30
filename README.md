# libSSTV

[![GitHub License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

libSSTV is a **C library** designed for encoding **Slow Scan Television (SSTV)** images into audio signals. It is suitable for both **desktop** and **embedded applications**. The library currently supports encoding in various SSTV modes, with decoding functionality planned for version 1.0.0.

---

## Features

- **Multi-mode SSTV Support**: Encode images into various SSTV modes, such as Martin, Robot, Scottie, PD, and FAX.
- **Desktop and Embedded Ready**: Optimized for use in embedded systems and general desktop applications.
- **Extensive Tooling**: Includes Python scripts and CLI tools for LUT generation, image encoding, and signal visualization.
- **Cross-Platform Build**: Easily compile using CMake on Linux, macOS, and Windows.
- **Lightweight and Dependency-Free Core**: The library itself has no external dependencies for encoding.

---

## Sample Output

- [Sample Audio](test/test-image.wav) generated in `PD180` mode for [this test image](test/test-image.bmp).

---

## Supported Modes

The following SSTV modes are supported:

- **Martin**: `MARTIN_M1`, `MARTIN_M2`, `MARTIN_M3`, `MARTIN_M4`
- **PD**: `PD50`, `PD90`, `PD120`, `PD160`, `PD180`, `PD240`, `PD290`
- **Robot**:
  - Black & White: `ROBOT_BW8_B`, `ROBOT_BW8_G`, `ROBOT_BW8_R`, etc.
  - Color: `ROBOT_C12`, `ROBOT_C24`, `ROBOT_C36`, `ROBOT_C72`
- **Scottie**: `SCOTTIE_S1`, `SCOTTIE_S2`, `SCOTTIE_S3`, `SCOTTIE_S4`, `SCOTTIE_DX`
- **FAX**: `FAX480`

---

## Installation

### Prerequisites

- **CMake**: Version 3.12 or higher.
- **Compiler**: GCC, Clang, or MSVC for C/C++.
- **Optional**:
  - `ImageMagick++` and `libsndfile` for encoding tool builds.
  - Python 3.x for utility scripts.

### Building the Library

```bash
git clone https://github.com/Qyuzet/lib-sstv-master.git
cd lib-sstv-master
mkdir build && cd build
cmake ..
make
```

This generates:
- `lib/libsstv.so`: Dynamic library for linking.
- `include/libsstv.h`: Header file for integration.
- `bin/sstv-encode`: Encoding tool.

---

## Conversion Tool Usage

The `sstv-encode` tool can encode images into SSTV audio with the following parameters:

```bash
./sstv-encode <mode> <input_image> <output_audio> [sampling_rate]
```

Example:
```bash
./sstv-encode pd90 ../test/test-image.bmp test.wav 48000
```

---

## Using the Library

### Initialization

Initialize the library using:
```c
sstv_init(malloc, free);
```

If initialization is skipped, you can encode up to `DEFAULT_ENCODER_CONTEXT_COUNT` images simultaneously (default: 4). You can adjust this during the build:

```bash
cmake . -DDEFAULT_ENCODER_CONTEXT_COUNT=8
```

### Encoding

1. Create an image:
   ```c
   sstv_image_t image;
   sstv_create_image_from_mode(&image, SSTV_MODE_PD120);
   ```

2. Encode the image:
   ```c
   sstv_signal_t signal;
   sstv_pack_signal(&signal, SSTV_SAMPLE_INT16, SIGNAL_BUFFER_CAPACITY, signal_buffer);

   void *ctx;
   sstv_create_encoder(&ctx, image, SSTV_MODE_PD120, 48000);

   sstv_error_t rc;
   do {
       rc = sstv_encode(ctx, &signal);
   } while (rc != SSTV_ENCODE_END);

   sstv_delete_encoder(ctx);
   ```

3. Destroy resources:
   ```c
   sstv_delete_image(&image);
   ```

---

## Building Encoding Tools Only

To skip building the tools:
```bash
cmake . -DBUILD_TOOLS=OFF
make
```

---

## Dependencies

The library core has **no dependencies**.

Optional dependencies for the encoding tool:
- **Ubuntu**:
  ```bash
  apt install libmagick++-dev libsndfile1-dev
  ```
- **ArchLinux**:
  ```bash
  pacman -S imagemagick libsndfile
  ```

---

## Packaging Options

- [Arch User Repository (AUR)](https://aur.archlinux.org/packages/libsstv)
- [Gentoo Overlay](https://github.com/rimio/gentoo-overlay/tree/master/media-libs/libsstv)
- [MacPorts](https://ports.macports.org/port/libsstv/)

---

## License

libSSTV is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

---

## Acknowledgments

Special thanks to:
- Taywee/args library initialized by Taylor C. Richberger and Pavel Belikov (MIT license).

For inquiries, contact: **Riki Awal Syahputra (Qyuzet)** at [riqyuzet@gmail.com](mailto:riqyuzet@gmail.com).
