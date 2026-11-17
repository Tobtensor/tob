# `tob`: Git-like version control for tensors

<div align="center">

<img src="https://raw.githubusercontent.com/tobtensor/.github/main/profile/tobtensor.svg" width="112" height="112" alt="tobtensor logo">

**Tensor-native version control for AI models.**

[![Repository: tobcli](https://img.shields.io/badge/repo-tobcli-24292f?logo=github)](https://github.com/tobtensor/tobcli)
[![License: Apache-2.0](https://img.shields.io/badge/license-Apache--2.0-blue)](https://github.com/tobtensor/tobcli/btob/main/LICENSE)
[![Language: Rust](https://img.shields.io/badge/rust-1.85%2B-dea584?logo=rust&logoColor=white)](https://github.com/tobtensor/tobcli/btob/main/Cargo.toml)
[![Format: SafeTensors](https://img.shields.io/badge/format-SafeTensors-0ea5e9)](https://github.com/huggingface/safetensors)
[![Status: pre-1.0](https://img.shields.io/badge/status-pre--1.0%20·%20format%20freeze%20pending-f59e0b)](https://github.com/tobtensor/tobcli)

</div>

---

## What is tobtensor?

tobtensor (`tob`) is version control built for tensors. Git manages source code;
tobtensor manages model weights, tensor metadata and tensor transformations; and
**Tensormorph** visualizes, inspects and edits them.

```text
Git          manages source code
tobtensor    manages tensors, model weights, tensor metadata, transformations
Tensormorph  visualizes, edits and understands them
```

A Git user should be able to manage the history of a multi-terabyte model with Git's
conceptual simplicity, without the system pretending tensors are just large files.
The verbs are the same. The atom is different: the unit of change is the **tensor** and
its **8 MiB chunks**, not the file.

## How it differs from Git and Git LFS

| | Git / Git LFS | tobtensor |
|---|---|---|
| Unit of change | a file | a tensor, split into 8 MiB chunks |
| Identity | hash of file bytes | BLAKE3 over canonical CBOR, in a content-addressed Tensor Merkle DAG |
| Diff | bytes or "binary files differ" | numerical: `max\|Δ\|`, cosine, tolerance-aware |
| Cost of a commit | the whole changed file | only the chunks that changed |
| Round-trip | byte copy of the file | every tensor's canonical bytes reproduced bit-for-bit |
| Compute | — | CPU is always complete; GPUs (CUDA, wgpu, Metal) only accelerate |

## A first session

```bash
tob init
tob import models/model.safetensors
tob status
tob commit -m "import checkpoint"
tob log
tob export HEAD --format safetensors -o out/
tob fsck
```

`out/model.safetensors` reproduces every tensor bit-for-bit. Beyond this path, `tob`
has branches and merge, numerical `diff`, tensor deltas, remotes (`file://`, S3 layout
and its own `tob://` protocol), SSH signing, policy checks, Git pinning and hooks, and a
local API server.

## Principles

- **Bit-exact or it is a bug.** Export reproduces every tensor's canonical bytes.
- **Stable identity.** Anything that decides an object ID is specified, independently
  written and pinned by golden vectors.
- **Cost scales with change.** Fine-tune one layer, store one layer's chunks.
- **CPU complete, GPU optional.** Every operation runs on CPU; accelerators only make it faster.
- **One container format first.** SafeTensors (single, sharded and indexed) is the tracked
  format today; other adapters arrive by design decision, not by accident.

## Status

Working, pre-1.0. `tob` round-trips real SafeTensors checkpoints bit-exact, and the
on-disk format freeze (v1) is the next milestone. Expect formats to change until then.

## Repositories

| Repository | What it is |
|---|---|
| [tobcli](https://github.com/tobtensor/tobcli) | the `tob` command-line tool and its Rust crates |

<div align="center">
<sub>Part of the Tensormorph ecosystem · Apache-2.0</sub>
</div>
