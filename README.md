# CS249r docs

Contributor-facing design and implementation documentation for sub-projects of [`harvard-edge/cs249r_book`](https://github.com/harvard-edge/cs249r_book) (the "Machine Learning Systems" course repository).

## StaffML

Interview-prep question bank and practice app.

- [`staffml-design.md`](staffml-design.md): what StaffML is, why it exists, the full technology stack, and how every part of the architecture fits together.
- [`staffml-implementation.md`](staffml-implementation.md): the file map, real code from every subsystem, local setup steps, and common contribution workflows.

## TinyTorch

Hands-on course where students build an ML framework from scratch.

- [`tinytorch-design.md`](tinytorch-design.md): what TinyTorch is, why it exists, the full technology stack, and how every part of the architecture fits together.
- [`tinytorch-implementation.md`](tinytorch-implementation.md): the file map, real code from the module and CLI systems, local setup steps, and common contribution workflows.

## MLSys·im

First-principles analytical modeling framework for ML systems (roofline-style performance, cost, and sustainability modeling), also the physics engine behind the browser-based interactive labs.

- [`mlsysim-design.md`](mlsysim-design.md): what MLSys·im is, why it exists, the full technology stack, and how every part of the architecture (the five-layer analytical stack, the CLI, the labs integration) fits together.
- [`mlsysim-implementation.md`](mlsysim-implementation.md): the file map, real code from the engine and CLI, the WASM/labs build pipeline, local setup steps, and common contribution workflows.

Start with each project's design doc, then use its implementation doc as your map once you're ready to make a change. All six documents describe their project as of `dev` HEAD (commit `8fb87d81`) in the main repository.
