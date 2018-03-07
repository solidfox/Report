# Specification

> A single-state architecture for industry scale, highly modularised, pure function front end applications

2018-01-30
Daniel Schlaug - dschlaug@kth.se  
Supervisor CSC - Philipp Haller  
Supervisors Evry - Tomas Ekholm & Lucas Wiener

## Background and objective

Recent developments provides some interesting opportunities for functional programming and the use of immutable state in front end, GUI-intense contexts. Frameworks such as React provides the ability to define applications as pure functions of their entire state while keeping rendering efficient by caching an intermediate UI-model and only re-rendering the parts of this model that changes after a change to the app state. This enabled the “properties flow down events flow up” architecture described, among others, by facebook in Flux and the Elm language in its Model-View-Update. This has spurred a new interest in the functional paradigm and with the facebook-sanctioned Flux architecture the large front end segment of developers are seeing massive benefits from coming aboard. 

Among functional languages that compile to Java ClojureScript is one of the larger ones. Interestingly ClojureScript also existed for a couple of years before React and Flux was introduced in 2013, but only after React did ClojureScript start seeing real front end use. The React approach fit Clojures strategy of immutability and purity so well that the ClojureScript wrapper of React performed better than the JavaScript counterpart. 

But with completely immutable applications follows the question of how to keep and update application state. Flux proposes to keep the entire app state in one “store”. The state renders with the help of React into a UI that in the ideal case is fully described by a function of the app state. The UI generates actions, data messages that are received by the dispatcher (one per app) and mapped into functions that alter the app-state. Thus the event loop is closed. Notably state is only updated at one point — by the dispatcher — and the entire state is kept in one point — in the store. 

Flux has been well received and seems to provide great advantages over the smeared out state and mutation of MVC architectures. Id does, however, leave quite a bit to be desired. Most notably it provides little advice on how to keep the ideal scenario when the app is a composition of independent modules rather than a large monolith. It is also quite vague on how to handle side-effects appart from that of rendering UI. Elm’s MVU architecture takes the extra step of handling side-effects but also does not mention scaling into modules. Another weakness of Flux implementations seems to be that they are riddled with hidden state in the form of closures and subscriptions.

The team at Evry have a highly invested frontend project leveraging unusually high modularisation and an architecture that they are mostly happy with. Even so they are unhappy with some entanglement that still exists and keep being reintroduced by careless developers. Thus they would like to explore the possibility of moving to a fully functional Flux approach but without losing any on their current strategies where modules are heavily shared among several applications. Modules even provide their own documentation complete with UI testing in a development tool application, a move that has been received with great positivity by their client and ensures that all modules are used in at least two applications. In order to achieve this the Flux architecture with a shared single point of mutation needs to be adapted to an environment with completely independent stateful modules. 

The desired result is an architecture that is usable in this demanding setting while staying true to the single point of mutation and state model of Flux.

## Scientific question

The question will be along the lines of “Is a single point of mutation, purely functional architecture viable for large-scale, real-world applications with demanding modularity constraints?”

The challenge lies in finding ways to keep modules operating with only pure functions on immutable state while still allowing them to be stateful. Modules may, of course, be nested and any parent of a module may want to perform modifications to its state in response to events in child-modules. There are also many situations where interaction between sibling modules is necessary. This can be in the form of one module being able to provide details on information that is displayed by a second module or a cache module providing cached access to a network resource that several modules want to poll.  Which also raises the issue of handling side-effects and the asynchrony that they give rise to.

## Method

To the fairly seasoned Clojure programmers at Evry it is not obvious whether the challenges of this project are at all possible to overcome. Although the method involves reimplementing part of or the entirety of their current app in ClojureScript the purpose of this is not to switch language but rather to test the existence of a Flux-like architecture that still handles all the unusual strategies that they are extremely happy with. The extreme level of modularisation and the medium-large scale of the app at Evry makes it a perfect case for stress-testing such an architecture.

With that said the method of study will be to first identify the architectural requirements or requests that Evry’s existing solution fulfils as well as those it would like to fulfil. Current architectures like Flux and functional design patterns will then be applied, combined and extended to the best of our efforts to produce a toy prototype that appears to be fulfilling the found requirements. Only when this is done will an attempt be made to apply this to the actual apps and modules of Evry’s current solution.

This process will yield one of two results. A satisfactory solution may not be found, in which case valuable discussion on the problem will still be generated. Alternatively a satisfactory solution may be found, in which case a an architecture for applying functional programming to large-scale modular real-world application will have been found. Whether novel or curated from existing knowledge the proof of the existence of such a solution will be valuable both to   
Evry and the rest of the industry.

Our hypothesis, in other words, is that a single point of mutation, purely functional architecture is viable for real world applications with high modularity demands and this hypothesis may be tested by proving the existence of such a solution.

## Evaluation and novelty

While objective evaluation certainly is at least partly possible through the requirements worked out at the initial state, to what extent these requirements are fulfilled will likely become a more subjective question. Architectural problems often do not become apparent until very late in a development-process or when a project is built by a large team. Thus measuring architectural quality with truly quantitative methods would require numerous teams to implement several instances of the same large project — an experiment that would require resources far beyond what is available to a master thesis. 

Nevertheless certain architectural features such as decoupling, immutability and referential transparency have proved to bring about good software architecture and a combination of feedback from experienced software architects and analysis of such architectural virtues should bring about a satisfactory, albeit qualitative, evaluation.

Attempting to actually reimplement Evry’s real world application will make the limitations and difficulties clearly visible and an example of such a solution would provide the industry both confidence and guidance in shifting entire large-scale applications to a purely functional architecture.

## Pre-study

The pre-study will focus on existing architectures and design patterns that may be usable for functional front end development. Resources identified so far include Facebooks description of Flux, documentation of Elm’s Model-View-Update and implementations of Flux such as Redux. Furthermore books and papers on functional frontend development may be helpful, some identified include 
* [Why Functional Programming Matters][1]
* [Real World Haskell][2]
* [Marlow et al. There is no Fork: an Abstraction for Efficient, Concurrent, and Concise Data Access][3]
* [Composable network stacks and remote monads][4]
* [The remote monad design pattern][5]

## Conditions

Evry needs to provide access to and insight into their current application. Furthermore generous ideas, feedback and discussions related to the developed architecture have been plentiful so far and may also be expected from Tomas and Lucas at Evry going forward.

In order to evaluate the final product it needs to be examined by experienced software architects. Both Tomas and Lucas qualify as such but additional opinions would certainly be valuable.

## Timeframe

During period 3 only 50% of full-time will be spent on the thesis, thus an extra 5 weeks into the summer vacation should probably be expected to prevent too stressful a situation. Thus the project would be running from Jan 15th through the middle of July with the following stages.

1. Exploration of the problem and the requirements.
2. -\> March 20th - Combined pre-study and work on the proof of concept. (\~5 weeks @ full-time)
3. -\> April 24 - Work on preliminary report and finishing up the architecture of the proof of concept.
4. -\> Beginning of June / middle of July  - Finishing up the report and testing the proof of concept by fleshing it out to mimic a part of the existing app.
4. Middle of June / end of August - presentation.

> Fin.

[1]:	https://academic.oup.com/comjnl/article/32/2/98/543535#
[2]:	http://book.realworldhaskell.org
[3]:	https://simonmar.github.io/bib/papers/haxl-icfp14.pdf
[4]:	https://dl.acm.org/citation.cfm?id=3122968
[5]:	https://dl.acm.org/citation.cfm?id=2804311