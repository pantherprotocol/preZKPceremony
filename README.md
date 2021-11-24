# Phase 2 trusted setup for PreZKP launch circuits

This repository captures the results of the trusted setup ceremony for
Panther Protocol's launch circuits, based on the Groth16 proof system.

The circuits were compiled and the initial zkey generated using
[zkey-manager](https://github.com/appliedzkp/zkey-manager).

Each individual's contribution is in a subdirectory, numbered to
reflect the order of contributions.

Contributions were collected using a
[fork](https://github.com/aspiers/multisetups/tree/prezkp) of
[`multisetups`](https://github.com/appliedzkp/multisetups), the
changes in which have since been [mostly or all merged
upstream](https://github.com/appliedzkp/multisetups/pulls?q=is%3Apr+author%3Aaspiers+).

For the instructions which contributors followed, see
[`CONTRIBUTING.md`](CONTRIBUTING.md).

For the processes followed by the coordinator, see
[`COORDINATOR.md`](COORDINATOR.md).

## Future work

Improvements to `multisetups` are
[ongoing](https://github.com/appliedzkp/multisetups/pulls?q=is%3Apr).

We may choose to conduct future ceremonies in an even more
user-friendly fashion, e.g. via the following web UI:

https://github.com/glamperd/setup-mpc-ui
