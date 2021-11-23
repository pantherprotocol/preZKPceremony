# Processes used by the ceremony coordinator

This file is not needed by contributors; it is here purely as
reference material:

1. to maximise transparency of the ceremony process
2. to help the coordinator remember what they did / need to do :-)

## Preparation for phase 2 contributions

This section documents the steps which were already taken in order to
prepare for the phase 2 ceremony described above.

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

    docker exec -it prezkp ./node_modules/snarkjs/build/cli.cjs zkey verify /ceremony/r1cs/PreZKP_10_prod.r1cs /ceremony/ptau/$ptau /ceremony/$cdir/PreZKP_10_prod.$cseq.zkey

or outside:

    ./node_modules/snarkjs/build/cli.cjs zkey verify preZKP/r1cs/PreZKP_10_prod.r1cs preZKP/ptau/$ptau preZKP/$cdir/PreZKP_10_prod.$cseq.zkey

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
