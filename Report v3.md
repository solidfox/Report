---
bibliography: [references.bib]
---

# Untitled Manuscript #

Daniel Schlaug, Tomas Ekholm. Philipp Haller and Lucas Weiner

# 1. Abstract #

Write last.

# 2. Introduction #

*It's OK to summarize state of the art in the introduction, however, only in a short way (perhaps a paragraph or two).*

This work explores a new approach to functional GUI programming that enables a nested, modular application architecture while completely restricting side-effects and mutation to one place in the root of the application — thus achieving rich GUI modules that are pure functions of their immutable state. It builds on the recently popular Elm and Redux architectures, inheriting their simplicity while adding advanced modularity.

This chapter will first give a background to the Functional and Objective programming paradigms, then move on to how GUI programming has traditionally been done before showing how some problems of the traditional object oriented methods can be solved with functional programming. It will then elaborate on functional programming in a frontend, GUI context to finally end up in how this work relates to recent developments.

## 2.1. Functional programming ##

Functional programming is an alternative to the object oriented programming (OOP) and imperative programming styles that are most common today. 

Whereas OOP couples data to the methods or functions that work on it, functional programming decouples functions and data. To an OOP developer used to modeling their apps as heavily encapsulated objects this may sound insanse but consider the average class definition, it first defines the initial state, then how it may be changed through its methods. A lot of functional programs do the same thing, defining an initial state and the functions that change it in one file. The big difference is that there's no concept of `this` or `self`, only input parameters and return values. Turns out this context-less setting is much easier to reason about in the long run.

Imperative programming progressively builds up a result by mutating data into the desired form. Functional programming composes and combines functions into more specific ones that directly transforms data into the desired result without ever conceptually mutatating anything. Since computers are imperative machines, intermediate states and mutation will off course be used in the background but never needs to reach the domain that the developer has to reason about.

The example below shows the same functionality being implemented, first in highly imperative OOP and then in a functional style. There are a few things to note in

```javascript
// An example of object oriented code (JavaScript syntax)
class ExampleUserProfile {
    constructor() {
        this.fetching = true;
        this.name = null;
        this.images = [];
        this.filesChecksum = null;
    }
    receiveHttpResponse(httpResponse) {
        this.fetching = false;
        this.name = httpResponse.name;
        this.filesChecksum = "";
        for (var i; i < httpResponse.files.length; i++) {
            const file = httpResponse.files[i];
            const parts = file.name.split('.');
            const extension = parts[parts.length - 1];
            if (extension === 'jpg') {
                this.images.push(file);
            }
            this.filesChecksum += file.name;
        }
        this.filesChecksum = checksum(filesChecksum);
    }
}

// An example of functional code (JavaScript with lodash)
function createInitialState() { return { fetching: true; } }

function getFileExtension(file) { return last(file.name.split('.')); }

function isImage(file) { return 'jpg' === getFileExtension(file); }

function receiveHttpResponse(state, httpResponse) {
    return merge(
        state,
        {
            name: httpResponse.name,
            fetching: false,
            images: httpResponse.files.filter(isImage),
            filesChecksum: checksum(
                httpResponse.files
                    .map((file) => file.name)
                    .reduce((filenames, file) => filenames + file.name)
            )
        }
    )
}
```

```clojure
; Another functional example with Clojure syntax
(defn create-initial-state []
    {:fetching true})

(defn get-file-extension [file]
    (last (split (:name file) '.')))

(defn is-image [file] 
    (= "jpg" (get-file-extension file)))

(defn receive-http-response [state httpResponse]
    (assoc state
      :name           (:name httpResponse)
      :fetching       false
      :images         (filter is-image httpResponse.files)
      :files-checksum (->> httpResponse.files
                           (map :name)
                           (reduce str)
                           (checksum))))
```

Concepts like immutability and referential transparency are

## 2.2. Background ##

In the past and present GUI frameworks have been and continues to be dominated by the object oriented programming (OOP) paradigm, most often combined with some flavour of the Model View Controller (MVC) strategy pattern. While there certainly are virtues to this approach it also gives rise to complexity that front end developers will be familiar with:

1.  Heavy use of shared, mutable state makes it hard to reason about the applications behavior.
2.  Encapsulation makes it extremely hard to read or set the state of the entire application, complicating testing, debugging and hot reload.
3.  The decentralized state creates a large need for state synchronization.

In 2012 an alternative to OOP and MVC became popular as the Elm language and React framework were introduced. These made it feasible to generate the entire GUI on each state change by utilizing a really fast model of the GUI and after each state change diffing the new updated GUI model with the old one to get a hopefully much smaller set of changes for which pixels are actually re-rendered by altering the underlying, heavy GUI framework (usually the DOM). Instead of micro-managing GUI-objects the developer could now simply define the application GUI as a single pure function of the application state. This made the functional paradigm a perfect match for these two technologies and with the subsequent popularity of React, functional strategies have for the first time become mainstream in the field of GUI programming.

