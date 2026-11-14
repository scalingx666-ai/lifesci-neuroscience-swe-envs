# Issue

**Title:** perf(mesh): deduplicate_vertices without np.unique(axis=0)

## Description

Hi Will!

I recently started working more with Wei's Aedes dataset (`wclee_aedes_brain` CAVE datastack). That dataset has pretty big single-resolution neuron meshes and I noticed that they take much longer to load with `CloudVolume` than in Neuroglancer. Profiling found the bottleneck in deduplication of vertices:

When `CloudVolume` downloads a mesh from a sharded graphene source, it joins the fragments with `Mesh.deduplicate_vertices`. That method calls `np.unique(..., axis=0)` twice. To group whole rows, numpy sorts them as records, which is slow (and holds the GIL the whole time).

Claude suggested a much faster version of the deduplication:

1. Sort the vertices by position with `np.lexsort` and find the groups of identical positions.
2. Merge each pair that occurs exactly twice where both vertices are chunk aligned (the same rule as before).
3. Keep only the vertices that faces use, renumbered with a true/false mask and a running count instead of a second `np.unique`.

The output is the same as before: same vertices, same vertex order, same faces, no changes for the callers with one exception: vertices with `NaN` coordinates. Because `NaN` never equals `NaN`, the old code gave such a vertex a separate copy for every face corner that used it. The new code keeps it as one vertex and never merges it.

Here is what this buys:

Measured on a large neuron (2.8M vertices, 5.6M faces) and a typical one (0.8M faces) from a sharded graphene dataset. "Longest freeze" is the longest stretch during which another Python thread could not run.

| | Before | After |
|---|---|---|
| `deduplicate_vertices`, large neuron | ~16 s | ~0.5 s |
| Longest freeze during that step | ~13 s | ~10 ms |
| Whole `vol.mesh.get()`, large neuron | ~21 s | ~6 s |
| Whole `vol.mesh.get()`, typical neuron | ~4.5 s | ~2.7 s |
| Longest freeze during a whole `get()` | ~13 s | ~0.2 s |
| Peak extra memory, large neuron | ~2.4 GB | ~0.47 GB |

What remains of a `get()` is mostly downloading. The remaining ~0.2 s freeze comes from draco decoding.

Hope this looks useful to you?

## Task

Modify the repository so that the issue described above is resolved. The repository is checked out at base commit `d8d698f2f765`. Edit files in place; the verifier captures your changes via `git diff` and scores them against an oracle patch using SWE-RL-style diff-similarity reward.