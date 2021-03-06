# 🎿 Bootilities

A set of utilities generated with Bootstrap 5 to mimic Tailwind CSS functional paradigm.

## Getting Started

### Installation

#### From NPM

```
npm install bootilities --save
```

#### Directly from GitHub:

```
npm install thiagomajesk/bootilities#master
```

### Usage

Since we have refactored the code to support Sass modules, you must call the library with `@use` instead of `@import`.

```scss
@use "bootilities";
```

To use our utilities simply merge the available variables from the `bootilities` module with Bootstrap's `$utilities` map. You can disabled, merge or rewrite utilities as you see fit, take a look at our [`bootilities-utilities.scss`](https://github.com/thiagomajesk/bootilities/blob/master/scss/bootilities-utilities.scss) for usage examples.

```scss
@use "bootilities";

// Add only the background color utilities to the bundle
$utilities: map-merge($utilities, bootilities.$background-color-utilities);
```

You can also customize our default configurations using `with`. Take a look at our [`_config.scss`](https://github.com/thiagomajesk/bootilities/blob/master/scss/_config.scss) file to see the variables marked with `!default`.

```scss
@use "bootilities" with ($colors: ("magenta-500": "#ff00ff"));
```

> Take a look at our [`/variables`](https://github.com/thiagomajesk/bootilities/tree/master/scss/variables) directory to see the variables exposed by the module - you can also `@use` those files to access the variables you want to use. Alternatively, you can run `@debug module-variables("bootilities")` to see all the default variables exposed by the bootilities module.

### A note on PurgeCSS

One of the main disadvantages of any framework that generates CSS dynamically is that your output files can grow quite large if you don't take proper care. Even though the library is segmented into logical groups that could be imported individually, I heavily recommend that you use [PurgeCSS](https://purgecss.com) from the start - even for development. This can greatly reduce the size of the output file and provide a better experience while developing. 

I'm also looking at providing options and switches to conditionally generate parts of the utilities to better improve the end-user experience in the future.

## Disclaimer

<details>

<summary>
I ❤ Tailwind CSS as much as the next guy, so much that I decided to use Bootstrap's brand new utility API to port some of that magic over...
</summary>

---

### Bear with me

I'm just a developer and the majority of my projects require quick iteration and for the most part, I don't have a designer at my disposal.
This means that, even though I like designing web pages, I don't have the time or patience to come up with a robust and consistent design/component system from scratch for every project I work on.

Tailwind CSS is awesome with its utility-first approach, which comes in handy when you need the freedom to work on custom designs.
However, since it's a very thin abstraction layer over pure CSS styles, you are on your own - which is both good and bad depending on what you need.
In my case, I just want to work on some functionality and see things on the screen as soon as possible and Bootstrap has almost anything you might need out-of-the-box.

### About components & abstractions

Although you can extract your utilities into components to accommodate your application design requirements with Tailwind, with long-term applications, chances are you will eventually wind up in the same place: with a custom framework that has buttons, cards, tables, dropdowns, and so on, with the exception that **you** are the one that has to keep it consistent. It's a lot of work if you are not up for it.

> The recommended way to work with Tailwind is about abstracting your code into reusable components. But after trying this first I have to admit that for large projects it tends to get very repetitive very fast. That is unless you already have an established component system at your disposal of course - like Tailwind UI. Having tried that multiple times in different projects in the past, I feel that it's just an extra step that generates unwanted overhead in my projects. Also, consider this: Instead of waiting for patterns to emerge so you can then extract and refactor your code into components, you can start your project with the most common abstractions from the beginning. Besides that, if you don't find what you need or you need a super-specific component, you can just go ahead and create it with CSS as you normally would.

Because of this, I think Bootstrap is an excellent choice if you want a full-fledged framework to help you focus on delivering value as quickly as possible. Besides that, with Bootstrap 5 you have a new utility API at your disposal that will boost your productivity - just no leaving anything behind.

### But TailwindCSS is awesome!

Yes, it is and Bootstrap is as well. It's not a dichotomy, two things **can** be true at once. I believe both tools were created by two of the most capable people I've ever known and are excellent at solving different problems. I've tried both approaches and saw where and when each one of those falls short for my use case, so here's my attempt to bridge that gap. I don't expect anybody to be convinced only by my words, try it and see for yourself.

### Why didn't you implement Bootstrap in TailwindCSS?

This crossed my mind at one point and I think it could be a valid approach as well. But I was not up for the sheer amount of work that would go on creating and maintaining it. Bootstrap is a very mature and battle-tested framework, so the decision on what direction to go was made simpler by that fact. Also, I was catching myself importing Bootstrap components like dropdowns, modal, and popovers on my Tailwind projects more frequently than I'd care to admit just for the ease of it, so...
</details>
  
---

## Notable differences

The utilities generated using the Bootstrap API have two main differences from the ones generated by TailwindCSS: 

The first one is that Bootstrap in its current implementation still uses the same naming convention from previous versions. This means that generated utilities will have a suffix appended to the class name that represents the state variant, like: `mx-sm-2`, `mx-hover-2`, `mx-focus-2`, and `mx-print-2`. 

The second one is that Bootstrap API needs to generate utilities with a prefix. Because of this, it's not possible to generate utilities like: `flex`, `block` in a consistent fashion.

> This is actually doable but you end up with something like `sm-flex` or `sm-block` because the suffix is absent. See this issue for more information on the topic: https://github.com/twbs/bootstrap/issues/32183.

Even though this is not my preferred way to work and I do think that Tailwind did a much better job at naming its utilities, this lib does not aim to override the Bootstrap conventions completely (just yet). 

### Naming conventions 

Replicating 100% of Tailwind utilities under Bootstrap has some caveats to take into consideration: 

- The major one is that would be necessary to rewrite the current utility API to generate classes with the `state:name` convention.

- The current convention does not stop at the level of utility classes, it's adopted framework-wide. Containers, columns, and components all share the same convention: `container-lg`, `col-lg`, `btn-sm`, etc.

- Bootstrap still has some mad naming inconsistencies. While `text-md-start` takes effect starting on medium viewport size, `shadow-lg` and `btn-lg` are actually just size modifiers (like in Tailwind).

### Compromises

So, taking all of this into consideration these are the guidelines I'm using to provide a better experience out of the box (all things considered):

- Single-word utilities will retain the current prefix requirement, so `flex` in Tailwind would still be `d-flex` here. Most of the utilities have compound words, so this problem is greatly reduced.

- No utility classes will be generated with size modifiers. Instead, we'll generate those classes with incremental modifiers like: `border-1`, `shadow-2`, and `rounded-3` (this rule prevents things like `border-sm-sm` from happening).

- Usually, only utilities that change the page structure are useful in different screen sizes, so utilities that are less common to change in different viewports won't generate variants by default (But you'll still be able to enable those).

- Whenever possible, we'll try to reduce the verbosity of class names, so instead of `justify-content-between` we'll just keep the nice `content-between` from TailwindCSS.