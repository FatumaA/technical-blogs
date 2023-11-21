---
title: "Markdown Basics and Beyond"
datePublished: Tue Nov 21 2023 13:44:59 GMT+0000 (Coordinated Universal Time)
cuid: clp8e13ny001109kw9h5a1ftr
slug: markdown-basics-and-beyond
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1700574275145/88eb3ec2-b8a7-4571-819b-906b745c3b88.png
tags: markdown, technical-writing-1, markdown-cheat-sheet, markdown-how-to-write-markdown-file, markdown-syntax

---

Markdown has always been one of those things where I just wing it when I encounter it. You know, those technologies you promise you will get around to but you simply don't... unless something forces you to.  
I recently had such a moment, found myself down a bit of a rabbit hole and decided to pen down all the weird and cool stuff I discovered about Markdown.

# What is Markdown?

Markdown is a type of markup language. A markup language is a way computers represent text using a common set of rules in the same way smoke signals contain agreed-upon signs that mean known things. Simply put, Markdown is a way to easily manipulate plain text into different formats.

Examples of popular markup languages include HTML and XML. While Markdown is just as straightforward to pick up as HTML is, It is only concerned with transforming text and HTML also defines the structure of a page.

# Why Learn Markdown?

Markdown is used in technical teams as part of technical documentation. It can be easily versioned with git and is adapted as part of the docs as code approach.

It is adapted by [GitHub](https://github.blog/2015-01-06-how-github-uses-github-to-document-github/) and is used for the READMEs' on GitHub repositories. It is also championed by [Docusaurus](https://www.markdownguide.org/tools/docusaurus/). This makes it a useful and valuable skill to have.

Markdown is versatile, easy to learn and portable. This means that you can use your markdown across different applications.

# Markdown Syntax

Markdown syntax is simple and can be understood even without much context. Markdown syntax is divided into two - the original syntax and the extended syntax. The original syntax covers the basics of manipulating text and the extended one is built on top of that and adds more ways to represent the text.

Let's look at some of the syntax below:

***Note: You will need a text editor to use Markdown and the files need to be in a '.md' or '.markdown' extension. Also,I have not divided the examples along the original vs extended syntax.***

## Basic Syntax

### Headings and Text Styles

```markdown
# this heading 1 
## and this is heading 2
**this text is bold**
*and this is italic*
```

* #, ##, ### - Heading 1, 2 and 3 respectively.
    
* Two asterisks \* in front of a text followed by two more asterisks make the text bold.
    
* Make that one asterisk\* instead and you have italic text.
    

### Images and Links

```markdown
[links to google](https://google.com)
![random image](some_image.png)
```

* \[\]() - This renders an underlined link. Put the text in the square brackets and the actual link in the curved brackets.
    
* !\[\]() - Similar to the link syntax but with a leading exclamation mark. You can put an alt text or a description in the square brackets and the image in the curved brackets.
    

### Tables and Lists

```markdown
| Id | Name |
|--- | ---- |
| 1 | Fatuma |
| 2 | **Amina** |
| 3 | *Zaytuna* |
```

![A render of the table defined in the markdown code above](https://cdn.hashnode.com/res/hashnode/image/upload/v1700570941441/69bf8355-36c4-4518-b523-f1ae1d988fc8.png align="left")

* Three dashes in a row render a horizontal line. This can be used to separate content better.
    
* Pipes | and dashes -- are used together to render tables. The pipes represent the column lines and the dashes define the header. Simply add text between the pipes. You can add text styles as needed.
    
* Simply type in numbers to render an ordered list. Nothing extra is needed here.
    
* Use a single dash followed by a space before your text to render an unordered list.
    

### Code and Quotes

![An image representing markdown code blocks with the result on its left](https://cdn.hashnode.com/res/hashnode/image/upload/v1700571231646/df92c81d-817c-4128-87a4-ac74a7b38ab8.png align="left")

* Enclose code in single backticks to render it inline.
    
* Use three \`\`\` on separate lines with your code between them to render a block of code. The code should be on its line.
    
* Use a right-angle bracket followed by a space before your text to render it as a block quote.
    

## Beyond Basic Syntax

### Task Lists

```markdown
- [ ] Task one undone
- [x] Task two complete
- [ ] Task three procastinated
```

* A dash followed by a pair of square brackets with a space between them, a space and a text will render a task list.
    
* Type an 'X' in the square brackets to mark it as complete.
    

### Emojis

```markdown
I :heart: you or I ❤️ you
```

* Enclose the name of the emoji between two colons to render it.
    
* Or simply copy and paste the emoji if you do not know its name.
    

### Text Highlights

```markdown
==hello!!!==
```

* Two equal signs followed by text and two more equal signs will highlight the text
    

## HTML in Markdown?

```markdown
# Hello! 
I can handle HTML code. 
<b>Just like so!</b>
<iframe class="absolute top-0 rounded-lg h-full w-full">
</iframe>
```

Markdown translates text to HTML under the hood and so supports HTML. This means that you can use HTML tags directly in your markdown files and it will understand how to render it. I find this most useful when it comes to embedding videos within your Markdown files.

# The Markdown Ecosystem

The Markdown ecosystem includes markdown preprocessors and variants. Markdown preprocessors take your .md files and convert them into other formats such as pdf. Examples include [Marked](https://marked.js.org/).

Markdown variants are different implementations of the original markdown. This includes syntax additions and feature extensions. Popular ones include [Github Flavored Markdown](https://github.github.com/gfm/) and [R Markdown](https://rmarkdown.rstudio.com/).

# Resources

* [Markdown Guide Website](https://www.markdownguide.org/)
    
* [Markdown Table Generator](https://www.tablesgenerator.com/markdown_tables)
    
* [Markdown Web Editor](https://dillinger.io/)