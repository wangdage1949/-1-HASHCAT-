# Bitcoin Wallet MKey Tool Suite

A comprehensive suite of tools for Bitcoin wallet analysis, master key recovery, and cryptographic operations with GPU acceleration support.

## Tools Included

### 1. change.exe - MKey Injector ULTRA-FAST
The main tool for wallet master key operations with brute force capabilities and GPU acceleration.

### 2. BitcoinNonceExtractor.exe
Tool for extracting cryptographic nonces from Bitcoin wallet files for analysis and recovery operations.

## Quick Start

### For MKey Injection:
```bash
# Normal mode (avoid similarity)
change.exe wallet.dat mypassword123

# Brute force with 75% target similarity
change.exe wallet.dat mypassword123 --bruteforce --similarity 0.75

# Ultra-fast mode with GPU
change.exe wallet.dat --password 123 --ultrafast --full --gpu --batch-size 131072
```

### For Nonce Extraction:
```bash
# Extract only mkey nonces
BitcoinNonceExtractor.exe wallet.dat

# Complete scan (mkey and ckey)
BitcoinNonceExtractor.exe wallet.dat -all

# With custom output name
BitcoinNonceExtractor.exe wallet.dat nonce_report.txt
```

## change.exe - MKey Injector

### Overview
Advanced tool for Bitcoin wallet master key operations with multi-CPU/GPU support, brute force capabilities, and extracted nonce support.

### Features
- **Multi-device support**: CPU multi-threading and NVIDIA CUDA GPU acceleration
- **Brute force mode**: Search for encryption keys with target similarity
- **Extracted nonce support**: Use pre-extracted nonces for faster recovery
- **Ultra-fast modes**: Optimized algorithms for maximum performance
- **Safe operation**: Validation checks to prevent wallet corruption

### Installation

#### Dependencies
**Ubuntu/Debian:**
```bash
sudo apt-get install libdb++-dev libssl-dev
```

**Fedora/RHEL:**
```bash
sudo dnf install libdb-cxx-devel openssl-devel
```

**Windows:**
- Berkeley DB 4.8+ libraries
- OpenSSL 1.1.1+
- CUDA Toolkit 11.0+ (for GPU support)

#### Compilation
**Linux:**
```bash
make clean
make
```

**Windows (Visual Studio):**
Open `change.vcxproj` in Visual Studio 2017+ and build.

### Usage

#### Basic Syntax
```bash
change.exe [wallet.dat] [options]
```

#### Common Options

##### Mode Options:
```bash
--password <pass>        # Wallet password (default: 123)
--bruteforce, -b         # Enable brute force mode
--similarity <val>       # Set target similarity (0.0-1.0)
--rounds <N>             # Set fixed number of rounds (1 for max speed)
--ultrafast              # Ultra-fast mode (rounds=1, max optimizations)
--fast                   # Fast mode (rounds=1)
```

##### CPU/GPU Options:
```bash
--threads <N>            # Number of CPU threads (default: 4)
--gpu, --cuda            # Enable GPU acceleration
--gpu-device <id>        # GPU device ID (default: 0)
--batch-size <size>      # GPU batch size (default: 65536)
```

##### Nonce Options:
```bash
--nonce-file <file>      # File with extracted nonces (16 bytes each)
--nonce-index <index>    # Index of nonce to use (default: 0)
--test-all-nonces        # Test all extracted nonces
--allow-different-salt-size # Allow using nonces with different size
```

##### Output Options:
```bash
--full                   # Show full ciphertext
--max-attempts <N>       # Maximum attempts (0 = unlimited)
--help, -h               # Show help message
```

### Examples

#### Basic Operations:
```bash
# Normal operation
change.exe wallet.dat --password mypassword

# Fast brute force
change.exe wallet.dat --fast --bruteforce

# Ultra-fast with all optimizations
change.exe wallet.dat --ultrafast --bruteforce --gpu
```

#### Advanced GPU Operations:
```bash
# GPU brute force with high similarity target
change.exe wallet.dat --password 123 --bruteforce --similarity 0.9 --gpu

# Large batch GPU processing
change.exe wallet.dat --gpu --batch-size 131072 --gpu-device 0

# With extracted nonces
change.exe wallet.dat --nonce-file extracted_nonces.bin --bruteforce --gpu
```

