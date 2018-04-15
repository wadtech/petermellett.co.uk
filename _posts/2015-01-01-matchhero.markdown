---
layout: post
permalink: projects/:title
title: MatchHero Cycling
hidden: true # hide from pagination
tech: 
  - Laravel
  - Geocoding
  - Maps
  - Strava
image: /assets/images/projects/matchhero-banner.jpg
headerImage: true
author: pete
description: MatchHero is a network of apps promoting participation in sport for all ages.
category: project
---

I was tasked to build a prototype for a social cycling application, used to advertise and respond to people looking for riding partners or clubs in their local area.

In order to facilitate rapid development the [Laravel PHP framework](https://laravel.com) was utilised and the core of the application based around affordable and powerful third-party APIs.

![MatchHero Find a Ride interface](/assets/images/projects/matchhero-rides.png){: .centered-image}

In order to give the best coverage for signups, the [Strava](https://strava.com) API was used to build in social sign-up and login with ready-populated profile data including avatars, unit preferences and names.

![MatchHero Profile snippet](/assets/images/projects/matchhero-profile.png){: .centered-image}

Using the Strava API allows for future expansion of the platform to include more functionality based upon the data provided. It also provides access to the same privacy restrictions as the user would use with Strava, which we can then honour in the MatchHero application.

To build out the 'post a ride' and 'find a ride' features, the [Google Maps API](https://developers.google.com/maps/) was used. Allowing a user to select their location by latitude and longitude, if known, or a street address if not using [Geocoding](https://developers.google.com/maps/documentation/geocoding/intro) services.

During the build phase we used a round of wireframe and whiteboard sessions to target the desired functionality and MVP features that we thought would be appropriate for the platform.

As keen cyclists in the team at the time, we all had a say in the design and initial functionality- and also a keen eye for what wasn't working after launch. Some adjustments were made post-launch to improve the user experience and provide a solid application to extend and promote in the community.

Using a prototype and review process I put together the initial release in around two weeks of development time.

If you're interested in the more social side of cycling then visit [MatchHero Cycling Club](https://matchhero.cc).
