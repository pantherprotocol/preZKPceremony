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

Due to time limitations during the ceremony, attestations regarding
the contributions were collected retroactively shortly after the
ceremony, via the process in [`ATTESTATIONS.md`](ATTESTATIONS.md).

## Verifying the ceremony

You may verify the ceremony results yourself:

    npm i

    ./node_modules/.bin/snarkjs zkey verify \
        r1cs/PreZKP_10_prod.r1cs \
        ptau/powersOfTau28_hez_final_17.ptau \
        0012_final/PreZKP_10_prod.12.final.zkey

and check that the output looks good.

## Lessons learned

In general the ceremony was very smooth - 11 contributions in
approximately 24 hours, typically taking participants around
10-30 minutes to complete.

It was slightly marred by occasional problems with sharing files from
IPFS nodes running behind NAT firewalls.  It seems that `go-ipfs` /
`libp2p` still have [a way to
go](https://libp2p.io/implementations/#nat-traversal) before NAT
traversal works reliably.

It seems clear that the higher level of automation provided by
`zkey-manager` and `multisetups` reduced the manual work required, and
ensured a degree of consistency and reliability in the results.  Many
thanks to Wei Jie Koh for writing this software, making it freely
available, and being so responsive and supportive in answering
questions and helping address issues!

The version of `multisetups` used during the ceremony did not have
support for the `--name` option to the `snarkjs zkey contribute`
command, therefore the output from `snarkjs zkey verify` is missing
nice indicators of which contribution came from whom.  However this
information can easily be obtained by correlating the contribution
sequence number with the contribution directory in this repository
with the same name, and [a pull
request](https://github.com/appliedzkp/multisetups/pull/12) has been
submitted to fix this for future ceremonies.

Various other minor
[issues](https://github.com/appliedzkp/multisetups/issues) with
multisetups were discovered, and various [fixes and
enhancements](https://github.com/appliedzkp/multisetups/pulls?q=is%3Apr)
have been submitted, many of which are already merged at the time of
writing.

## Future work

Improvements to `multisetups` are
[ongoing](https://github.com/appliedzkp/multisetups/pulls?q=is%3Apr).

We may choose to conduct future ceremonies in an even more
user-friendly fashion, e.g. via the following web UI:

https://github.com/glamperd/setup-mpc-ui