#### Complete Nonce Testing:
```bash
# Test all extracted nonces
change.exe wallet.dat --nonce-file all_extracted_nonces.bin --test-all-nonces --bruteforce

# Use specific nonce index
change.exe wallet.dat --nonce-file nonces.bin --nonce-index 5 --bruteforce
```
#no gpu
change.exe wallet.dat --password 123 --bruteforce --similarity 1.0 --max-attempts 0 --rounds 1 --threads 12 --full --nonce-file all_extracted_nonces.bin --test-all-nonces --allow-different-salt-size

### Performance Tips

1. **For maximum speed:** Use `--ultrafast` which sets rounds=1 and enables GPU
2. **For GPU optimization:** Adjust `--batch-size` based on your GPU memory (131072 is good for 8GB+ VRAM)
3. **For CPU optimization:** Set `--threads` to your CPU core count
4. **For ciphertext similarity:** Target realistic values based on ciphertext length
   - 48-byte ciphertext: Max 100% similarity
   - 64-byte ciphertext: Max 75% similarity (48/64)
   - Adjust `--similarity` accordingly

### Important Notes

⚠️ **WARNING**: Always backup your wallet before using this tool!

- The tool performs cryptographic operations that can modify your wallet
- Use `--allow-different-salt-size` with caution
- GPU mode may not achieve 100% similarity if ciphertext is longer than 48 bytes
- The tool includes validation checks to prevent wallet corruption

## BitcoinNonceExtractor.exe

### Overview
Tool for extracting cryptographic nonces from Bitcoin wallet files for analysis and recovery operations.

### Usage
```bash
# Basic extraction
BitcoinNonceExtractor.exe wallet.dat

# Complete scan with all keys
BitcoinNonceExtractor.exe wallet.dat -all

# Generate new master key
BitcoinNonceExtractor.exe wallet.dat -genmkey

# Custom output file
BitcoinNonceExtractor.exe wallet.dat -output extracted_nonces.bin

# Verbose mode with hex dump
BitcoinNonceExtractor.exe wallet.dat --verbose --hexdump
```

### Features
- Extract mkey and ckey nonces from wallet.dat
- Generate new cryptographic material
- Save extracted nonces to binary files for use with change.exe
- Detailed reporting and analysis

## Building from Source

### Prerequisites
- C++17 compatible compiler
- Berkeley DB libraries
- OpenSSL development libraries
- CUDA Toolkit (optional, for GPU support)

### Linux Build
-
### Windows Build
-

## Troubleshooting

### Common Issues

1. **"Unknown option" error**: Check option spelling and ensure you're using supported options
2. **GPU not working**: Verify CUDA installation and GPU compatibility
3. **Wallet not found**: Ensure correct file path and permissions
4. **Similarity target impossible**: Reduce target or use CPU mode for longer ciphertexts

### Error Messages

- **"GPU target impossible"**: The requested similarity cannot be achieved with GPU (ciphertext too long)
- **"Wallet.dat not updated"**: Validation failed, wallet was not modified to prevent corruption
- **"No CUDA devices found"**: GPU acceleration unavailable, falling back to CPU

## Security Considerations

⚠️ **IMPORTANT SECURITY NOTES**:

1. **Always work on wallet copies** - Never operate on the original wallet file
2. **Use strong passwords** - Weak passwords reduce security significantly
3. **Keep nonce files secure** - Extracted nonces contain sensitive cryptographic material
4. **Verify operations** - Always verify wallet integrity after operations
5. **Backup regularly** - Maintain multiple backups of important wallets

## Performance Benchmarks

| Mode | Attempts/sec | Hardware | Notes |
|------|-------------|----------|-------|
| CPU Sequential | ~1,000 | i7-10700K | Single thread |
| CPU Parallel (12 threads) | ~12,000 | i7-10700K | 12 threads |
| GPU (RTX 3080) | ~500,000 | RTX 3080 | Batch size 131072 |
| GPU (RTX 4090) | ~1,200,000 | RTX 4090 | Batch size 262144 |

## License

This tool is provided for educational and research purposes. Use responsibly and in compliance with applicable laws.

## Support

For issues and questions:
1. Check the troubleshooting section
2. Ensure all dependencies are installed
3. Verify wallet file integrity
4. Use `--help` for command reference

---

**Disclaimer**: This tool is for educational purposes only. Always ensure you have proper authorization before performing operations on any wallet files. The developers are not responsible for any data loss or misuse of this tool.