In 2012 the Recent developments have provided some interesting opportunities for functional programming and the use of immutable state in front-end, GUI-intense contexts. Frameworks such as React provide the ability to define applications as pure functions of their entire state while keeping rendering efficient by caching an intermediate UI model and only re-rendering the parts of this model that change in response to an updated app state. This enabled the "properties flow down, events flow up" architecture described, among others, by Facebook’s Flux and the Elm architecture. This has spurred a new interest in the functional paradigm and with the Facebook-sanctioned Flux architecture, the large front end segment of developers are seeing massive benefits from adopting functional practices.

With maximally immutable applications follows the question of how to maintain and update application state. Facebook's Flux architecture proposes to keep the entire app state in one "store". The state renders with the help of React into a UI that in the ideal case is fully described by a function of the app state. The UI generates actions — data messages that are received by the dispatcher (one per app) and mapped into functions that alter the app state. Thus the event loop is closed. Notably, state is only updated at one point - by the dispatcher - and the entire state is kept in one point - in the store.

Flux has been well received and seems to provide great advantages over the smeared out state and mutation of MVC architectures. It does, however, leave quite a bit to be desired. Most notably it provides little advice on how to keep the ideal scenario when the app is a composition of independent modules rather than a large monolith. It is also quite vague on how to handle side-effects apart from that of rendering UI. Elm's MVU architecture takes the extra step of handling side-effects but also does not mention scaling into modules.

The team at Evry has a highly invested architecture leveraging unusually high modularisation that enables them to share entire business values between apps, complete with views, models, and communication with external services. For future projects, they would like to explore the possibility of moving to a full functional reactive programming approach, but without losing any of the advantages that their current modular architecture brings. In order to achieve this the Flux architecture needs to be adapted to an environment with completely independent, stateful and nested modules.

## 2.3. Objective ##

*the goal, objectives, and requirements should be clearly described in the introduction.*

The objective has been to devise and thus prove the existence of a functional architecture for GUI applications that fulfills the following requirements:

1.  Applications have a single state that is atomically updated at only one point in the code.
2.  Applications have a single point in the code where side-effects are performed.
3.  Applications are composed of modules consisting of pure functions and values that can describe their state, state-transitions, user interface and side-effects.
4.  Modules can be rich enough to make sense as independent apps.
5.  Modules can be kept in independent repositories.
6.  Modules can be nested within other modules.
7.  A parent of a module must not have any knowledge of the module's inner workings and the module must have complete control over its API.
8.  A module must have no knowledge about its parent.
9.  Rendering can be optimised by not completely ignoring module subtrees where the input data is unchanged.
10. The application's state can be maintained while redefining functionality during development. (Also known as "hot reload”.)
11. Modules can leverage an established protocol for describing its side-effects such that performance optimizations like caching of http-requests are enabled.

## 2.4. Motivation ##

*Why immutability? Speeding up DOM rendering. Testability. Hot reload. Feature-complete modules.*

## 2.5. Contributions ##

-   Exploration of the design space.
-   A solution architecture that takes real world requirements of a large application into account.
-   A case study with an open source example implementation.
-   An open source library of functions to help in using the architecture.

# 3. Related Work #

*The complete discussion of related work should go into a separate chapter (usually chapter 2) called "Related Work". It's OK if that chapter is short, but it must be visible that an effort was made to analyze the scientific literature. I suggest to also include seminal papers such as "Why Functional Programming Matters" by Hughes:* <span class="citation">http://academic.oup.com.focus.lib.kth.se/comjnl/article/32/2/98/543535</span>

*Don't worry if some of the papers we've identified earlier seems less related: an important task of the thesis is to make connections, and explain them, also when those connections are weak. It is almost a creative exercise to take a step back, and try to look at principles, regardless of fine-grained details. Also, inspirational ideas are worthwhile to point out as well, even if relationships are not very strong. Also, it is probably a good idea to go back and see if after all your work you can find some other work going in your direction*

*Comparison to FRP?*

*Hot reload? Perhaps Erlang.*

# 4. Approach #

An application starts with a state, containing all the information needed to render the application's UI. This gets passed into a a pure function that returns the UI and renders it to a screen. The application can indicate that more information is needed from an external system through another pure function that returns a set of service requests which the engine may choose to perform in order to fill in the blanks. User interaction happens through the usual kind of events, but events can never alter state or perform side-effects themselves. Events are instead translated into actions in a way that is defined by the rendered UI. The engine can then decide on how to integrate the actions into the application state. Note the simplicity; all information is always fully available in the state.

*Problematize by using current architectures and pointing out flaws, then show how Remodular solves the flaws. Also state naïve approaches and why they did not work out.*

This chapter will describe the solution in detail, contrasting it to the common Redux approach by pointing out flaws in Redux and how those flaws may be solved.

## 4.1. Overview ##

<div id="MPFigureElement:3CFFA1B6-334A-46F9-C176-1E5F13D07C9B">

Figure 1:

</div>

*Naïve: Let modules be self contained and provide implementations for their services. Better: Assume a module knows nothing about its runtime environment and thus has no idea about how to perform services. This main*

# 5. Case Study #

# 6. Discussion and Conclusions #
