---
published: true
layout: post
title: 'An APIs.io Prototype'
image: /assets/images/blog/apis-io-prototype.png
author:
    name: kinlane
tags:
- API
- Search
---
I wanted to develop a prototype of the APIs.io search that brought the API search engine back to life using the APIs.json format. However, in the same motion I wanted to address two shortcomings that we never got around to developing before: 1) having it actually pull and update the APIs.json file, and 2) providing a rating system for the APIs that would help us filter out the garbage and identify the high quality APIs. 

To help me accomplish what I wanted I developed nine individual APIs that provide the state machine for the APIs.io search engine:

- **Search API** - Allows you to submit APIs.json, and search APIs, properties, maintainers, and tags.
- **Engine API** - Pulls the APIs.json, and the properties of APIs on weekly schedule.
- **Ratings API** - Rates each APIs.json using a rules-driven approach to incentivizing high quality APIs, APIs.json, and OpenAPI indexes.
- **Rules API** - Spectral rules that target specific capabilities of APIs.json and OpenAPI files for use in rating.
- **Linting API** - Am API version of Spectral, retrofitted to lint not just OpenAPI, but also APIs.json.
- **Publishing API** - Handles publishing APIs, rules, properties, maintainers, and tags, to the static site behind the search engine.
- **Properties API** - An API for managing the properties of APIs across all APIs.
- **Maintainers API** - An API for managing the maintainers of APIs across all APIs.
- **Tags API** - An API for managing the tags of APIs across all APIs.

To demonstrate the rating system I had to apply the API rating to the APIs.io API, being the model API we would like to see in APIs being submitted to the search engine. The APIs.io Search Engine API has all of the properties like documentation, OpenAPI, plans, etc. that we want to see across all API providers, and I've published an APIs.json file to document it. 

Then I found some of the other APIs.json files for providers that still exist out there. I will continue finding more, and also publish a bunch that I have created, but I wanted to get started with just a few--then push the engine and rating system by adding more. I can submit new APIs.json using the Search API, then it pulls the APIs.json using the Engine API, then rates the APIs.json using the Ratings API which uses the Rules API and Ling API to make the magic happen. Then using the publishing API it publishes APIs to the web site.

There are two web sites, [the search engine](https://apis.io/) and the [developer portal for the API](https://developer.apis.io/), which has the APIs.json for APIs.io and all the supporting properties. While the website has state page for all APIs, the search is dynamic through the Search API, returning results with each key stroke -- I will keep working on this user experience. I was able to achieve an API search using the APIs.json for APIs.io, as well as the handful of other providers that I have included in the index--next, I will pull APIs.json from my archives and begin submitting to see what quality I endue with.

The details of each API are pretty messy coming in, and I added the properties, maintainers, and tags APIs to begin getting a handle on these elements -- while you will want to keep things as they were submitted by the APIs.json author, there will be things you want to translate, evolve, and change as things come in. I added these three layers for managing these three key dimensions of the index, but then also make it easy to publish them as part of the web site browsing and search.

There is a lot of work still needed, but I was able to prove out the rating system, which I feel was the toughest portion. I'd like to add more rules, adjust the point system, and get more sophisticated about the algorithm in how they are applied. This is the most important layer I feel like because it will help keep the cream floating to the top, but also provide a transparent incentive model for others to follow if they want their API rising to the top.

I'm going to step back for a week and just gather things that need to be done as issues, while I think about the big picture some more. I was able to prove the rule system works, and I'll keep load testing the concept with more APIs.json files, but also adding the layer for OpenAPI and other machine readable properties -- which is where the holy grail on this will be I think. I am excited about the potential of this for small and medium scale API search engines. I just don't think 10K plus range is what is needed -- I feel like you can have hundreds or millions in the index, but only the cream should rise to the top when it comes to a published search engine.