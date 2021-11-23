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

Currently Linux and MacOS have been tested, but Windows may also work;
any feedback on this is gratefully received.

## Security recommendations

In order to ensure that the results of the ceremony are secure,
please bear in mind the following best practices:

https://github.com/celo-org/snark-setup/blob/master/RECOMMENDATIONS.md

However, any contribution is valuable and appreciated, even if it does
not follow all those recommendations.

## Preparation (do ASAP)

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

### Network configuration

Finally, to reduce the risk of network connectivity issues:

  - Please ensure that your machine's firewall (if any) allows
    incoming traffic on TCP port 4001.

  - If you have a NAT router, please consider enabling UPnP
    and/or setting up port forwarding for incoming traffic on
    TCP port 4001 to the machine running Docker.

## Get in the queue!

Now let the coordinator know you are ready to make your contribution,
and then wait for them to get back to you (bearing in mind time zone
differences which might mean they are currently asleep!!)

**Please note: the ceremony is sequential, therefore each person has
to wait their turn, starting from where the previous contributor left
off.  Your contribution will be invalid if you base it on a content ID
which hasn't been explicitly given to you (and only you) by the
coordinator.**

## Steps when it is your turn

Once the coordinator has provided you with an IPFS content id, first
store it in a shell variable, e.g.:

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

    $ docker exec -it prezkp node build/index.js download -m QmUXxQLbfKDvNXbFKRo8YNXGPKh1bZ3yuaZkrtuRtw2sGj -d /ceremony/r1cs

At this point you can safely ignore the error:

    Error: there are no .zkey files in /ceremony/r1cs

Now perform the verification (note you will have to alter the sequence
number in the last argument):

    docker exec -it prezkp ./node_modules/snarkjs/build/cli.cjs zkey verify /ceremony/r1cs/PreZKP_10_prod.r1cs /ceremony/$ptau /ceremony/old/PreZKP_10_prod.0.zkey

Check the verification passed; last line of the output should be:

    [INFO]  snarkJS: ZKey Ok!

### Generate your contribution

Store some entropy in an `$entropy` environment variable:

    $ read -s entropy

Then type a long random string and hit `Enter`.

Calculate your contribution:

    $ docker exec -it prezkp node build/index.js contribute -d /ceremony/old -n /ceremony/new -e "$entropy"
    Contribution complete. Please run the 'upload' subcommand. Next, sign the transcript in /ceremony/new/transcript.1.txt and send it to the coordinator.

### Sign your contribution

Despite what it says here, it is simpler if you put the signature alongside
the transcript, e.g.:

    $ gpg --detach-sign --armor ceremony/new/transcript.*.txt

Even better, use [Keybase](https://keybase.io/) to cryptographically
prove the link between your GPG private key and your other accounts
such as GitHub, Twitter etc.

You can even sign directly with Keybase, e.g.

    $ new=ceremony/new/transcript.3.txt  # adjust number as appropriate
    $ keybase sign -d -i $new -o $new.asc

or via PGP:

    $ keybase pgp sign -d -i $new -o $new.asc

### Share your contribution

To upload the contribution:

    $ docker exec -it prezkp node build/index.js upload -d /ceremony/new
    Contribution uploaded. Please send this multihash to the coordinator and keep your IPFS node running and connected to the IPFS network.
    QmYDsgWYRuHNYBeJABGZ6Csdj256jvj4E4WYX2dJ6w6iCj

and then follow the instructions in the output, and also let the
coordinator know how they can obtain any public key you have used for
signing the transcript - e.g. whether it's available via a PGP
keyserver, or via Keybase, and if so what your Keybase username is.

### IPFS networking issues, and a contigency plan

If you are running an IPFS node behind a NAT gateway, it can be tricky
to avoid networking issues - hence [the hints in the preparatory steps
above](#network-configuration).

If for some reason those suggestions don't work, e.g. the coordinator
can't download your contribution via IPFS, there are of course several
more conventional file-sharing fallback options to choose from:

- Submit a PR to this repository which adds a new directory following
  the existing scheme.

- Upload the contribution to some centralised cloud storage (e.g.
  Google Drive or Dropbox) and provide a link to the coordinator.

- Use [Magic-Wormhole](https://magic-wormhole.readthedocs.io/en/latest/)
  (it's awesome!)

- Anything else in this vein.

## Completing the ceremony

Following standard practice for trusted setup ceremonies, in order to
calculate the final zkey file of the PreZKP ceremony, we will apply a
random beacon to the circuit, via:

    snarkjs zkey beacon <circuit_prev.zkey> <circuit_final.zkey> <beaconHash(Hex)> <numIterationsExp>

In order to prove that we cannot know this randomness in advance, we
make a commitment to taking it from [the drand
chain](https://drand.love) at a fixed point in the future,
specifically round 1410000 - and furthermore, we provide a guarantee
that we cannot tamper with that commitment in an undetectable way, as
follows:

We have taken the `keccak256` hash of the following text (including
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

We have then made a commitment to this hash by placing it within
[transaction `0x489212672a43220f8229752242d7bb1ec13349a37770955d9b82c737c3c69c56` on the Ethereum
mainnet](https://etherscan.io/tx/0x489212672a43220f8229752242d7bb1ec13349a37770955d9b82c737c3c69c56)
(click on `Click to see More` to expand extra data about the transaction,
and you will see the above commitment hash in the `Input Data` box).

After the random of round 1410000 has arrived, it can be verified via
[the `drand_verify` tool](https://github.com/hermeznetwork/drand_verify).

## Future work

We may choose to conduct future ceremonies in a more user-friendly
fashion, e.g. via the following web UI:

https://github.com/glamperd/setup-mpc-ui

## Processes used by the ceremony coordinator

Please see [`COORDINATOR.md`](COORDINATOR.md).
