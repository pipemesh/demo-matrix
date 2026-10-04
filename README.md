# demo-matrix

A **dogfooding demo repo** for [Pipemesh](https://pipemesh.io) — fake
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

Each `test` lane is a `kind: build` that checks out only its own file
(`checkout: ["streams/${{ matrix.stream }}.txt"]`), and each `publish`
lane is a `kind: deploy` that checks out nothing and ships its lane's
entry when it changed.

Try it: edit exactly one `streams/<name>.txt` and watch only that
stream's lanes run; the others reuse their earlier runs. Corrupt the
`stream:` line in one file to see that lane go red while the others keep
shipping.
