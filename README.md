# MudBASIC

![b-ready](https://github.com/botbreeder/mudbasic/raw/main/b-ready.png)

## Introductory thoughts

While the inhabitants are meant to be genetically evolving AIs, the world they live in, Open City, is just a reactive environment, even though it's a complex one. Modularity is a cornerstone of the desired architecture, because the world is hosted on Firebase (or any similar shared DB). The state of the world is stored online, but the dynamics of the world has to run somewhere. It runs client-side, in the browser.

When a new client connects, it takes a part of the world's running tasks, and runs these tasks.

One first design decision is: does the entire world runs full-scale 24/7, or does it run only where needed? Open City is small, but even a small city represents a lot of work. Not only the world runs only where it needs to, but it also runs at different level of details (LOD), depending on the needs of primary agents (AIs and human users).

3 LODs can be distinguished.

- Low details level is only the minimum: where are things, is there anything terribly important happening, when does things begin and end, and that's about it.
- Mid-level is about what's happening: who's involved (and what), what exactly is happening, but still not how (only a distant description).
- High-level of details includes not only what's happening but also _how_ it's happening, and why, using [scenes/scripts](https://tinycog.sourceforge.net/wiki/Scene_Based_Reasoning).

Having several LODs means that the system must be able to convert a situation from one LOD to another. Downgrading from high-level to low-level looks straightforward at first, it's just a matter of forgetting certain aspects of the situation. Upgrading from low to high, on the other hand, requires the system to have a schema of the corresponding "typical" situation at the higher level, so blanks can be filled in. Of course the schemas can include stochastic parameters.

But it's not enough. If an agent witnesses a situation at high-level, and this situation later gets downgraded to a lower level of details, if the agent meets the situation again, after time has passed, it will expect the situation to have evolved plausibly. This means that as long as agents "remember" a situation, even if the situation's been downgraded at some point, upgrading it is not a free process. Indeed upgrading is subject to various constraints due to how agents expect the situation to evolve. So... some "story slots" are free, while other story slots are already set because of how agents remember the situation.

## Low details level

Here is a low details description of a typical western-style citizen life differentiator.

- Birth, age, death,
- Parents, children,
- Friends,
- Home: house / flat, pets,
- Job / vacation,
- Spare time, week-ends: hobby, sport, art, social,
- Groceries, shopping habits.

## Unstructured BASIC and the entities

No, this is not the next Trent Reznor. This is Good Old BASIC. With line numbers. Why?

Defining all these things takes a lot of abstraction, and I don't think we can do it without natural language. Concurrently, true natural language is full of ambiguities and edge cases, so not exactly the right tool for the job.

The approach taken here is that of a controlled language, made of statements that look like natural language sentences, with moving parts. These statements are identified by numbers, and they take arguments that can, among other things, be references to other statements.

Meet Jackie the cat and her human pet Jane.

```INI
; Jackie the cat
10 = a cat
20 = the name of 10 is "Jackie"
30 = the color of 10 is mostly white
40 = 10 is fat and lazy

; Jane the human pet of Jackie
110 = a woman
120 = the name of 110 is "Jane"
130 = 110 belongs to 10
```

Sometimes we need to say things about statements themselves. For instance:

```INI
50 =  10 believes that 130 is true
140 = 130 is actually false
```

Very large programs wouldn't be easy to manipulate as monolithic blocks, but the line-numbering thing works fine for small connected chunks.

## Narratives as reusable blocks

A block of declarative code can be packaged like a function. Let's call these blocks _narratives_.

```INI
[10 is a cat]
110 = 10 is an animal
120 = 10 has fur
130 = the size of 10 is small
```

A narrative is a user-defined statement. The first line is the header of the narrative. It declares the syntax of the statement being defined. In this example, `10` represents the thing the narrative is applied to. It's like the argument of a function.

Narratives provide modularity by separating code in different line-number spaces, so the statement `110` of one narrative is distinguished from the statement `110` of another narrative.

> _**Side-note**: As you can see, we're using the ancient INI file format here. I am well aware of modern solutions like [TOML](https://toml.io/en/) or [ENO](https://eno-lang.org/). I just happen to love archeology and vintageware._

## Defining/using archetypes

Archetypes are prototypes with a different name because heck, we're not doing OOP here. An archetype is a narrative without parameters. An archetype is usually a type of things or, if you prefer, a typical thing of some kind. It can also be an individual if instantiated only once.

Keep it natural. The cat would really be:

```INI
[a cat]
0 =  an animal
10 = 0 has fur
20 = the size of 0 is small
30 = 0 has (4) legs and a tail
```

`0` is a reference to the statement being defined itself (the header of the narrative).

Definitions can be modified by the higher-level "caller". It goes like:

```INI
10 = a cat
20 = the name of 10 is "Jackie"
30 = the color of 10 is mostly white
40 = 10 is fat and lazy
50 = 10 has (3) legs ; overriding the typical number of legs of a cat
```

The statement `10` creates an instance of `a cat`. Jackie has 3 legs instead of 4 though, because the statement `50` overrides the statement `30` of `a cat`.

Local overriding also works on non-archetypal narratives (i.e. narratives with non-zero arity).

## Literal values & comments

While numbers are usually references to a statement within the narrative, numbers between parentheses are understood as **_literal numbers_**, like the number of legs in the example above.

Texts between double quotes are understood as **_literal strings_**.

`true` and `false` are understood as **_literal booleans_**.

Everything after a semi-colon `;` on a line is a **_comment_**.

## Fancy neo/retro line-numbering

Since it's a modern unstructured BASIC, we'll pimp our line-numbers and allow several dot-separated numbers instead, like this:

```INI
10 =   a cat
20.1 = the name of 10 is "Jackie"
20.2 = the color of 10 is mostly white
30 =   10 is fat and lazy
```

If you make a statement about `20`, it would apply to both `20.1` and `20.2`.

`20.0` is not the same as `20`.

## How do you define real

Let's go back to this:

```INI
; Jackie the cat
10 = a cat
20 = the name of 10 is "Jackie"
30 = the color of 10 is mostly white
40 = 10 is fat and lazy

; Jane the human pet of Jackie
110 = a woman
120 = the name of 110 is "Jane"
130 = 110 belongs to 10

; Surprise
50 =  10 believes that 130 is true
140 = 130 is actually false
```

Did you notice that the `140` above tastes like [INTERCAL](https://en.wikipedia.org/wiki/COMEFROM)? It really makes you wonder what is real. If writing a statement is not enough to make it true, how do we finally make things true? After all, another part of the program could declare that `140` is false too!

The answer is, you don't. You don't define real, you only build structures in the air. Most of the time, you're defining _how a subject sees_ things. Subjectively. Sometimes it goes fractal, you're defining how a subject thinks another subject sees things. Then at the end there is one true thing: the human user sitting in front of the computer. The point of view of the end-user is the entry point to... real.

Simply put, what matters is what happens next. A cat is a cat, no matter what. Is it real or not, doesn't change a thing. It will move just the same.

# The world

##  The ECAES architecture

The [Entity / Component / System](https://en.wikipedia.org/wiki/Entity_component_system) architecture is a well established, efficient way of running a virtual world. ECS is good at handling a lot of heterogeneous entities at high framerates.

ECAES would be an **Entity / Component-As-Entity / System** architecture.

The line `10 = a cat` seems to be creating an entity, an instance of the narrative called `a cat`. It feels pretty natural.

The line `20 = the name of 10 is "Jackie"` on the other hand, feels more like a component, a datum we're attaching to `10`.

Of course, their arity is different: `a cat` has an arity of 0, and `the name of # is #` has an arity of 2. But apart from that, they're both narratives, so they should be treated the same.

MudBASIC is an architecture where every component is also an entity. It may sound weird, but `20 = the name of 10 is "Jackie"` is not only a component attached to `10 = a cat`. It is also an instance of the narrative `the name of # is #` and as such, an entity, which can have components attached to it.

Components are entities. Components can have components.

Systems are still classical ECS systems (with a big warning sign on the scheduler though).

## Scheduling in a distributed environment

The way I see it, the main drawback of the ECS architecture is that systems are very dependent on their ordering. Adding new systems to already existing systems can be disruptive. Big world? Big headache.

To me, this is not a problem you solve with code. This is a problem you solve with a simple rule: **do not rely on systems execution order**. If, from the very beginning, you keep in mind that you never know which systems run before/after another one, then the scheduling problem vanishes. 

Moreover there's a crucial benefit here since we're working in a distributed environment. The datastore is centralized on Firebase, but it's serverless: everything runs client-side, in the browsers of users. **Every system can be assigned to a client, so everything runs concurrently.**

The architecture doesn't even have to run every system every cycle. Instead, systems have an "ideal frequency", and the architecture balances work automatically by redistributing systems to clients when needed, in order to get closer to the desired frequencies. There is no central controller, clients communicate through a **scheduling board** to cooperate and achieve adaptive balancing.

The world isn't deterministic, but it works as expected, because it is designed from the ground up in the perspective of doing everything asynchronously and concurrently. It can be slow: we're not making a 60fps game, we're telling a story. Events will happen in time.

Clients always share their current contribution score when they update things. Their `ContribScore` reflects how well they're doing. `ContribScore = FreqScore * EaseScore`. Then `FreqScore = RealFreq / IdealFreq` and `EaseScore = IdleTime / TotalTime`.





