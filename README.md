# Logistics Optimization
Various adjustments to improve trader efficiency in X4 Foundations

- Our GitHub repo: https://github.com/Vectorial1024/v1024_logistics_optimization
  - Detailed changelog: https://github.com/Vectorial1024/v1024_logistics_optimization/blob/master/CHANGELOG.md
  - Advanced users may make use of commit tags
- Our EgoSoft Forums link: https://forum.egosoft.com/viewtopic.php?t=471490
- Our Steam Workshop link: https://steamcommunity.com/sharedfiles/filedetails/?id=3496466928
- Our Nexus Mods link: https://www.nexusmods.com/x4foundations/mods/1719

> Effective and efficient logistics; that's how simple it is!

------

## Quick Info
TL;DR: no player action needed!

This mod modifies the vanilla trading scripts to improve trader efficiency, and so allows logistics to work better.

The changes made by this mod can be summarised into several "bundled modules":
- Faster Trade Matching: station traders idle less when searching/matching trade offers
- (more WIP...)

Each such module will have its own README section below.

With this mod, perhaps you have less need for custom trading scripts (e.g. TaterTrader, the Mules, etc.).

## Motivation
This mod is a spiritual successor of Civilian Fleets, a 1.0-era mod that was eventually adapted into the base game as of the 7.0 update.

Following the dissolution of Civilian Fleets, the deprecated fleets of old were reorganized to serve several new trade stations.
However, it was noticed that station-based traders (especially trading-station traders) are behaving... not as expected.
They are consistently making trade deals that are apparently unoptimal.

This causes larger-scale logistics problems.

While there are other custom trading scripts to help with these logistics problems, they are often tedious to configure and manage
(e.g. usually exists as separate fleets, causing cluttering). It would be best if the vanilla one just "works", so these tedium may vanish.

It was eventually discovered/determined that the vanilla station trading logic can be improved, which motivated the creation of this mod.

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
