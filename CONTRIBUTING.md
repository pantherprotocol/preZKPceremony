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

- Familiarity with running commands from the CLI.

- Asynchronous preparation time of ~5-45 minutes, followed by an
  uninterrupted time slot of ~5-10 minutes, depending on how quick /
  familiar you are with this kind of technology.

  If you are familiar with git, Docker, basic TCP/IP networking
  etc. the process should be quick and easy; even more so if you are
  already familiar with zero knowledge trusted ceremonies.

- During the uninterrupted time slot you will need to be responsive in
  the ceremony communication channel, in order not to risk delays for
  other participants.

- A machine with the following components installed:

  - `git`
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

    $ git checkout 1980ec96f0233e8d448b00da7a1ce7881a5f2580

although be aware that there may be legitimate reasons for the
`prezkp` branch changing; if so these should be announced via the
ceremony communication channel.

You may optionally wish to verify the integrity of the repository:

    $ git fsck --full --no-dangling

If you're using a new Mac with M1 CPU or similar CPU architecture,
you'll have to apply [this workaround](https://github.com/appliedzkp/multisetups/issues/7)
for now before building the Docker image.

Build the Docker image:

    $ ./scripts/docker-build.sh -t multisetups

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
    incoming traffic on at least TCP port 4001, and also maybe
    UDP 4001.  (MacOS instructions
    [here](https://support.apple.com/en-gb/guide/mac-help/mh11783/mac).)

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

    $ docker exec -it prezkp ./node_modules/snarkjs/build/cli.cjs zkey verify /ceremony/r1cs/PreZKP_10_prod.r1cs /ceremony/$ptau /ceremony/old/PreZKP_10_prod.0.zkey

Check the verification passed; last line of the output should be:

    [INFO]  snarkJS: ZKey Ok!

### Generate your contribution

Store some entropy in an `$entropy` environment variable:

    $ read -s entropy

Then type a long random string and hit `Enter`.

Calculate your contribution:

    $ docker exec -it prezkp node build/index.js contribute -d /ceremony/old -n /ceremony/new -e "$entropy"

### Generate an attestation

    $ docker exec -it prezkp node build/index.js attest -d /ceremony/new -t attestation.template.md

and then edit the generated file so that it is accurate.

### Sign your attestation

Ideally you should generate a signature alongside the attestation,
e.g.:

    $ gpg --detach-sign --armor ceremony/new/attestation.*.md

Even better, use [Keybase](https://keybase.io/) to cryptographically
prove the link between your GPG private key and your other accounts
such as GitHub, Twitter etc.

You can even sign directly with Keybase, e.g.

    $ new=ceremony/new/attestation.3.md  # adjust number as appropriate
    $ keybase sign -d -i $new -o $new.asc

or via PGP:

    $ keybase pgp sign -d -i $new -o $new.asc

### Share your contribution

To upload all the files in one go (i.e. the `.zkey` contribution,
transcript, attestation, and signature):

    $ docker exec -it prezkp node build/index.js upload -d /ceremony/new
    Contribution uploaded. Please send this multihash to the coordinator and keep your IPFS node running and connected to the IPFS network.
    QmYDsgWYRuHNYBeJABGZ6Csdj256jvj4E4WYX2dJ6w6iCj

and then follow the instructions in the output, and also let the
coordinator know how they can obtain any public key you have used for
signing the attestation - e.g. whether it's available via a PGP
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

