# Attestation of contribution to trusted setup ceremony

<!--
Please edit this document as you see fit.

The following information is prepopulated for convenience and transparency;
however that does not mean that it is all required.

Please exercise your personal judgement, and if necessary delete stuff
to reach a balance of privacy and transparency which is comfortable for you.
-->

## Context

Contributor name: Koh Wei Jie
Date: Wed Nov 23 2021 01:25:00 GMT+0000 (Coordinated Universal Time)
Geographic location: Undisclosed
Time taken: ~5 minutes

## Hardware used

Device(s): Thinkpad P53s
CPU architecture: x64
CPU model: Intel(R) Core(TM) i7-8565U CPU @ 1.80GHz
CPU logical cores: 8
CPU bugs: spectre_v1 spectre_v2 spec_store_bypass swapgs itlb_multihit srbds
Memory: 24GB

## Software used

OS type: Linux
OS platform: linux
OS version: #44-Ubuntu SMP Wed Oct 20 16:16:42 UTC 2021
Uptime: 298592
Repository upstream: 
Repository commit:  ()

## Transcript

```
node ./node_modules/snarkjs/build/cli.cjs zkey contribute /ceremony/old/PreZKP_10_prod.3.zkey /ceremony/new/PreZKP_10_prod.4.zkey
Enter a random text. (Entropy): 
[INFO]  snarkJS: Circuit Hash: 
		cfe5dde7 42e5da76 7cf4b00d 636ac35e
		ee02c799 d492db63 5ee1616a 60715852
		dcbdd452 007aaac1 e3c242e3 e248aabd
		58346957 334e2e27 815162bb fd4136f5
[INFO]  snarkJS: Contribution Hash: 
		16d36ba6 73df5fd3 1465e437 8e0c5d4f
		2bc55a81 35a18829 2ddbd9db 1046283e
		b4955c1c 83eb5534 6714e955 a92d0c4e
		b288c61e b5c0b9de 7fb3a318 2955a457
```

## Entropy sources

- [x] mashed keyboard to generate random string
- [x] bytes from /dev/urandom from machine ...
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
- [x] Power-cycled the machine
- [ ] Wiped the storage drive
- [ ] Destroyed the machine after (!)

## Sharing of results

I intend to share the files:

- [x] via IPFS using multisetups
- [ ] via GitHub PR
- [ ] via `magic-wormhole`
- [x] via Dropbox

## Any other comments (optional)

