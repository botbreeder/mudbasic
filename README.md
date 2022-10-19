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

Here is a low details description of a typical western-style citizen life.

- Birth, age, death,
- Parents, children,
- Friends,
- Home: house / flat, pets,
- Job / vacation,
- Spare time, week-ends: hobby, sport, art, social,
- shopping, groceries.

## Unstructured BASIC and the entities

Yup, good old BASIC. With line numbers. Why?

Defining all of these things takes a lot of abstraction, and I don't think we can do it without natural language. Concurrently, true natural language is full of ambiguities and special cases, so not exactly the right tool for the job.

The approach taken here is that of a controlled language, made of statements that look like natural language sentences, with placeholders. These statements are identified by numbers or identifiers, and they have placeholders that can hold references to other statements.

Here is a dumb example, Jackie the cat and her human pet Jane.

```
10 a cat
20 the name of 10 is Jackie
30 the color of 10 is mostly white
40 10 is fat and lazy

110 a woman
120 the name of 110 is Jane
130 110 belongs to 10
```

Sometimes we need to say things about statements themselves. For instance:

```
50 10 believes that 130 is true
140 130 is actually false
```

Very large programs wouldn't be easy to manipulate as monolithic blocks, but it works fine for small connected chunks.

## Narratives as reusable blocks

A block of declarative code can be packaged like a function. Let's call these blocks _narratives_.

```
[10 is a cat]

110 10 is an animal
120 10 has fur
130 the size of 10 is small
```

A narrative is a user-defined statement. The first line is the header of the narrative. It declares the syntax of the statement being defined. In this example, `10` represents the thing the narrative is applied to. It's like the argument of a function.





