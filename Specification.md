# Specification

> A single-state architecture for industry-scale, highly modularised, pure-function front-end applications

2018-03-14  
Daniel Schlaug - dschlaug@kth.se  
Supervisor EECS - Philipp Haller  
Supervisors Evry - Tomas Ekholm & Lucas Wiener

## Background and objective

Recent developments provide some interesting opportunities for functional programming and the use of immutable state in front-end, GUI-intense contexts. Frameworks such as React provide the ability to define applications as pure functions of their entire state while keeping rendering efficient by caching an intermediate UI model and only re-rendering the parts of this model that change after a change to the app state. This enabled the “properties flow down, events flow up” architecture described, among others, by facebook in Flux and the Elm language in its Model-View-Update. This has spurred a new interest in the functional paradigm and with the Facebook-sanctioned Flux architecture, the large front end segment of developers are seeing massive benefits from adopting functional practices. 

With maximally immutable applications follows the question of how to maintain and update application state. Facebook’s Flux architecture proposes to keep the entire app state in one “store”. The state renders with the help of React into a UI that in the ideal case is fully described by a function of the app state. The UI generates actions, data messages that are received by the dispatcher (one per app) and mapped into functions that alter the app state. Thus the event loop is closed. Notably, state is only updated at one point — by the dispatcher — and the entire state is kept in one point — in the store. 

Flux has been well received and seems to provide great advantages over the smeared out state and mutation of MVC architectures. It does, however, leave quite a bit to be desired. Most notably it provides little advice on how to keep the ideal scenario when the app is a composition of independent modules rather than a large monolith. It is also quite vague on how to handle side-effects apart from that of rendering UI. Elm’s MVU architecture takes the extra step of handling side-effects but also does not mention scaling into modules.

The team at Evry has a highly invested architecture leveraging unusually high modularisation that enables them to share entire business values between apps, complete with views, models, and communication with external services. For future projects, they would like to explore the possibility of moving to a full functional reactive programming approach, but without losing any of the advantages that their current modular architecture brings. In order to achieve this the Flux architecture needs to be adapted to an environment with completely independent, stateful and nested modules.

The objective is to devise and thus prove the existence of an architecture that fulfills the following requirements:

1. Applications have a single state that is atomically updated at only one point in the code.
2. Applications have a single point in the code where side-effects are performed.
3. Applications are composed of modules consisting of pure functions and values that can describe their state, state-transitions, user interface and side-effects.
4. Modules can be rich enough to make sense as independent apps.
5. Modules can be kept in independent repositories.
6. Modules can be nested within other modules.
7. A parent of a module must not have any knowledge of the module’s inner workings and the module must have complete control over its API.
8. A module must have no knowledge about its parent.

Furthermore it is desired but not vital that the architecture also fulfills the following criteria:

1. There is opportunity for optimizing performance by not re-rendering module subtrees where the input data is unchanged.
2. There is opportunity to maintain the application state when redefining functionality during development. (Also known as “hot reload”.)
3. There is opportunity for a module to declare a protocol for its side-effects that enables performance optimizations through, for example, standardized caching.

## Scientific question

The question will be along the lines of “Is a single point of mutation, purely functional architecture viable for large-scale, real-world reactive applications with demanding modularity constraints?”

The challenge lies in restricting modules to pure functions on immutable data while still allowing them to be stateful in the application context. Modules also need to be nestable and any parent module may want to perform modifications to its state in response to events in child-modules. There are also many situations where interaction between sibling modules is necessary. This can be in the form of a bank-transactions module being able to provide details on an account selected in an accounts module or a cache module providing cached access to a network resource that several modules want access to.  This also raises the issue of handling side-effects and the asynchrony that they give rise to. The actions in Flux can be considered synchronous since their alterations happen on the state that generated them. With side-effects like network requests that may take minutes before they call back the state that the callback will be operating on will usually be quite different from the state that generated the side-effect. This complicates things quite a bit.

## Method

A solution will be sought by exploring, combining and extending existing design patterns and architectures such as Flux, Model-View-Update, Functional Reactive Programming and MVC.

Our hypothesis is that the architecture specified above is possible. The hypothesis may be tested by producing a proof of concept that fulfills the above requirements.

## Evaluation and novelty

In evaluating whether the objective of the project was attained a produced proof of concept will be checked against the requirements stated in the _Method_ section. If every requirement is either fulfilled or conclusively unfulfilled the objective has been met with an either confirmed or unconfirmed hypothesis.

While implementations like the Elm Architecture fulfill many of our requirements we are not aware of any solution that also handles nested stateful modules. It is our hope that formalizing such an architecture would provide the industry with both confidence and guidance in shifting entire large-scale applications to a purely functional architecture.

## Pre-study

The pre-study will focus on existing architectures and design patterns that may be usable for functional front-end development. Resources identified so far include Facebooks description of Flux, documentation of [Elm’s Model-View-Update][1][^1] and implementations of [Flux][3][^2] such as [Redux][5][^3]. Furthermore, books and papers on functional frontend development may be helpful, some identified include 

* [Czaplicki (2012) Elm: Concurrent FRP for Functional GUIs]()
* [Czaplicki (2013) Asynchronous functional reactive programming for GUIs]()
* [ Hughes (1989) Why functional programming matters]()
* [O'Sullivan et al. (2008) Real world Haskell: Code you can believe in]()
* [Marlow et al. There is no Fork: an Abstraction for Efficient, Concurrent, and Concise Data Access]()
* [Dawson et al. (2017) Composable network stacks and remote monads]()
* [Gill et al. (2016) The remote monad design pattern]()

## Conditions

Evry needs to provide access to and insight into their current application. Furthermore generous ideas, feedback, and discussions related to the developed architecture have been plentiful so far and may also be expected from Tomas and Lucas at Evry going forward.

In order to evaluate the proof of concept, it needs to be examined by experienced software architects. Both Tomas and Lucas qualify as such but additional opinions would certainly be valuable.

## Timeframe

During period 3 only 50% of full-time will be spent on the thesis, thus an extra 5 weeks into the summer vacation should probably be expected to prevent too stressful a situation. Thus the project would be running from Jan 15th through the middle of July with the following stages.

1. Exploration of the problem and the requirements.
2. -\> March 20th - Combined pre-study and work on the proof of concept. (\~5 weeks @ full-time)
3. -\> April 24 - Work on preliminary report and finishing up the architecture of the proof of concept.
4. -\> Beginning of June / middle of July  - Finishing up the report and testing the proof of concept by fleshing it out to mimic a part of the existing app.
4. Middle of June / end of August - presentation.

> Fin.

[^1]:	[https://guide.elm-lang.org/architecture/][2]

[^2]:	[https://facebook.github.io/flux/docs/overview.html][4]

[^3]:	[https://redux.js.org][6]

[1]:	https://guide.elm-lang.org/architecture/
[2]:	https://guide.elm-lang.org/architecture/
[3]:	https://facebook.github.io/flux/docs/overview.html
[4]:	https://facebook.github.io/flux/docs/overview.html
[5]:	https://redux.js.org
[6]:	https://redux.js.org
