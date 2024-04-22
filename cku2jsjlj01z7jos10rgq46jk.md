---
title: "Flutter Layouts For Beginners"
seoTitle: "Basic Flutter Layouts"
seoDescription: "Learn about essential Flutter layouts for beginners, including Center, Row, Column, Container, Stack, ListView, and GridView widgets"
datePublished: Mon Sep 27 2021 11:08:48 GMT+0000 (Coordinated Universal Time)
cuid: cku2jsjlj01z7jos10rgq46jk
slug: flutter-layouts-for-beginners
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1713787427850/07cffbf9-1e3d-480b-a398-e73e4accf6da.png
tags: flutter, flutter-widgets

---

In this article, we understand how layouts in flutter work and the most important widgets when it comes to flutter layouts.

First things stuff, let's define what flutter is, what widgets are and what layouts are.

Flutter is a cross-platform development framework that eliminates the need to use different environments and tools to target different platforms including web, IOS and Android.

Widgets are the building blocks of flutter applications. Consider them lego blocks. The stuff you put together to compose the end product. And in flutter, at the risk of sounding cliché, everything is a widget.

Layouts are the way in which you organise items to fit in a given space. In application development, a layout refers to the way in which different User Interface elements are constrained or placed on the screen.

User interface refers to the parts of your application that a user interacts with and UI elements are the items that make up the screen. Examples of UI elements include buttons, input fields and icons.

When it comes to layouts in flutter, the most crucial widgets are:

1. Center Widget
    
2. Row Widget
    
3. Column Widget
    
4. Container Widget
    
5. Stack Widget
    
6. ListView Widget
    
7. GridView Widget
    

## Center Widget

A center widget places its children in its own middle point. By default the center widget takes up all available space such that if there are no other widgets, it would take up the entire screen. But the center widget can be given specific dimensions and in such cases, it would center its children within itself and not in the center of the entire screen.

## Row Widget

A row widget arranges its children horizontally. Its main axis is horizontal and its cross axis is vertical. By default, it packs its children close together but you can alter this behaviour using other alignment widgets such as Align and Padding. Row widgets do not scroll.

## Column Widget

A column widget is essentially the same as a row widget except the main and cross axis flip. A columns main axis is the vertical axis and its' cross axis is the horizontal axis. Therefore, a column arranges its children vertically by default and it does not scroll.

## Container Widget

A container widget is basically a presentational widget. It does not have strong opinions on how to size itself. Therefore, if no width and height is specified, it has no child and its parent has no specific dimensions, it will close in on itself and be very small.

If the previous statement is true except the parent has specific constraints, then the Container will expand to fit the parent.

If it has a child whose dimensions are set and a parent whose dimensions are not set, it will wrap itself around that child and assume the childs' size.

if it has an alignment, a child with no dimensions and a parent with dimensions, it will expand to fit the parent and place the child within itself as per the its alignment.

For a video explanation of the above including code examples, check out:

%[https://www.youtube.com/watch?v=9LpVdVVZAIg] 

## Stack Widget

This is a widget that allows you to place widgets on top of each other in an overlapping manner. It takes an array of children and uses a stacking technique, in which the widget at the bottom is the base and all other widgets within the stack are placed relative to it, to organise the children.

You can determine how the children are placed on top of the base child using alignment. Stack is commonly used to place text in front of an image.

## ListView Widget

This widget takes an array of children, arranges them vertically and makes them scrollable by default. Horizontally, the children are stretched to fit. It's higher level than column widget but comes with more out of the box than column.

ListView is commonly used with ListTile widget. ListTile is a widget that provides the common layout of having an image or icon at the start or end, a title text and a short description text below the title text; out of the box.

You will commonly find that the ListView is populated with a list of scrolling ListTiles.

## GridView Widget

This widget provides the ability to organise children in a two-dimensional manner and it scrolls. GridView Widget scrolls vertically by default, but can be configured to scroll horizontally.

You can specify how many columns the children should be arranged into by using:

```go
Gridview.count
```

And you can specify how wide a grid tile is by using:

```go
GridView.extend
```

There are other parameters you can use to customise your GridView such as crossAxisSpacing.

For a video explanation of GridView and ListView, check out this video:

%[https://www.youtube.com/watch?v=QpXZvobiGz0&t=789s] 

There are more flutter widgets to help with laying out your applications, but these are the most fundamental. You can use all the above widgets together to compose most UIs as needed.

For more information about flutter layouts, be sure to read [the documentation](https://flutter.dev/docs/development/ui/layout) and this awesomely [detailed article](https://medium.com/flutter-community/flutter-the-advanced-layout-rule-even-beginners-must-know-edc9516d1a2)