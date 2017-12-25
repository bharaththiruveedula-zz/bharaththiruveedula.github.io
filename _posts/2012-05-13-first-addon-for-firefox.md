---
layout: post
author: Bharath Thiruveedula
title: First Addon for firefox
date: 2012-05-13 18:43:07 +0530
categories:
- firefox
- opensource
tags:
- firefox
- opensource
---


As I am a big fan of Mozilla firefox because of its addons, I want to develop an addon for firefox( though a simple one ). I started learning how to create an addon for firefox from the great[ mozilla developer site](https://developer.mozilla.org/en-US/addons), I then found three ways for creating an addon for firefox.

First one is the old manual way where you can create all the files and folders like chrome, browser.js, locale, chrome.manifest, install.rdf etc., I think though this method makes the developer aware of all the files he is developing to make an addon up and running but it takes a lot of time for writing the content which doesn't include the main logic of your addon.

Second is the creating an addon by using addon-sdk. It gaves you a lot of freedom for you to concentrate mainly on the code for functioning of your addon, rest of the things will take care by addon-sdk.

Third one is developing addon using addon-builder, it is lot easier than addon-sdk. You will code the addon on the cloud and you can test it with in a second and download the satisfied version of your addon. I want to develop an addon using addon builder and I want to develop an addon which will search in google for the highlighted text, and developed the addon and [submitted](https://addons.mozilla.org/en-US/firefox/addon/search-for-it/) to mozilla addons website. Right now it is under review and waiting for it to release officially.
