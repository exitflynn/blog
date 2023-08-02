+++
title = "Across the Summer of Code"
date = "2023-07-29T05:33:30"
tags = ["gsoc"]
+++

I spent the most part of my time since last blog-post working on the failing tests.
The most complex client to fix and the most complex client in general, the GOES Client, needed to be changed a bit. The problem that arose there was mainly because of how we earlier had the liberty of parsing out any variable given down in the pattern since that string would never be used to act as a url but since we also want it to do the job of the baseurl, we can't have any to-be-determined value in the base parts. Since the client involved calling a helper function that returned the data for specific values of those variables, I had to modify the code to obtain those values from that part instead of extracting it again from the URL (since we put it there in the URL string in the first place). 
Another issue was implementing a system for dealing with cases where there's no numerical representation for the month in the file's URL, i.e. in the `%B` or `%b` datetime formats cases.
Aside from that, I added tests to cover the newer parts of the codebase that I added.
I also added documentation about writing the new patterns.

I made changes as per the PR reviews and now, finally, with all tests (remote or otherwise) passing, and the documentation updated, the scraper rewrite draft PR is ready for review!

I had a meeting with Nabil to discuss what I have to get done where I also showed him one of my fav pasttime activities, browsing open cameras using Shodan and also discussed about some general career advice.

While everyone has a look at the changes, Nabil guided me about moving on to "moving-the-code" part of the project now, which I'll be doing by branching off from my current branch, the scraper rewrite one, and then making a PR to it since it builds on top of those changes and it'll take a while and some reviews for it to merge into main. This way the mentors can review the PR and suggest changes while I work on it.