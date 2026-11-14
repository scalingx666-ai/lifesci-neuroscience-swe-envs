# Issue

**Title:** MNT: Build niimath into the base image

## Description

First step toward replacing 3dvolreg, which takes about 7 min per BOLD or DWI run (#1513), with niimath's `-moco`: a clean-room BSD-2 implementation of the same method (Cox & Jesmanowicz, 1999). Nothing in MRIQC uses niimath yet. A follow-up points `Dockerfile` at the new base image and adds `-moco` behind an option, validated against 3dvolreg before it becomes a default.

- **What:** niimath v1.0.20261016 (BSD-2; its N4 code is Apache-2.0), compiled from source on amd64 and arm64 like svht_denoise, since upstream ships only an x86-64 Linux binary.
  - Built with OpenMP and the system zlib, with `-moco` and `-n4`. It needs only libraries the runtime image already has (libgomp, zlib).
  - arm64 gets `-fsigned-char`, as for the other tools, through the compiler: niimath's Makefile sets `CFLAGS` itself, so passing it would drop `-O3`.
- **Checks:** the image build fails if niimath misses a library, reports another version, or lacks `-moco`.
- **First numbers** (amd64, 40 volumes of the ds002785 BOLD run used in #1513): 3dvolreg 34 s; `niimath -moco -estimate` 0.9 s on one thread, 0.4 s on four, with identical parameters at both thread counts. The validation belongs to the follow-up, against MRIQC's 3dvolreg options (`-Fourier -twopass -zpad 4`).

Tests (the base-image workflow builds both architectures on this PR):

```
docker buildx build -f Dockerfile.base --target niimath --load -t mriqc-niimath-stage:test .
docker run --rm --entrypoint sh mriqc-niimath-stage:test -c 'ldd /usr/local/bin/niimath; niimath | head -1'
```

## Task

Modify the repository so that the issue described above is resolved. The repository is checked out at base commit `7a54210f476e`. Edit files in place; the verifier captures your changes via `git diff` and scores them against an oracle patch using SWE-RL-style diff-similarity reward.