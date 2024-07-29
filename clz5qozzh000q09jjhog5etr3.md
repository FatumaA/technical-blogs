---
title: "Auto Add Alt Text to Img Tags with Easy Alt, an AI-Powered VSCode Extension"
seoTitle: "Easy Alt: AI-Driven Alt Text for Images"
seoDescription: "Boost accessibility effortlessly with Easy Alt, an AI-powered VSCode extension that auto-generates alt text for images in HTML, JSX, and TSX files"
datePublished: Sun Jul 28 2024 15:53:15 GMT+0000 (Coordinated Universal Time)
cuid: clz5qozzh000q09jjhog5etr3
slug: auto-add-alt-text-to-img-tags-with-easy-alt-an-ai-powered-vscode-extension
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1722289843344/7c5ffe1c-1a8d-49b8-966d-aec290d58897.png
tags: vscode-extensions, huggingface, ai-tools, huggingface-inference, aifortomorrow

---

***This blog post introduces Easy Alt VSCode extension, my submission to Hashnode's AI-for-Tomorrow hackathon.***

Easy Alt is a Visual Studio Code extension that automatically adds alt text to image tags in `.html`, `.jsx` and `.tsx` files, making the process seamless for developers. It takes the hassle out of having to write meaningful image descriptions, increasing the accessibility of your products while ensuring you stay in the flow for longer.

## Why Alt Text Matters

Alt text refers to alternative text. It is an HTML image tag attribute that provides extra information about images. This is useful in the case of low network environments where images, especially large ones, may take a while to load. This is more useful in the case of visually impaired users who may be accessing your product through assistive technologies, including screen readers. Meaningful alt text enables such users to more comfortably interact with your product.

Yet as a developer, having to write alt texts as you build out different parts of your products may feel restrictive... like a blocker of sorts. Even if your team has dedicated people to write them out, having to do the back and forth of adding them as you go or via a script still feels like an extra step. It interrupts your flow but as a responsible developer, you know it has to be done.

This is the problem that Easy Alt solves for you. It automatically inserts descriptive alt text to your images as you code. All you have to do is perhaps double check the generated text.

By making this step a little more straightforward for developers, Easy Alt increases the chances that products images will be shipped along with descriptive text. And in this little way, make the web accessible to more people over time.

## Why a VSCode Extension?

I found myself stopping more and more frequently to think about alt text for my images, whether when coding or when writing blogs. Then I discovered Hashnode's AI feature could generate alt text for my images and screenshots while writing blogs. I really loved the experience of that and found it much less tasking to double check the descriptions the AI provided.

%[https://x.com/XquisiteDreamer/status/1796501071550099535] 

Soon enough, I started missing this feature when blogging in other platforms and when writing code in my editor. Hashnode spoiled me in a way and the idea was born.

I was thinking a chromium extension, a VSCode extension or maybe an npm package. The developer mind taking over, but of course: to get somewhere, you must start somewhere. Consider this my step one.

## Technical Deets

VSCode extensions are written in JavaSript/TypeScript within a NodeJS environment. I used a very simple stack of just that and the following libraries:

* HuggingFace.js, in particular the inference package, to make API calls to the model
    
* Sentry and Winston, to keep track of errors in production
    
* Node HTML Parser to grab the image tags from an active VSCode file
    

The model I went with in the end was [nlpconnect/vit-gpt2-image-captioning](https://huggingface.co/nlpconnect/vit-gpt2-image-captioning).

### Key Features

Easy Alt ships with the following core features:

* Leverages AI to generate alt texts thereby improving the accessibility of your products
    
* Works with both local images and remote images making it versatile
    
* Supports HTML, JSX and TSX files out of the box
    

### Blockers, Blockers, Everywhere

I had a fair share of issues, the majority of them being skill issues as this was my first time both building a VSCode extension and working with AI. Notably, I had to move from local-first approach to calling the HuggingFace API because of this [known issue](https://github.com/xenova/transformers.js/issues/317) and [this one](https://github.com/microsoft/vscode/issues/130367).

The plan was to use `transformers.js` directly because it auto caches models after first download, making it local-first and super efficient. But it turned out to have compatibility issues in VSCode extension environment and that the issue was due to a VSCode limitation.

I started thinking of workarounds to keep the local-first approach. I wanted to use the [wkcn/TinyCLIP-ViT-8M-16-Text-3M-YFCC15M](https://huggingface.co/wkcn/TinyCLIP-ViT-8M-16-Text-3M-YFCC15M) model, because it's a reasonably small model that could work in low resource environments. I tested it via the API and it worked fine.

Next I downloaded it via HuggingFace's hub library and tried to use it directly. Only problem? It is based on Pytorch and could not be used as was without the `transformers/js` library. Solution? Download it and export to an ONNX format.

Did that. Only to realize that actually it is a zero-shot-classification model and would not give me a description. It seemed that HuggingFace API was doing some processing behind the scenes and hence why I was getting expected results from the API call.

It was at this point, that I decided done was better than perfect and I could always improve on it as I go. I switched to this model [nlpconnect/vit-gpt2-image-captioning](https://huggingface.co/nlpconnect/vit-gpt2-image-captioning) as it was an image-to-text model and was more suitable and rewrote the code to make API calls to HuggingFace using the Inference library. That worked 🎉 , so I shipped it. Added a pretty cute website too.

![Dark-themed webpage for "Easy Alt," an AI-powered alt text generator for VS Code. Features include options like "Install Now" and "Star the Repo," along with links to About, Features, GitHub, Install, Report an Issue, and Contact. A switch toggles dark mode.](https://cdn.hashnode.com/res/hashnode/image/upload/v1722178483115/5e5429b7-ca7e-419d-aab5-14135bb1e3ab.png align="center")

### What about UI/UX?

Easy Alt is straightforward to install and gives you feedback via VSCode notification and status bar on what it is doing.

It also comes with a website that makes it easier to discover. The website links to places where you can report issues, shares how to reach me and supports dark mode 🤭

### Ready to Try?

You can [install Easy Alt](https://marketplace.visualstudio.com/items?itemName=FatumaA.easy-alt) now. At the time of writing, you will need to add a [HuggingFace token](https://huggingface.co/docs/hub/en/security-tokens) to VSCode's user settings. Please report any issues here and remember to [leave a star](https://github.com/FatumaA/easy-alt).

`"easyAlt.hfToken": "your_huggingface_token"`

### Roadmap

First thing is to stabilise the extension as bugs are discovered and to deal with known issues.

Next is to plan out a path to have it be local-first without relying on network while keeping it lightweight enough to not lag your VSCode.

## Resources

These resources were very helpful in building this:

* HuggingFace's incredibly [detailed documentation](https://huggingface.co/docs)
    
* Learn about the incredible project ONNX
    
* VSCode extension API [documentation](https://code.visualstudio.com/api)
    

Easy Alt is [open-source](https://github.com/FatumaA/easy-alt), please report any issues and get involved. You can learn more about Easy Alt by [visiting the website](https://easy-alt-ai.netlify.app/).