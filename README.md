# demo-matrix

A **dogfooding demo repo** for [PipeMesh](https://pipemesh.dev) — fake
project, real pipeline. `demo-*` repos exercise engine features
end-to-end on the production instance without touching the
`pipemesh/pipemesh` mainline.

This one exercises the **matrix design** (2026-08-11), both modes:

- **`as: jobs`** — `test` and `publish` expand into per-stream sibling
  jobs (`test[stream=alpha]`, …) with lane-to-lane `needs:` edges via
  `${{ matrix.stream }}` interpolation. Break one stream and only that
  lane stops promoting.
- **`as: workflow`** — `suite` runs its checks inside one generated
  child workflow: a single node, a single verdict.

The "project" is three text files under `streams/`; each lane verifies
its own file, ships it as an artifact, and the publish lane reads it
back — real artifact inheritance along the lane edge.

Try it: edit exactly one `streams/<name>.txt` and watch only that
stream's lanes run (changed-path rules would narrow further; here every
lane runs per revision, which is the point — the demo shows lane
independence, not skip logic). Corrupt the `stream:` line in one file
to see that lane go red while the others keep shipping.
