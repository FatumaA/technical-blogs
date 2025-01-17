---
title: "Taking Apart and Putting Together Wikimedia's Design System"
datePublished: Fri Jan 17 2025 13:27:11 GMT+0000 (Coordinated Universal Time)
cuid: cm60snjkc000x09md2031dv6o
slug: taking-apart-and-putting-together-wikimedias-design-system
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1737120079830/f56779cf-d432-443d-a151-c4b7bcbdc6d8.png
tags: figma, design-systems, outreachy, penpot, wikimedia

---

It’s been over a month since I started working on moving [Codex](https://doc.wikimedia.org/codex/latest/), Wikimedia’s design system, from Figma to Penpot. In that time, I have come to appreciate the intricacies involved in building world-class design systems.

## What even is a Design System?

A design system is the language a product speaks. This language consists of design tokens (like colors, typography, and spacing), patterns (guidelines for handling specific tasks or layouts), and assets (like images and icons), which are combined to create components - the small parts of a User Interface like buttons, input fields, and progress bars.

These components can then be put together to build pages and interfaces—like building with Lego blocks. It’s similar to component-based development.

But a design system isn't just about visual elements, it also defines component behavior and states, making interactions predictable and intuitive across products. It includes rules and guidelines (documentation) based on user research and best practices, which helps teams use the system effectively.

This language can then be applied across a single product or many products within an organization, ensuring products speak the same language, leading to product consistency.

A design system encourages collaboration between designers, developers, and other stakeholders. By providing a unified set of resources, design systems speed up the product design and development process, improving efficiency and reducing decision fatigue. Additionally, design systems help to maintain a unified user experience, making your product recognizable and memorable.

## What is Wikimedia?

[The Wikimedia Foundation](https://wikimediafoundation.org/about/#:~:text=The%20Wikimedia%20Foundation%20is%20the,reliable%2C%20and%20available%20to%20all.) is a nonprofit organization that supports free knowledge projects and is the backbone of [Wikipedia](https://en.wikipedia.org/wiki/Main_Page) and other Wikimedia projects. The Foundation is dedicated to making knowledge accessible to everyone for free.

Wikimedia welcomes and encourages contributors worldwide and organizes these efforts through the [Wikimedia Movement](https://meta.wikimedia.org/wiki/Wikimedia_movement). This movement encompasses everyone working towards the shared goal of free knowledge. It includes technical contributors, community members, and affiliate organizations.

## How Codex Fits

Wikimedia hosts several technical projects, all with user-facing aspects. These projects need to be identifiable as related and consistent across the board. Additionally, they need to consider accessibility and internationalization because of the different cultures, countries, and communities they serve.

A cohesive design system makes meeting these standards simpler and acts as a single source of truth for all user-facing aspects of Wikimedia projects.

## Moving Codex from Figma to Penpot

I’m working on moving the entire design system from Figma to Penpot. This entails quite a few things, key among them being:

* Understanding how Figma works and how design systems are built in Figma
    
* Figuring out the mirror equivalent of doing the same in Penpot
    
* Recreating design tokens and decisions in Penpot
    
* Understanding components and the design tokens and decisions that make it up
    
* Figuring out how best to reassemble them in Penpot
    

All while keeping in mind file organization and striving to keep the new Penpot components as close to the original in behavior, states, and looks as possible.

This has been challenging mainly because Penpot does not yet support variables and variants like Figma does, meaning that each component must be recreated multiple times instead of simply setting variables and themes.

### Why the Move?

Given that [Penpot](https://penpot.app/) is open-source, it aligns better with Wikimedia’s values and mission to give them a fair shot. Also, Penpot allows for limitless seats making them more attractive to Wikimedia as they have a large community who may want to contribute and expand Codex.

## How to use Codex

Codex is available in [NPM](https://www.npmjs.com/package/@wikimedia/codex) and supports [the Vue.js web framework](https://vuejs.org/) and vanilla CSS. It is open source and [can be accessed here](https://github.com/wikimedia/design-codex).

## Final Thoughts

Frontend-facing work excites me and knowing that Wikimedia stands for the greater good makes working on this project even more fulfilling. I look forward to experimenting more with Codex on the developer side of things and getting more insight into how the design-to-development process works in design systems.

## Useful Links

* [More on Codex](https://doc.wikimedia.org/codex/latest/)
    
* [On Wikimedia Foundation](https://en.wikipedia.org/wiki/Wikimedia_Foundation)
    
* [On Design Systems](https://www.figma.com/blog/design-systems-101-what-is-a-design-system/)