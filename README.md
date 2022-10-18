# MudBASIC

## Introductory thoughts

While the inhabitants are meant to be genetically evolving AIs, the world they live in, Open City, is just a reactive environment, even though it's a complex one. Modularity is a cornerstone of the desired architecture, because the world is hosted on Firebase (or any similar shared DB). The state of the world is stored online, but the dynamics of the world has to run somewhere. It runs client-side, in the browser.

When a new client connects, it takes a part of the world's running tasks, and runs these tasks.

One first design decision is: does the entire world runs full-scale 24/7, or does it run only where needed? Open City is small, but even a small city represents a lot of work. Not only the world runs only where it needs to, but it also runs at different level of details (LOD), depending on the needs of primary agents (AIs and human users).

3 LODs can be distinguished.

- Low details level is only the minimum: where are things, is there anything terribly important happening, when does things begin and end, and that's about it.
- Mid-level is about what's happening: who's involved (and what), what exactly is happening, but still not how (only a distant description).
- High-level of details includes not only what's happening but also _how_ it's happening, and why, using [scenes/scripts](https://tinycog.sourceforge.net/wiki/Scene_Based_Reasoning) in [OpenDDL](http://openddl.org/).

Having several LODs means that the system must be able to convert a situation from one LOD to another. Downgrading from high-level to low-level looks straightforward at first, it's just a matter of forgetting certain aspects of the situation. Upgrading from low to high, on the other hand, requires the system to have a schema of the corresponding "typical" situation at the higher level, so blanks can be filled in. Of course the schemas can include stochastic parameters.

But it's not enough. If an agent witnesses a situation at high-level, and this situation later gets downgraded to a lower level of details, if the agent meets the situation again, after time has passed, it will expect the situation to have evolved plausibly. This means that as long as agents "remember" a situation, even if the situation's been downgraded at some point, upgrading it is not a free process. Indeed upgrading is subject to various constraints due to how agents expect the situation to evolve. So... some "story slots" are free, while other story slots are already set because of how agents remember the situation.

## Low details level

Here is a low details description of a typical citizen life.

- Birth, age, death,
- Parents, children,
- Friends,
- Home: house / flat, pets,
- Job / vacation,
- Spare time, week-ends: hobby, sport, art,
- shopping, groceries.















