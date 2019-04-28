# Modular Flux

> A single-state architecture for industry scale, highly modularised, pure function front end applications

## Background

> As software becomes more and more complex, it is more and more important to structure it well. (…) Conventional languages place conceptual limits on the way problems can be modularised. Functional languages push those limits back. (…) Since modularity is the key to successful programming, functional languages are vitally important to the real world.
> — J. Hughes 1988

So begins the classical “Why functional programming matters” from 1988. {journal:8XhzoPra} It then goes on to elegantly describe how different algorithms can be beautifully achieved through composition of a few remarkably generally applicable _and_ highly abstract functions. Yet up until recently — and quite arguably even to this day the functional paradigm remains an obscurity that might be applied to small parts of applications, like the ones in Hughes’, paper by slightly eccentric enthusiasts. Very seldom is it applied to an entire app by an entire team.

But times are changing. Recent developments provide some interesting opportunities for the functional paradigm and the use of immutability in front-end, GUI-intense contexts. Frameworks like React provides the ability to define applications as pure functions of their entire state while keeping rendering efficient by caching an intermediate UI-model and only re-rendering the parts of this model that changes after a change to the app state. This enables the “properties flow down events flow up” architecture described, among others, by facebook in Flux and the Elm language in its Model-View-Update. Suddenly GUI programming that previously required dirty workarounds and mutation even in Haskell is bringing the large front end segment of developers to the functional paradigm. Immutable, pure functions and single state is the new, shiny thing sanctioned by Facebook. The benefits are not only felt by the switchers from OO but it seems that functional advocates have finally been given a way to implement entire GUI applications that feel true to the functional paradigm in that they truly minimize and confine impurity and define the entire application as a function of its state.

### ClojureScript

Among the functional languages empowered by React is ClojureScript. Clojure is a LISP that originally targeted the JVM but in 2011 got ported to compile into JavaScript. Interestingly ClojureScript existed for a couple of years before React and Flux was introduced in 2013, but only after React was introduced did ClojureScript start seeing real use. The React approach fits Clojures strategy of immutability and purity so well that the ClojureScript wrapper of React performed better than the JavaScript counterpart prompting Clojures persistent data structures to be ported to JavaScript in the form of immutable.js.

### Confining mutation and Flux

With completely immutable applications follows the question of how to keep and update application state. Flux proposes to keep the entire app state in (ideally) one “store”. The state renders with the help of React into a UI that in the ideal case is fully described by a function of the app state. The UI generates actions, data messages that are received by the dispatcher (one per app) and mapped into functions that alter the app-state. Thus the event loop is closed. Notably state is only updated at one point — by the dispatcher — and the entire state is kept in one point — in the store. 

Flux has been extremely well received and seems to provide great advantages over the smeared out state and mutation of MVC architectures. It does, however, leave quite a bit to be desired. Most notably it provides little advice on how to keep the ideal scenario when the app is a composition of independent modules rather than a large monolith. It is also quite vague on how to handle side-effects appart from that of rendering UI. Elm’s MVU architecture takes the extra step of handling side-effects but also does not mention scaling into modules. Another weakness of Flux implementations seems to be that they are riddled with hidden state in the form of closures and subscriptions.

## Adding modularity to Flux

This work is an attempt at solving Flux’s lack in handling modularity and side-effects and thus show that even extremely large programs with stateful, independent modules in their own repositories can be implemented with the maximal purity of a single state and confined side-effects.

We introduce the concept of a view module. A view module should not be confused with a component or a view in the sense of MVC. Rather it contains all parts needed to run a certain feature or business value — e.g. interact with a bank account. Thus it needs to define its own state, the functions that operate on the state, functions generating any UI views on the state and functions requesting and describing side-effects that are required by the state. The only thing a View Module does not have that distinguishes it from an app is a runtime to perform state updates and side-effects for it. 

View modules are completely pure. They achieve this by delegating all state updates and side-effects to the confined app runtime. Since view modules can be nested delegation happens recursively up the module tree until it eventually ends up in the root of the app where a simple runtime performs the impure operations as described by child modules. The runtime does its entire job without needing any specific information about its view modules. If specifics about the child-modules is needed a parent is allowed to be aware of the public API of its direct children and can, if it so desires, choose to re-expose this API to its own parents. A parent can do this both for dependencies it wants injected and for delegations coming from its children. As delegations flow up the module tree they may be modified by a parent in order to provide features like a shared http cache or mocked side-effects during debugging.

## The plight of side-effects

Side-effects are may be split into three categories. 
* Mutation of (shared) variables or state. 
* Interactions with the outside world, including rendering to screen, disk / network IO, etc.
* Hidden state like caches, listeners, closures, etc.

All of these can change how a function behaves between calls with identical input.