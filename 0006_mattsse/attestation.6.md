# Attestation of contribution to trusted setup ceremony

<!--
Please edit this document as you see fit.

The following information is prepopulated for convenience and transparency;
however that does not mean that it is all required.

Please exercise your personal judgement, and if necessary delete stuff
to reach a balance of privacy and transparency which is comfortable for you.
-->

## Context

Contributor name: Matthias Seitz
Date: Tue Nov 23 15:28:04 2021 GMT+0000 (Coordinated Universal Time)
Geographic location: Undisclosed
Time taken: ~10 minutes

## Hardware used

Device(s): Macbook Pro
CPU architecture: x64
CPU model: 2,3 GHz 8-Core Intel Core i9
CPU logical cores: 8
CPU bugs: spectre_v1 spectre_v2 spec_store_bypass swapgs itlb_multihit srbds
Memory: 32GB

## Software used

OS type: Mac Os
OS platform: Mac Os
OS version: 11.5.2
Uptime: 23431
Repository upstream: 
Repository commit:  ()

## Transcript

```
node ./node_modules/snarkjs/build/cli.cjs zkey contribute /ceremony/old/PreZKP_10_prod.5.zkey /ceremony/new/PreZKP_10_prod.6.zkey
Enter a random text. (Entropy): 
[INFO]  snarkJS: Circuit Hash: 
		cfe5dde7 42e5da76 7cf4b00d 636ac35e
		ee02c799 d492db63 5ee1616a 60715852
		dcbdd452 007aaac1 e3c242e3 e248aabd
		58346957 334e2e27 815162bb fd4136f5
[INFO]  snarkJS: Contribution Hash: 
		b94a9815 5d90c865 38197491 64315072
		e6a27e6f a9f355bd d84966ef f2b8507d
		998f8ce7 7f3466e7 deb31270 dad2e2f8
		975e240e bcac95eb 6ba78e61 29b7530c
```

## Entropy sources

- [x] mashed keyboard to generate random string
- [ ] bytes from /dev/urandom from machine ...
- [ ] webcam
- [ ] audio input
- [ ] Geiger counter
- [ ] random words from random books
- [ ] asked people for random numbers / words

## Side channel defenses

- [ ] Left other processes running
- [ ] Didn't use swap space
- [ ] Ran the software on secure hardware
- [ ] Didn't tell anyone except coordinator about participation in advance

## Other defenses

- [ ] Disconnected network during generation of contribution
- [ ] Rebooted with airgap before generation of contribution
- [ ] Full disk encryption
- [ ] Put the machine in a Faraday cage
- [ ] Used multiple machines and randomly picked the result of one of them
- [x] Ensured no one could see the screen

## Post processing

- [ ] Filled up memory with memtester
- [ ] Rebooted the machine
- [ ] Power-cycled the machine
- [ ] Wiped the storage drive
- [ ] Destroyed the machine after (!)

## Sharing of results

I intend to share the files:

- [x] via IPFS using multisetups
- [ ] via GitHub PR
- [x] via `magic-wormhole`
- [ ] via **INSERT CLOUD STORAGE HERE**

## Any other comments (optional)

