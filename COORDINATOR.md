# Processes used by the ceremony coordinator

This file is not needed by contributors; it is here purely as
reference material:

1. to maximise transparency of the ceremony process
2. to help the coordinator remember what they did / need to do :-)

## Preparation for the ceremony

This section documents the steps which were already taken in order to
prepare for the ceremony described above.

Firstly we checked out the repository and compiled the circuits:

    git clone https://github.com/pantherprotocol/preZKP.git
    yarn
    cd circuits
    yarn compile

Then we fetched the appropriate phase 1 powers of Tau:

    yarn ptau

Next we generated the initial `.zkey` files:

    yarn zkey

We checked out https://github.com/aspiers/multisetups at commit
f298a820ff856d7f6b05cda4a55b9699d4844f2e, then created the Docker
image:

    cd multisetups
    docker build -t multisetups .

We set up directories with the `.zkey`, `.r1cs`, and `.ptau` files:

    mkdir -p preZKP/{r1cs,ptau,0000_initial}
    cp ../preZKP/circuits/zkeys/*.zkey preZKP/0000_initial
    cp ../preZKP/circuits/zkeys/*.r1cs preZKP/r1cs
    cp ../preZKP/circuits/zkeys/*.ptau preZKP/ptau
    gpg --detach-sign --armor preZKP/0000_initial/*.zkey
    gpg --detach-sign --armor preZKP/r1cs/*.r1cs

and ran the server as a container in the background:

    docker run --name prezkp -d --rm -v $PWD/preZKP:/ceremony -p 4001:4001 -p 5001:5001 -p 8080:8080 multisetups

(Note that this bind-mount's source is deliberately different to the
one for contributors, in order to avoid mistakes when the same person
is testing the ceremony as both a coordinator and contributor.)

We uploaded the `.r1cs` file to allow participants to optionally
perform verification:

    docker exec -it prezkp node build/index.js upload -d /ceremony/r1cs

which resulted in the content id `QmUXxQLbfKDvNXbFKRo8YNXGPKh1bZ3yuaZkrtuRtw2sGj`.

Finally we created a new ceremony:

    docker exec -it prezkp node build/index.js init -d /ceremony/0000_initial

which resulted in the following message:

    Ceremony initialised. Please give this multihash to the first participant and keep your IPFS node running and connected to the IPFS network.
    QmTS7CtiJnyYJqyVVAxgbRKu1zMCsjXwTLPiXshhPr2ELU

## Handling contributions

This section is for reference by the ceremony coordinator, and explains
how to handle incoming contributions.

Store the IPFS content id of the incoming contribution in a variable:

    ccid=QmbnBGgH8PTgSoNKRepD6SeDm9HjPUepHSGAfu2YLTDLjE

Set variables storing the sequence number and contributor's nick / handle, e.g.

    cseq=1 cnick=aspiers
    cdir=$(printf %04d_%s $cseq $cnick); echo $cdir

Download the contribution into a fresh directory:

    docker exec -it prezkp node build/index.js download -m $ccid -d /ceremony/$cdir

Set `$ptau`:

    ptau=powersOfTau28_hez_final_17.ptau

Verify the contribution via Docker:

    docker exec -it prezkp ./node_modules/.bin/snarkjs zkey verify /ceremony/r1cs/PreZKP_10_prod.r1cs /ceremony/ptau/$ptau /ceremony/$cdir/PreZKP_10_prod.$cseq.zkey

or outside:

    ./node_modules/.bin/snarkjs zkey verify preZKP/r1cs/PreZKP_10_prod.r1cs preZKP/ptau/$ptau preZKP/$cdir/PreZKP_10_prod.$cseq.zkey

Check the verification passed; last line of the output should be:

    [INFO]  snarkJS: ZKey Ok!

Verify the signature (if any), e.g.:

    keybase pgp pull $cnick
    gpg --verify preZKP/$cdir/transcript.*.txt.asc

or via keybase:

    keybase verify -d preZKP/$cdir/transcript.*.txt.asc -i preZKP/$cdir/transcript.*.txt

Upload the directory so it's available for the next contributor:

    docker exec -it prezkp node build/index.js upload -d /ceremony/$cdir

Then share the same content id with the next contributor.

## Completing the ceremony

Following standard practice for trusted setup ceremonies, in order to
calculate the final zkey file of the PreZKP ceremony, we applied a
random beacon to the circuit as the final contribution.  The
randomness was obtained by taking round 1410000 of [the drand
chain](https://drand.love), and can be verified via [the
`drand_verify` tool](https://github.com/hermeznetwork/drand_verify):

    $ node drand_verify/main.js 1410000
    1410000 c96e0284f0a578bf344d53176b9bbcc20eb564d991dd6d9f86b3dc1eecaf5489

We then applied this using [`snarkjs zkey
beacon`](https://github.com/iden3/snarkjs#20-apply-a-random-beacon):

    ./node_modules/.bin/snarkjs zkey beacon \
        0011_mikerah/PreZKP_10_prod.11.zkey \
        0012_final/PreZKP_10_prod.12.final.zkey \
        c96e0284f0a578bf344d53176b9bbcc20eb564d991dd6d9f86b3dc1eecaf5489 \
        10 \
        -n="Beacon from round 1410000 of drand.love"

and then verified it:

    ./node_modules/.bin/snarkjs zkey verify \
         r1cs/PreZKP_10_prod.r1cs \
         ptau/powersOfTau28_hez_final_17.ptau \
         0012_final/PreZKP_10_prod.12.final.zkey

Finally we exported the verification key:

    ./node_modules/.bin/snarkjs zkey export verificationkey \
         0012_final/PreZKP_10_prod.12.final.zkey \
         0012_final/verification_key.json

## Proof of inability to tamper with final result

In order to prove that we could not know this randomness in advance,
or select it in a way which could influence the result, we made a
commitment to taking it from the drand chain at a fixed point in the
future, specifically round 1410000 - and furthermore, we provided a
cryptographic guarantee that we could tamper with that commitment in
an undetectable way, as follows:

We took the `keccak256` hash of the following text (including
`\n` newline characters at both the beginning and end):

```
Before calculating the final zkey file of the [PreZKP ceremony](https://github.com/pantherprotocol/preZKPceremony/blob/69c43ba76293c9b6b91beba2b4a38f6dab96405f/README.md), we will apply a random beacon to the circuit.
For this, we will apply the result of the round 1410000 of [drand](https://drand.love/).
Here is the info of the drand chain that will be used:
{
  "public_key": "868f005eb8e6e4ca0a47c8a77ceaa5309a47978a7c71bc5cce96366b5d7a569937c529eeda66c7293784  a9402801af31",
  "period": 30,
  "genesis_time": 1595431050,
  "hash": "8990e7a9aaed2ffed73dbd7092123d6f289930540d7651336225dc172e51b2ce"
}
```

This results in a hash of:

    0x45c72731fc31fc83fe28ae9a48f39bd2e35ab2b14276054a1bfe909ca5b487f9

(You can verify it yourself by copying and pasting that block of text
with surrounding `\n` characters into [this
site](https://www.keccak-256.cloxy.net/).)

We then made a commitment to this hash by placing it within
[transaction `0x489212672a43220f8229752242d7bb1ec13349a37770955d9b82c737c3c69c56` on the Ethereum
mainnet](https://etherscan.io/tx/0x489212672a43220f8229752242d7bb1ec13349a37770955d9b82c737c3c69c56)
(click on `Click to see More` to expand extra data about the transaction,
and you will see the above commitment hash in the `Input Data` box).
