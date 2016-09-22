---
layout:     post
title:      "Error installing nokogiri - Fastlane"
subtitle:   "Solution? 3 easy steps"
date:       2016-11-22 06:20:00
author:     "Billy"
header-img: ""
---
### Problem
A couple of days ago I was about to install fastlane on my laptop to start automate my app store deploys.
It didn't take long before I was hit with the first error:

"ERROR:  Error installing nokogiri:
ERROR: Failed to build gem native extension."

### Solution
I solved this by with the following 3 steps:
* xcode-select --install
* sudo gem install nokogiri -v '1.6.8' -- --use-system-libraries=true --with-xml2-include=/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.11.sdk/usr/include/libxml2
* sudo gem install fastlane --verbose
