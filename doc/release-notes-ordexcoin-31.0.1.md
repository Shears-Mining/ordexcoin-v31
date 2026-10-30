# OrdexCoin Core 31.0.1 - difficulty fix

Maintained by Shears Mining (https://sha256-mining.shears.co.uk), on top of the v31 code
published by Gatevia. Original OrdexCoin project by the OrdexCoin team.

## Why

OrdexCoin stalled at block 5,549 (28 Oct 2026). The difficulty only adjusts every 12 blocks and by
at most 4x, so after a short burst of outside hashrate it reached ~1.98 billion, far beyond the
remaining miners. Since then a hopping pool has mined on and off at that level, which repeats the
cycle: cheap bursts for the hopper, then long freezes for everyone else.

## What changes (consensus - every node must upgrade)

- From block **5,570** the difficulty is recalculated on **every block** using ASERT (aserti3-2d,
  the algorithm Bitcoin Cash uses) with a **2-hour half-life**.
- Block 5,570 (the anchor) uses a fixed, reachable difficulty of ~250,000 (bits `0x1a431b9b`).
  Later blocks are measured from the anchor block's height and timestamp, so the restart is smooth.
- All blocks up to 5,569 are validated exactly as before.

Nodes that do not upgrade cannot follow the chain past block 5,569.

## Also fixed

- `-reindex` no longer aborts on the genesis block (BIP34/CSV/segwit are active from height 0).

## Upgrading from 25.0.0

- Stop the old node, replace `ordexcoind` / `ordexcoin-cli`, start again. The data directory and
  descriptor wallets carry over.
- **Port clash:** 31.x opens an extra local Tor port at *P2P port + 1*. If your RPC port is your
  P2P port + 1 (for example `port=25173` and `rpcport=25174`), the node stops with
  "Unable to bind to 127.0.0.1:25174". Add an explicit bind line to `ordexcoin.conf`, e.g.
  `bind=0.0.0.0:25173`, and start again.
