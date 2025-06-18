# Logistics Optimization
Various adjustments to improve trader efficiency in X4 Foundations.

- Our GitHub repo: https://github.com/Vectorial1024/v1024_logistics_optimization
  - Detailed changelog: https://github.com/Vectorial1024/v1024_logistics_optimization/blob/master/CHANGELOG.md
  - Advanced users may make use of commit tags
- Our EgoSoft Forums link: https://forum.egosoft.com/viewtopic.php?t=471490
- Our Steam Workshop link: https://steamcommunity.com/sharedfiles/filedetails/?id=3496466928
- Our Nexus Mods link: https://www.nexusmods.com/x4foundations/mods/1719

> Improve your station traders; improve your trade stations!

(Actually, NPC station traders are also improved, but eyyy.)

------

## Quick Info
TL;DR: no player action needed! Also perfectly save-compatible!

This mod modifies the vanilla trading scripts to improve trader efficiency, and so allows logistics to work better.

The changes made by this mod can be summarised into several "bundled modules":
- Faster Trade Matching: station traders idle less when searching/matching trade offers
- Counter Trend Trading: some station traders reprioritize their work to stabilize station production
- Full Load Delivery: M/L/XL station traders dislike unused cargo space
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

### Expected Performance Impact
In theory, reducing idling time may momentarily decrease performance (aka "cause lag spikes") when station traders are finding trades.

------

## Counter Trend Trading
Station traders now have a small chance to engage in so-called "counter-trend" trading (elaborated in Technical Info below).

This counter-trend trading occurs only once per trade search. (Note: "no trades found" failure does not end the search!)

This works alongside the "prioritize shortage" vanilla behavior.

### Technical Information
The relevant parts of the vanilla station trader logic is roughly as follows:
- Station traders wait for their turn to find trades
- Check whether there is a shortage; if exists, stop and handle it (added since 7.0)
- Check whether some wares can be exported; if exists, stop and handle it
- Check whether some wares can be imported; if exists, stop and handle it

It should be noted that station traders follow this procedure very rigidly, leading to the following behavior (assume no outside help):
- Station is producing normally: traders ignore the ingredients, leading to eventual shortage, which may stop production
- Station ran out of ingredients: traders ignore the products, leading to reduced cash, which may actually prevent resumption

These behaviors result in stations often swinging wildly between "overworked" and "frozen", with each state being difficult to autonomously recover from.
This then destabilizes the supply chain even when resources are obviously available.

By having some (not all) station traders prioritize counter-trend trading, these swings should happen less, so station production can be greatly stabilized.

### Expected Performance Impact
No performance impact expected since this is only a reordering of inevitable events.

------

## Full Load Delivery
Mid-large (i.e. M/L/XL) station traders dislike unused cargo space.

Small (i.e. S) traders are excluded because they are small and fast:
- Their small cargo size means they are very likely to run around with full load anyway
- Their high speed makes up for potential low-usage cargo runs
- Thus, their role is to fulfill small-volume trade deals which larger traders dislike

This module inherits the mod Fully Loaded for better management.

### Technical Information
The relevant parts of the vanilla station trader logic is roughly as follows:

- When a trade offer is available, the trader calculates a trade value:
  - `Trade value = Total cargo space used * Relative price margin`
- The trade offer with the best trade value is selected, and the trader then makes the correct trade reservations

The concern is that, there is nothing to prevent traders from selecting juicy but small offers over large but bland offers.
This causes mid-large traders to run around with very low cargo space usage just because the relative margin is great.

In vanilla, some of the ware categories with the greatest price ranges (i.e. `max price / min price`) are:
- Drugs (~3.4x) (note: does not include Black Market additional markups, which would push range to ~5.5x)
- Food, Water and Energy (~2.3x)
- Refined Goods (~2.3x) (e.g. Refined Metals, Antimatter Cells, etc.)

Mid-large traders dealing with these wares will likely be inefficient with their cargo space usage.

This problem can be easily fixed by scaling down the trade value if the proposed trade offer uses very few cargo space.

### Expected Performance Impact
No performance impact expected since this is just a change of calculation formula.

------

## Patient Opportunists
Station traders may randomly extend their trade search to hopefully find better trades; exact chances depend on the ship class and the station trade range.

The chances and effects of search extension is documented below:

(Each cell contains `(chance of extended search, number of additional sectors to search if extended search)`)

| Trade range | S-Class | M-Class | L-Class | XL-Class |
| -------- | ------- | ------- | ------- | ------- | 
| 0 Sectors | (0%, +0) | (0%, +0) | (0%, +0) | (0%, +0) |
| 1 Sector | (5%, +1) | (10%, +1) | (10%, +1) | (10%, +1) |
| 2 Sectors | (10%, +2) | (20%, +2) | (20%, +1) | (20%, +1) |
| 3 Sectors | (14%, +3) | (28%, +3) | (28%, +2) | (28%, +2) |
| 4 Sectors | (17%, +4) | (35%, +4) | (35%, +2) | (35%, +2) |
| 5+ Sectors | (20%, +5) | (40%, +5) | (40%, +3) | (40%, +3) |

You may observe the following:
- L/XL traders do not want to go too far away because they are usually slow
- S traders are less likely to extend searches because of their very limiting cargo hold

With this, the trader's ship class becomes important. Ideally, most traders should be M traders, with a bit of S/L traders to handle niche cases.

This interacts with other modules in this mod, e.g. L/XL traders will want to quickly find trade offers among several sectors that can fill up its cargo hold.

### Technical Information
The relevant parts of the vanilla station trader logic is roughly as follows:
- Select a ware from commander station to find matching external trade offers
- Then, iterate through the in-range sectors list, closest sector first:
  - Then, iterate through the external trade offers in the sector:
    - Find and select the best trade offer using the trade value formula mentioned above

The problem is that, this is a greedy algorithm. With this greedy algorithm, station traders will always buy out closer trade offers,
and will generally refuse to consider cheaper trade deals that are slightly further away.

This can be easily alleviated by telling station traders to find trade offers from several sectors, *then* find the best offer among them.

A larger trade range warrants a more extended search, but to avoid potential supply failure because everyone is all in transit and unavailable,
this search extension does not trigger every time.

### Expected Performance Impact
Low performance impact expected. While extended trade searches is an expensive operation, it is mostly limited by the "1 thinking trader only" rule as described above.
