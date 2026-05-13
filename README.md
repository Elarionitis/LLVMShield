# LLVMShield

A minimal LLVM-based code obfuscation toolchain built for learning how compilers transform code under the hood. Takes C/C++ source and produces obfuscated native binaries via a custom LLVM pass plugin.

> Built to understand LLVM internals, not to do anything clever with the output.

---

## What it does

Implements four basic transformations as an LLVM pass:

- **XOR string encryption** — encrypts string constants with a configurable key
- **Bogus function insertion** — injects dummy functions with meaningless arithmetic
- **Symbol renaming** — appends `_obf` suffix to private global symbols
- **Dead code insertion** — adds unreachable conditional branches

These are intentionally simple and reversible. The point is the pipeline, not the strength of the obfuscation.

---

## Pipeline

```
C/C++ source → LLVM bitcode → obfuscation pass → native binary + JSON report
```

---

## Requirements

- LLVM/Clang 10–14 (12 recommended)
- CMake 3.10+
- Python 3.8+
- GCC/Clang with C++14 support

```bash
# Ubuntu/Debian
sudo apt-get install llvm-12-dev clang-12 cmake python3 build-essential
```

---

## Build

```bash
mkdir build && cd build
cmake ..
make
```

---

## Usage

```bash
# Basic
./warp_aai.py example.c --pass-lib build/lib/libSimpleObfPass.so

# With options
./warp_aai.py example.c \
    --pass-lib build/lib/libSimpleObfPass.so \
    --xor-key 42 \
    --bogus-count 3 \
    --cycles 2 \
    --out output_binary \
    --verbose
```

| Flag | Default | Description |
|------|---------|-------------|
| `--pass-lib` | required | Path to compiled pass plugin |
| `--xor-key` | 170 | XOR key for string encryption |
| `--bogus-count` | 2 | Number of fake functions to insert |
| `--cycles` | 1 | Obfuscation passes to run |
| `--target` | linux | `linux` or `windows` |
| `--verbose` | off | Print pass output |
| `--keep-temp` | off | Keep intermediate `.bc` files |

---

## Output

Each run produces an obfuscated binary and a `warp_report_<timestamp>.json` with stats on what was transformed — strings encrypted, functions inserted, cycles completed.

---

## Limitations

This is an MVP for learning purposes:

- XOR encryption is trivially reversible
- Bogus functions are obvious to any analyst
- No control flow flattening, packing, or anti-debug techniques

Not suitable for production use.

---

## License

MIT
