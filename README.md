# openglcontext-reference-images

The images OpenGLContext's regression tests compare renders against, kept in a
repository of their own because they are large and are re-blessed on their own
schedule rather than when the code changes.

The PNGs are stored in [Git LFS](https://git-lfs.com/), so the history holds
pointers and the image bytes live in the LFS store. `git lfs install` has to
have been run once on the machine; without it a checkout holds the pointer files
and every comparison reads a text file where it wants a PNG.

Checked out as `tests/reference_images/` inside an OpenGLContext clone:

```bash
git lfs install    # once per machine
git clone --recurse-submodules https://github.com/mcfletch/openglcontext
# or, in an existing clone:
git submodule update --init tests/reference_images
```

Without it the regression tests still run: a view with no reference to compare
against is reported as having none, rather than failing.

New images are held in LFS by `.gitattributes`, which tracks `*.png` at any
depth; adding one needs nothing beyond `git add`.

## `gltf_baseline/`

The verified renders `oglc-gltf-regression` diffs new renders against — one
`<scene>.png` and one `<scene>.json` per glTF sample scene. The JSON records
what produced the PNG: the source URL, the camera framing, the background and
environment, the frame count and the load time.

```bash
oglc-gltf-regression            # compare
oglc-gltf-regression --bless    # re-establish, after reviewing the change
```

`OPENGLCONTEXT_GLTF_BASELINE` points somewhere else, for a working copy of the
baselines kept outside the checkout.

## `x3d_baseline/`

The verified renders `oglc-x3d-regression` diffs new renders of Web3D's X3D
examples against, one `<archive>.<path>.png` and `.json` per document.
`OPENGLCONTEXT_X3D_BASELINE` points somewhere else.

## `x3d_xite/`

The same documents drawn by X_ITE, the JavaScript X3D browser Web3D publishes
its examples with, in a headless Chromium with software WebGL. The regression
report shows each beside our render; they are never compared for a pass or a
failure. X_ITE's StraightenHorizon option is off, so each document is seen
from its Viewpoint's orientation as written. Each `.json` records the X_ITE
and Chromium versions and the errors the page reported.
`scripts/x3d_xite_references.py` in OpenGLContext draws them:

```bash
uv run scripts/x3d_xite_references.py            # documents with no render yet
uv run scripts/x3d_xite_references.py --refresh  # all of them again
```

## Top-level `*.png`

The script suite's reference frames, one per test script, compared by
`tests/test_all_scripts.py`. Comparison is a percentage tolerance rather than a
byte match, because rasterization, anti-aliasing and gamma differ between GPUs;
pin a software rasterizer (`LIBGL_ALWAYS_SOFTWARE=1`) where byte-stable
references are wanted.

Everything here is a render: this project's, or X_ITE's in `x3d_xite/`. Third-party imagery — a
photograph, a scan, a downloaded material — belongs with the model it was
gathered for, outside version control, rather than in a public repository that
would redistribute it.
