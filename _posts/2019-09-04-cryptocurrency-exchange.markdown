---
layout: page
permalink: projects/:title
title: Cryptocurrency Exchange Website
hidden: true # hide from pagination
tech: 
  - JavaScript
  - Serverside rendering (SSR)
  - CCS Animation
image: /assets/images/projects/tomasz-frankowski-kBUfvkbFIoE-unsplash.jpg
headerImage: true
author: pete
description: A cryptocurrency approached one of my clients for a rebrand and rebuild of their website to attract new audiences. Given some strict requirements for technology and approach made for a challenging and fun project with many new things learned along the way.
category: project
---

# Cryptocurrency Exchange Website

My client was approached to facilitate a rebrand and update the website for a cryptocurrency exchange. Their current focus is on professional clients. I was brought in to implement the new design with an emphasis on motion and transition while adhering to low carbon emission and responsible web development principles.

[Photo by Tomasz Frankowski on Unsplash](https://unsplash.com/photos/kBUfvkbFIoE)

## Requirements

Being a cryptocurrency exchange, security was of utmost concern to the client. They placed restrictions on the technology we were able to use and the approach we should take. This aligned with the internal team's ability to review and audit the codebase along with use of standard metrics and error capturing tools.

To avoid introducing dependencies on too much third party code we avoided libraries where possible, instead choosing to implement lightweight alternatives which were easier to audit and maintain.

In order to provide a top quality and fast service from the site we elected to use a static site, only small amounts of the site are dynamically populated and have established APIs around them.

Node was expressed as a preference in early planning meetings, so it was the clear choice for rendering and delivering the static pages.

## Methodology

Because the site was designed to be composed of modular blocks, I chose to use React with Serverside Rendering (SSR). This meant that if in future we wished for a fully interactive site then hydrating the application on the client side would be possible and not require rebuilding all the application code as a duplicate.

Initially any custsom JavaScript behaviour in the browser was written in plain JavaScript and injected into the page as a bundle. These scripts remained reasonably small as CSS transitions and animations were used to take advantage of hardware acceleration and the reduced CPU load per visitor.

## Results

*n.b. photography and text have been replaced with generic alternatives here, so please excuse any poor image selection!*

The site contains some novel features using CSS animation and interactivity without drowning out the content.

Here is a small selection of some elements from the site.

A CSS-animated carousel with JavaScript triggers on the navigation arrows and blobs. It uses an overflowed container to allow the elements to be moved using a transition and `translateX` which is calculated from the available slides. The same system is used for the text.

![Carousel powered by CSS animations with a simple JavaScript trigger to switch classes](/assets/images/projects/carousel-screenshot.png)

[Photo by Duy Pham on Unsplash](https://unsplash.com/photos/Cecb0_8Hx-o)

A "meet the team" section, designed to be fully responsive and accept any number of team members. Each has a personalised bio revealed on click or tap, with a hover effect to remove the blue hue.

![Team element with responsive grid](/assets/images/projects/team.png)

[Images from https://randomuser.me/](https://randomuser.me/)

This is all very nice but doesn't reveal the motion of the elements, here is a screen recording of the create your account section. It's a series of animations tied together by a delay so they can all be triggered by adding a single class to the component.

Please excuse the autoplay video, the gif was too low framerate to show it off.

<video autoplay muted loop src="/assets/images/projects/create-account.webm"></video>

The project has certainly been a learning experience, CSS animations were something I used to avoid but they are very accessible and with a bit of thought behind them can have a big impact.
