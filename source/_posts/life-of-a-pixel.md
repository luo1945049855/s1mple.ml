---
title:  "Life of a Pixel"
date:   2020-07-18 14:00:00 +0800
categories: Chromium 
---

DOM->Style->Layout->Paint->Raster->GPU->Pixel

## Document Object Model(DOM)

## Style(CSS)

CSS parser builds a model of the style rules. This works similar to the html parser except the css syntax is more specified(less forgiving): parse...turtles...actual style.

Furthermore, it's not trivial to determine which elements a style rule selects.
Some elements may be selected by more than one rule, with conflicting declarations for a particular style property.
The resolution of this is known as the "cascade" in CSS.

## Layout 

## Compositing

* Decompose the page into layers which raster independently.
* Combine the layers on another thread(`Compositor thread`).
* The Compositor thread can handle input events from browser process. 
* Update layer list before paint because each layer is painted separately. In the future, layers will be created after paint(?).
* The Compositor can apply various properties to the way it draws a layer: transform, clip, effect, scroll.
* The Compositor thread has two copies of the tree, so that it can raster tiles from a new commit  while drawing the previous commit. We want to ensure atomic commits and two trees is the approach to do this.(Raster and Draw Quad same time.)

* Tiling: 
    Layers can be large-rastering the whole layer is expensive, and unnecessary if only part of it is visible.
    Tiles are the unit of raster work. Tiles are rastered with a pool of dedicated `raster threads`(this include image decodes).
    Tiles are prioritized based on their distance from the viewport.
    (Note: a layer actually has multiple tilings for different resolutions.)

## Paint

Paint walks the layout tree and records a linear list of paint operations.

## Raster

Raster is the step after paint, which turns paint ops into bitmaps.

* Accelerated by the GPU in `GPU process`.
* Rasterization issues OpenGL calls through Skia From `render process`.
* OpenGL, DirectX(Windows, ANGLE translate OpenGL to DirectX which build by Google).
* The output of raster is tile bitmap in GPU memory.

## Draw Quad(layers)

* Once all the tiles are rastered, the compositor thread generates "draw quads".
* A quad is like a command to draw a tile in a particular location on the screen, taking into account all the transformations applied by the layer tree.
* Each quad reference the tile's rastered output in memory.
* The quads are wrapped up in a CompositorFrame object which gets submitted to the gpu process.

## Display(viz)

* Compositor frames come from multiple renders, and from the brwoser(which has its own compositor for the UI).
* Compositor frames are associated with a "surface", representing the place where they will appear on screen.
* Surfaces can embed other surfaces. The browser UI embeds a render. Renders can embed other renders for cross-origin iframes(a.k.a. site isolation, "out of process iframe" or OOPIF).
* The Display compositor runs on the Viz Compositor Thread in the GPU process. It's part of the viz service(short for "visuals").
* The Display compositor synchronizes the incoming frames and understands the dependencies betweens embedded surfaces("surface aggregation").
* Viz uses skia to display each quad in the CompositorFrame. This allow the compositor to draw using any backend(GL, Vulkan, Metal, DX12) supported by skia, similar to raster.
* The display compositor's output is double-buffered.

## Reference

1. [Life of a Pixel](http://bit.ly/lifeofapixel)