---
title: "On Design Tokens, Variables, and Penpot"
seoTitle: "Design Tokens, Variables and Penpot"
seoDescription: "Navigating design tokens, variables, and Penpot: Insights from transitioning between Figma and Penpot in Wikimedia's design system"
datePublished: Mon Dec 30 2024 10:56:33 GMT+0000 (Coordinated Universal Time)
cuid: cm5axchih000108l69gkd5prn
slug: on-design-tokens-variables-and-penpot
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1735555183851/31262e1a-2dcd-474b-bc4d-d3af34ead48a.png
tags: design, figma, design-tokens, penpot, design-engineer, design-tools

---

Working on Wikimedia’s [Figma-based design system (Codex)](https://doc.wikimedia.org/codex/latest/) means diving deep into Figma, Penpot, and all things product design. As a developer who has not spent enough time on the design side, I struggled with suddenly working with an entire design system and getting up to par with best design practices.

This is a reflection on the nuances and terms I learned thus far, and how I navigated overwhelm and anxiety as I got deeper into my project.

## Everyone Struggles

First is to acknowledge that everyone struggles. The pros started somewhere, and they are not equally competent in all areas. Seeking help and clarity is a sign of humility and an understanding of your constraints. There is no shame in needing help. The secret is to accept this and keep going.

## New Terminology

To my surprise, it turned out that I did not understand what design tokens were, how they differed from Figma variables, and how semantic naming in design tied in. I spent some time reading on this, figuring out the differences, and researching how all this translated to [Penpot](https://penpot.app/).

I asked for help and tips in a [public tweet](https://x.com/XquisiteDreamer/status/1865352355237044532) and through it got to have an insightful discussion with [Jonny Summers-Muir](https://x.com/JSummersMuir) who does Product Design at [Supabase](https://supabase.com/). He also shared [this resource](https://www.youtube.com/watch?v=JyCmacSyDY4) which was a nice introduction to Figma-based design systems.

I frequented Penpot’s [community forum](https://community.penpot.app/) and their [docs](https://help.penpot.app/technical-guide/) whenever I was stuck and used the [Figma YouTube channel](https://www.youtube.com/@Figma).

## File Organization

Penpot’s variables and components do not map exactly with Figma, and Penpot does not yet support variants (in a similar way as Figma) and design tokens. This meant that the organization of Wikimedia’s design system also needed to be translated to Penpot in a way that made sense.

I had to take a step back and look at a component in Figma and pull out the different states and variants. Pick out the colors and relate them to their states. Then I had to organize this information in a way that would be simple to maintain going forward and that allowed for correction or rearrangement.

I used the existing naming of the Figma components to help guide this process.

Now, Penpot has a [plugin that helps with migrating from Figma](https://www.figma.com/community/plugin/1219369440655168734/penpot-exporter) but that has its little nuances, for example, it does not pick out colors. This plugin is quite helpful and I used it to help streamline this process.

## Ask for Help

At some point, I could not access the icons, fonts, and typography. I played around and could not find them and had other questions, so I reached out to my mentor on the project and he promptly organized a meeting with another team member who cleared that up for me.

The whole thing was fast and straight to the point. And my mentor was available to offer assurance and guidance. From this, I felt confident to ask whenever I needed feedback or clarity.

## Managing overwhelm

Other than the technicalities, I felt another subtle resistance. This one lived in my mind. This design system is already complete, a work of beauty… what if I messed it up? What if I was moving too slowly to get to all the points I wanted during this contract period? It felt like I was standing at the feet of a mountain and wondering how on earth I was meant to get to the pinnacle in style and within schedule.

I employed my strategies for handling overwhelm, which is taking deep breaths and focusing on just the next step. I frequently reminded myself that it was NOT a life and death matter and to trust that the project mentor would alert me in case I needed to make adjustments. And that it was ok if somewhere along the journey I showed struggle.

## Next Steps

Now that I’ve gotten into some routine and I’m comfortable translating between Figma and Penpot, I look forward to developing an understanding of accessibility in design and how that applies practically. I’m also looking forward to experimenting with the corresponding [npm package](https://www.npmjs.com/package/@wikimedia/codex) and learning what the entire design-dev-opensource workflow looks like.

## Resources

In case I sparked some curiosity in you, here are some resources about Penpot, Figma and Codex:

* [Codex’s documentation site](https://doc.wikimedia.org/codex/latest/)
    
* [Codex's NPM Package](https://www.npmjs.com/package/@wikimedia/codex)
    
* [Penpot's export plugin](https://www.figma.com/community/plugin/1219369440655168734/penpot-exporter)
    
* [Penpot’s documentation](https://help.penpot.app/user-guide/)
    
* [Penpot’s YouTube channel](https://www.youtube.com/@Penpot)
    
* [Figma’s YouTube channel](https://www.youtube.com/@Figma)