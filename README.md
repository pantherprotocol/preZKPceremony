# Phase 2 trusted setup for PreZKP launch circuits

Thanks a lot for taking part in this trusted setup ceremony, which we
believe is an exciting step towards the future of truly private,
decentralised protocol launches!

This document describes the steps required to contribute. If you have
any questions, please don't hesitate to ask in the Telegram group!

Please note that contributions are only required for phase 2 (which is
specific to Groth16 SNARKs). Phase 1 has already been covered thanks
to the [Perpetual Powers of Tau][ppot] project.

[ppot]: https://github.com/weijiekoh/perpetualpowersoftau

## Prerequisites

- Familiarity with running commands from the CLI

- A machine with the following components installed:

  - `docker`

Currently Linux has been tested, but MacOS and Windows may also work;
any feedback on this is gratefully received.

## Security recommendations

In order to ensure that the results of the ceremony are secure,
please bear in mind the following best practices:

https://github.com/celo-org/snark-setup/blob/master/RECOMMENDATIONS.md

However, any contribution is valuable and appreciated, even if it does
not follow all those recommendations.

## Instructions

### Preparation

First check out the repository if you haven't already:

    $ git clone -b prezkp https://github.com/aspiers/multisetups.git
    $ cd multisetups

Check out the following specific commit in order to ensure that
there is no tampering or other deviation:

    $ git checkout f298a820ff856d7f6b05cda4a55b9699d4844f2e

You may optionally wish to verify the integrity of the repository:

    $ git fsck --full --no-dangling

Build the Docker image:

    $ docker build -t multisetups .

Prepare a directory to store your new contribution:

    $ mkdir -p ceremony/new

Run the server as a container in the background:

    $ docker run --name prezkp -d --rm -v $PWD/ceremony:/ceremony -p 4001:4001 -p 5001:5001 -p 8080:8080 multisetups

Check it started up correctly:

    $ docker logs -f prezkp

At the end of the output, you should see something like:

    API server listening on /ip4/127.0.0.1/tcp/5001
    WebUI: http://127.0.0.1:5001/webui
    Gateway (readonly) server listening on /ip4/127.0.0.1/tcp/8080
    Daemon is ready

At this point you can hit `Control-C` to stop watching the logs.

Now let the coordinator know you are ready to make your contribution,
and they will provide you with an IPFS content id which you can store
in a shell variable, e.g.:

    $ cid=Qme5ABCQSgYT69x1WdZ8G6oGtaoU2SQXjUXkAwv6rYPCfi

(The above id is not valid; don't expect it to work.)

Now you can download the ceremony files you need:

    $ docker exec -it prezkp node build/index.js download -m $cid -d /ceremony/old
    Saving file(s) to /ceremony/old
     39.21 MiB / 39.21 MiB [==================================] 100.00% 0s

Note that the above `docker run` command will map the `ceremony`
subdirectory created above to `/ceremony` inside the container, so
when the command output refers to paths within the container such as
`/ceremony/old`, you can look for `./ceremony/old` from the host:

    $ ls ceremony/old

### Verifying the previous contribution (optional)

This step is optional, and will require the relevant file from
Perpetual Powers of Tau, which is a 144MB download:

    $ ptau=powersOfTau28_hez_final_17.ptau
    $ wget -c https://hermez.s3-eu-west-1.amazonaws.com/$ptau -P ceremony

You'll also need the `.r1cs` file for the circuit:

    $ docker exec -it prezkp node build/index.js download -m QmQffKEsC15gpdFsWjPgQ2DM2TLDX4P9h686JW6u8JZ1g9 -d /ceremony/r1cs

At this point you can safely ignore the error:

    Error: there are no .zkey files in /ceremony/r1cs

Now perform the verification:

    docker exec -it prezkp ./node_modules/snarkjs/build/cli.cjs zkey verify /ceremony/r1cs/PreZKP_10_prod.r1cs /ceremony/$ptau /ceremony/old/PreZKP_10_prod.0.zkey

### Making a contribution

Store some entropy in an `$entropy` environment variable:

    $ read -s entropy

Then type a long random string and hit `Enter`.

Calculate your contribution:

    $ docker exec -it prezkp node build/index.js contribute -d /ceremony/old -n /ceremony/new -e "$entropy"
    Contribution complete. Please run the 'upload' subcommand. Next, sign the transcript in /ceremony/new/transcript.1.txt and send it to the coordinator.

Despite what it says here, it is simpler if you put the signature alongside
the transcript; however due t e.g.:

    $ gpg --sign ceremony/new/transcript.1.txt

To upload the contribution:

    $ docker exec -it prezkp node build/index.js upload -d /ceremony/new
    Contribution uploaded. Please send this multihash to the coordinator and keep your IPFS node running and connected to the IPFS network.
    QmYDsgWYRuHNYBeJABGZ6Csdj256jvj4E4WYX2dJ6w6iCj

and then follow the instructions in the output.

## Future work

We may choose to conduct future ceremonies in a more user-friendly
fashion, e.g. via the following web UI:

https://github.com/glamperd/setup-mpc-ui

## Preparation for phase 2 contributions (reference only)

This section is here purely for reference, and documents the steps
which were already taken in order to prepare for the phase 2 ceremony
described above.

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
    gpg --sign preZKP/0000_initial/*.zkey
    gpg --sign preZKP/r1cs/*.r1cs

and ran the server as a container in the background:

    docker run --name prezkp -d --rm -v $PWD/preZKP:/ceremony -p 4001:4001 -p 5001:5001 -p 8080:8080 multisetups

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

    docker exec -it prezkp node build/index.js download -m $ccid -d /ceremony/$cid

Verify the contribution:

    ptau=powersOfTau28_hez_final_17.ptau
    docker exec -it prezkp ./node_modules/snarkjs/build/cli.cjs zkey verify /ceremony/r1cs/PreZKP_10_prod.r1cs /ceremony/ptau/$ptau /ceremony/$cid/PreZKP_10_prod.$cseq.zkey

Verify the signature (if any):

    gpg verify prezkp/$cid/transcript.1.txt.gpg

Then share the same content id with the next contributor.
