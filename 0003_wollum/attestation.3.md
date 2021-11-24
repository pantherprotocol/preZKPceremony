# Attestation of contribution to trusted setup ceremony

<!--
Please edit this document as you see fit.

The following information is prepopulated for convenience and transparency;
however that does not mean that it is all required.

Please exercise your personal judgement, and if necessary delete stuff
to reach a balance of privacy and transparency which is comfortable for you.
-->

## Context

Contributor name: Willem Olding
Date: Wed Nov 23 2021 22:23:50 GMT+0000 (Coordinated Universal Time)
Geographic location: Tasmania, Australia
Time taken: ~20 minutes

## Hardware used

Device(s): Lenovo ThinkCenter
CPU architecture: x64
CPU model: Intel(R) Core(TM) i5-4590T CPU @ 2.00GHz
CPU logical cores: 4
CPU bugs: cpu_meltdown spectre_v1 spectre_v2 spec_store_bypass l1tf mds swapgs itlb_multihit srbds
Memory: 7866.8984375MB

## Software used

OS type: Linux
OS platform: linux
OS version: #170-Ubuntu SMP Mon Oct 18 11:38:05 UTC 2021
Uptime: 168773
Repository upstream: 
Repository commit:  ()

## Transcript

```
node ./node_modules/snarkjs/build/cli.cjs zkey contribute /ceremony/old/PreZKP_10_prod.2.zkey /ceremony/new/PreZKP_10_prod.3.zkey
Enter a random text. (Entropy): 
[INFO]  snarkJS: Circuit Hash: 
		cfe5dde7 42e5da76 7cf4b00d 636ac35e
		ee02c799 d492db63 5ee1616a 60715852
		dcbdd452 007aaac1 e3c242e3 e248aabd
		58346957 334e2e27 815162bb fd4136f5
[INFO]  snarkJS: Contribution Hash: 
		5fff85f8 dadd1eff 3f2062db 3b99d0c7
		bf326953 9c29f7c9 fc80fcdf d9aa89fc
		664cf03f 4eba64a9 c97977e5 582a4030
		28c2cdb0 17b31618 19689f05 671cfc94
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
- [x] Rebooted the machine
- [x] Power-cycled the machine
- [ ] Wiped the storage drive
- [ ] Destroyed the machine after (!)

## Sharing of results

I intend to share the files:

- [x] via IPFS using multisetups
- [ ] via GitHub PR
- [ ] via `magic-wormhole`
- [ ] via **INSERT CLOUD STORAGE HERE**

## Any other comments (optional)

