# Bitcoin Address Generator

Derivation of the full set of Bitcoin address formats from a single secret, in C++17, with a Qt6
interface and a console test harness.

## What it does

From one input the program derives and displays:

- WIF private keys, compressed and uncompressed
- P2PKH addresses, compressed and uncompressed
- a P2SH address, built by wrapping the P2PKH output script
- a Bech32 (P2WPKH) address, derived from the public key

The Bech32 path is implemented directly rather than taken from a library: the public key is
hashed with `RIPEMD-160(SHA-256(pubkey))`, the result is converted from 8-bit to 5-bit groups,
the witness version prefix is prepended, and the checksum and character encoding are applied.
The 8-to-5 bit conversion is a C++ translation of the reference Python implementation (see
`src/btc_func.cpp`).

## Security notice — read before using this on anything real

**This program derives the private key by hashing a passphrase. That construction is known as a
brainwallet and it is unsafe.** A private key produced this way is only as unpredictable as the
phrase behind it, and phrases that a human invents are searched exhaustively by anyone who cares
to: attackers precompute keys from dictionaries, quotations, song lyrics and leaked password
lists, and sweep the resulting addresses automatically. Funds sent to a brainwallet address have
been drained within seconds of the first deposit.

This repository is an exercise in the derivation algorithms, not a wallet. For anything holding
value, use keys generated from a cryptographically secure random source and a standard seed
scheme (BIP-39 / BIP-32) in an audited wallet.

## Build

Dependencies:
[libbitcoin-system](https://github.com/libbitcoin/libbitcoin-system),
[Boost](https://www.boost.org/),
[secp256k1](https://github.com/libbitcoin/secp256k1),
[libbech32](https://github.com/dcdpr/libbech32),
[Qt 6](https://doc.qt.io/).

Boost and secp256k1 can be installed with the libbitcoin-system installation script — see
[libbitcoin installation](https://github.com/libbitcoin/libbitcoin-system#installation).

```bash
cmake --preset default
cmake --build --preset default
```

Built and tested with GCC on Linux; the Windows build with Visual C++ is partial.

## Structure

| Path | Purpose |
|---|---|
| `src/btc_key.*` | private key handling and WIF encoding |
| `src/btc_public.*` | public key, extends `ec_public` with the Bech32 derivation |
| `src/btc_address.*` | extends `payment_address` with P2SH construction |
| `src/btc_func.*` | 8-to-5 bit conversion and helpers |
| `src/mainwindow.*` | Qt6 interface |
| `src/test.cpp` | console harness that checks the derivations against known vectors |
| `task/` | algorithm notes and verification logs |

## Origin

The code was written as a self-contained exercise in the Bitcoin address formats and in building
against libbitcoin. `task/` keeps the notes and verification logs produced along the way.

## Acknowledgements

The 8-to-5 bit conversion follows Pieter Wuille's reference implementation.

## Licence

MIT.
