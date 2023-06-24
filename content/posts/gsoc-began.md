+++
title = "Into the Summer of Code"
date = "2023-06-24T01:33:30"
tags = ["gsoc", "WIP"]
+++

I had my end-semester exams during the Community Bonding Period and the real-early part of the Coding Period. However, my mentors were super-accomodating.
I spent a few days on plans with friends before bidding goodbye for the summers, travelling home and relaxing a bit.

## The Talk
This was also the time when IIT BHU reached out to me for a talk as part of a collab between their Astronomy and Open-Source clubs about Astronomy in tech and OSS. This was a  g r e a t  experience! I've always wanted to improve at public-speaking stuff and to finally pull off a satisfactory talk was a great experience. Also Prayash was a great host.

## Beginning with the Project
My mentor, Nabil, had asked me to start by writing tests for the scraper, even if they may not return a -ve)for all the URLs it doesn't support now.
Looking closely though, all the issues were due to limitations of the way regex is implemented for inputting URLs, i.e. since one of the main goals of the project was to remove regex and use parse instead, these tests would have proven to be quite redundant. I asked my mentors and understandably so, Nabil said that he doesn't want me working on code that I might have to remove soon enough and said I can proceed to the rest of the rewrite.

After that, I began reading up on what Metaclasses, Abstract Base Classes (ABCs), etc are, their advantages and how they can be implemented in python to decide which would be better for the purpose of the project. However, at this point I wasn't really maintaining good communication with my mentors. When they asked me for any updates and then inquired about why I had been reading up on ABCs and such, they clarified that I should be able to improve the scraper without going that route.

Had to take a couple of days off in between for unavoidable reasons.

After that I took some calls with my mentors, clarifying details and trying to work things through together as I figured out what I should do next.
All the scraper // dataretriever clients require two strings, `baseurl` and `pattern` and I figure out a way to merge them somehow.
I looked into just what role the two of them had and found that `pattern` was used only to parse data.
For example, When writing scraper clients, we require a baseurl and a pattern, an example from the NOAA Client:
```python
baseurl = r'ftp://ftp.ngdc.noaa.gov/STP/swpc_products/daily_reports/solar_region_summaries/%Y/%m/%Y%m%dSRS.txt'
pattern = '{}/{year:4d}/{month:2d}/{year:4d}{month:2d}{day:2d}SRS.txt'
```

Instead of passing both, we should be able to merge them into just one since the pattern string is conveying information that is already available in the baseurl.

I figured it should be possible to transform `baseurl` to `pattern` and generate `pattern` that way but halfway through I realised that it'd not be possible. However we should be able to convert a full `pattern` to it's `baseurl` formatted counterpart.

I remember writing out loud what I thought as I approached that problem:

## rubber ducky microblogging?
now how do we include this transformation in our code?
we have mainly two places / files of concern.
`dataretriever/client.py` and `scraper.py`
at this point there are a couple of ways to go about it that come to my mind, all of them would however can be categorised as:
a) include the transformation in dr/client.py
b) include it in scraper.py

before i get ahead of myself, it's better to arrive at a concrete decision here, if that's possible, to avoid having too much of overhead.

case a:
pattern transforms in client and is then sent to scraper. this means the scraper still operates on strftime baseurl.
and when we want to call the parse function, instead of sending pattern as we do now, we can just send the original new format

case b:
pass the new format to scraper. scraper converts it into strftime to use in all of its functions, and 

the second approach makes more sense, yeah.


NOW
what'd be a nice way to incorporate this transform in the scraper file?

So there have to be Two strings/patterns, throughout this codebase.
the strftime kind and the parse kind.
what we input is the parse kind (since we can convert this to strftime, and the other way around wasn't possible)

how to incorporate both strings? make them both private members of the Scraper class?

some functions will be changed as a result of this

like _URL_followsPattern

okay so the plan of action is:
go add a transform function, call it in init
we'll have a variable for the time_pattern, update pattern -> time_pattern wherever applicable.
rewrite using parse wherever applicable

but this would take away from having a standard system and we'll be defining our own set of names to name variables as.


also a future plan can be to check for redundant functions / moving code.


## in conclusion
So as of right now, I've been working on [#7073](https://github.com/sunpy/sunpy/issues/7073) and [PR #7077](https://github.com/sunpy/sunpy/pull/7077), more details on this issue and my proposed solution can be found in the issue description.

This'll be all for now, will be posting more in the future.