---
layout: post
title:  CSS Positioning 
description: Full explanation of CSS positioning values, static, fixed, relative, absolute and sticky
date:   2020-06-18
---
I found myself constantly being confused between different positioning values in CSS. Hence, I decided to go back and understand them fully in order to get a concrete understanding of each of them.

If you don't know what `position` in CSS is, it's a property used to position your elements.

So, there are 5 values in total for it:

1.  Static
2.  Relative
3.  Absolute
4.  Sticky
5.  Fixed

Along with these value you have to specify either of the properties like top, bottom, left and right which essentially act like margins. Without this, the position (except static) values won't work.

Let's go throught each value one by one.

## Static
This is selected by default. This is the only value that doesn't need properties, top, bottom, right and left. If you see any element that doesn't have a position value, just assume, it has `static` in its CSS invisibly.

CSS code of this property look like:
```
.static {
    position: static;
    border: 2px solid;
    background-color: rgb(190, 121, 255);
}
```
With HTML code found [here](https://github.com/zainafzal88/css-positioning), the above results in:

<p align="center">
  <img src="/assets/images/2020-06-18/static-positioning.png">
</p>

## Relative
This is the same as `static` however, it needs to have either properties, top, bottom, left and right for it to work. It pulls the element out its original place on the page and can move it around in the way that it can hide other elements near it.

It's written as `position: relative;` CSS code is:

```
.child-one {
    position: relative;
    background-color: darkgreen;
    left: 20px;
    top: 30px;
    color: #fff;
}
```
With HTML code found [here](https://github.com/zainafzal88/css-positioning), the above results in:

<p align="center">
  <img src="/assets/images/2020-06-18/relative-positioning.png">
</p>

As you can see above that the `Div 1` is overlapping `Div 2` and `Div 3` which clearly shows that `Div 1` is no longer "embedded" in the page and it's free to move without caring how it effects other elements in the page.

## Absolute
Positions the element next to its nearest parent. It doesn't care about other elements. It will hide other elements behind it if they try to effect it but it won't be effected. It's written as `position: absolute`.

Before Absolute Positioning:
<p align="center">
  <img src="/assets/images/2020-06-18/before-absolute-positioning.png">
</p>
CSS code to get below layout:
```
.child-one {
    position:absolute;
    background-color: darkgreen;
    color:#fff;
}
```
With HTML code found [here](https://github.com/zainafzal88/css-positioning/commit/d17aeb29c39e4d75b735bda34951be1415029920)

After Absolute Positioning:
<p align="center">
  <img src="/assets/images/2020-06-18/after-absolute-positioning.png">
</p>

By applying property `top:10px` on absolute position observe how the element goes relative to the viewport(browser)
<p align="center">
  <img src="/assets/images/2020-06-18/absolute-positioning-top.png">
</p>
```
.child-one {
    position: absolute;
    top: 10px;
    background-color: darkgreen;
    color: #fff;
}
```
With HTML code found [here](https://github.com/zainafzal88/css-positioning/commit/aecf537977726ab668c81fe009bbda6e6015ed6c)

To avoid the above behaviour, we can simple apply `position: relative` to its nearest parent.
```
.parent {
    background-color: red;
    position: relative;
}
.child-one {
    position: absolute;
    top: 10px;
    background-color: darkgreen;
    color: #fff;
}
```
With HTML code found [here](https://github.com/zainafzal88/css-positioning/commit/88da6bc2298c3159090e6456b8c685e2f5567b6a)
<p align="center">
  <img src="/assets/images/2020-06-18/absolute-relative-positioning.png">
</p>

## Sticky
Positions the element in a way it's always visible to the user.

What I mean by that is when `position: sticky` is applied to an element at the start of the page (for example), initally it stays in its original place and when the users scrolls through that area, that element stays fixed on the top so, even if the user has scrolled down, he can still view that element.

`sticky` position is the combination of `relative` and `fixed`. Remember the above concept, `relative` applies when element initally stays in its original place whereas `fixed` applies when the users scrolls through that area, that element stays fixed on the top so, even if the user has scrolled down, he can still view that element. 

You can view this [example](https://www.w3schools.com/css/css_positioning.asp)

CSS code would look like:

```
.child-two {
    background-color: yellow;
    position:sticky;
    top:0;
}
```

## Fixed
Positions the element next to the viewport (browser) and "glues" it in one place even when the user scrolls. Either of the properties, top, left, right and bottom are compulsory to be user with this. Otherwise, it won't work

<p align="center">
  <img src="/assets/images/2020-06-18/fixed-positioning.png">
</p>

CSS would look like:
```
.child-two {
    background-color: yellow;
    position: fixed;
    top: 0;
    left: 0;
}
```
All the code can be found in my [repository](https://github.com/zainafzal88/css-positioning)

Hope you have a better understanding now. If you have any questions, let me know. I'll be happy to clarify.

