# Generating an attestation after your contribution was already accepted

Since for this ceremony the process of generating an attestation was
not implemented until shortly after the zkeys were all generated and
collated in this repository, we would like to collect retroactive
attestations.

This file documents the simple steps for doing that.  It should only
take ~5-15 minutes, and would be much appreciated as a way of bringing
greater integrity to the ceremony!

Don't hesitate to ask to ask on the Telegram channel if you have any
issues or questions about this.

## Preparing a new Docker container

The `multisetups` code has been [enhanced to automatically generate
attestation documents](https://github.com/appliedzkp/multisetups/pull/13).
This is in order to make it as quick and easy as possible for contributors
to submit their attestations!

However to take advantage of this, you will need to fetch a newer version
of the code and rebuild the Docker container image.

If you haven't deleted the git repository:

    $ cd path/to/multisetups
    $ git fetch
    $ git reset --hard origin/prezkp

or if you did, simply reclone:

    $ git clone -b prezkp https://github.com/aspiers/multisetups.git
    $ cd multisetups

If you want, tag the old version of the container in case you want to
reference it in the future; however it's very unlikely this will be
needed, so feel free to skip this command:

    $ docker tag multisetups multisetups:old

Now rebuild the container:

    $ ./scripts ./scripts/docker-build.sh -t multisetups

If your old container is still running, stop it:

    $ docker stop prezkp

Now start a new container with the new code:

    $ docker run --name prezkp -d --rm -v $PWD/ceremony:/ceremony -p 4001:4001 -p 5001:5001 -p 8080:8080 multisetups

Check it started up correctly:

    $ docker logs -f prezkp

As before, at the end of the output, you should see something like:

    API server listening on /ip4/127.0.0.1/tcp/5001
    WebUI: http://127.0.0.1:5001/webui
    Gateway (readonly) server listening on /ip4/127.0.0.1/tcp/8080
    Daemon is ready

At this point you can hit `Control-C` to stop watching the logs.

### Ensure you have your old files

If you deleted your contribution files, you can reobtain them via

    $ cid=<INSERT CONTENT ID OF YOUR CONTRIBUTION HERE>
    $ docker exec -it prezkp node build/index.js download -m $cid -d /ceremony/new

If necessary, your `cid` can be retrieved by scrolling through the
chat history of the ceremony communication and finding the message you
sent in order to share your contribution.

### Generate the attestation

    $ docker exec -it prezkp node build/index.js attest -d /ceremony/new -t attestation.template.md

You should see something like:

    Wrote prepopulated attestation to /ceremony/new/attestation.5.md.
    Please edit this file, sign it, and run the 'upload' subcommand.

Edit the generated file so that it is accurate.  Please don't worry if
you took minimal steps to secure your contribution!  The template is
intended to be thorough for the benefit of future ceremonies which may
have high security requirements.  This particular PreZKP ceremony has
relatively low security requirements because no significant economic
value depends on it.

When editing, you may opt to:

- modify the date and time to reflect roughly when you
  generated the original `.zkey` file

- remove things like the system uptime data, since that refers to the
  current uptime rather than when you generated the `.zkey`

Edit whatever else you want too!

## Sign the attestation

You can do this in exactly the same way you previously signed the
transcript, e.g.

    $ gpg --detach-sign --armor ceremony/new/attestation.*.md

or with Keybase, e.g.

    $ new=ceremony/new/attestation.3.md  # adjust number as appropriate
    $ keybase sign -d -i $new -o $new.asc

or via PGP:

    $ keybase pgp sign -d -i $new -o $new.asc

## Share your contribution

Given that only two small files need to be shared this time
(`attestation.$n.md` and the accompanying signature file), if it is
quick and easy for you to do, please just submit a PR to
https://github.com/pantherprotocol/preZKPceremony/ adding those two
files to your subdirectory.  Here's a [perfect
example](https://github.com/pantherprotocol/preZKPceremony/pull/3)
thanks to Wei Jie :-)

However, if it's easier then you can just DM the files directly to
Adam in Telegram, or upload via IPFS exactly as before:

    $ docker exec -it prezkp node build/index.js upload -d /ceremony/new
    Contribution uploaded. Please send this multihash to the coordinator and keep your IPFS node running and connected to the IPFS network.
    QmYDsgWYRuHNYBeJABGZ6Csdj256jvj4E4WYX2dJ6w6iCj

or via any of the other alternatives listed at the bottom of
[`CONTRIBUTING.md`](CONTRIBUTING.md).

## Thanks!

Once again, thanks so much for your participation.  Please keep an eye
out for announcements from Panther with more details on how the
circuits of this ceremony are going to be used :-)

## Disclaimer

Please note that the original contributions steps in
[`CONTRIBUTING.md`](CONTRIBUTING.md) have been updated to include the
attestation generation steps - not in an attempt to rewrite history,
but rather because it is expected that those steps will be reused for
future ceremonies, in which it will be helpful to generate
attestations prior to uploading, rather than retroactively at the end
of the ceremony.
