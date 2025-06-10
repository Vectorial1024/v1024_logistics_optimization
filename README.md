# Logistics Optimization
Various adjustments to improve station trader efficiency in X4 Foundations

(links etc)

> Effective and efficient logistics; that's how simple it is!

------

## Quick Info
TL;DR: no player action needed!

This mod modifies the vanilla trading scripts to improve trader efficiency, and so allows logistics to work better.

The changes made by this mod can be summarised into several "bundled modules":
- Faster Trade Matching: station traders idle less when searching/matching trade offers
- (more WIP...)

Each such module will have its own README section below.

With this mod, perhaps you no longer need to use the custom trading scripts (e.g. TaterTrader, the Mules, etc.).

------

## Faster Trade Matching
Reduce the undocumented idling time of station traders.

### Technical Information
The relevant parts of the vanilla station trader logic is roughly as follows:
- Station traders find trades one-at-a-time to prevent trade stampeding
- When a station trader is finding trades, it iterates through the tradeable ware list first, and then the trade partner sector
- There are some idling after completing the search on the ware/sector

The concern is that if the ware list is long and the sector coverage is wide (e.g. regional trade hub), then traders may spend too long trying to confirm a trade run.

As such, this mod reduces the idling time of station traders.

In theory, reducing idling time may momentarily decrease performance (aka "cause lag spikes") when station traders are finding trades.
