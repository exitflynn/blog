+++
title = "Scraper Things"
date = "2023-06-30T01:33:30"
tags = ["gsoc"]
+++

Most of the week was spent rewriting the Scraper functions to go with the new parse-pattern and then looking for edge cases in the new implementations, fixing bugs and updating tests.

# {{Catching up}}
In the scraper, while trying to merge `baseurl` (an strftime formatted pattern i.e.` %Y%m` etc) and `pattern` (parse formatted pattern i.e. `{year:4d}{month:2d}` etc), I faced two choices, we could either:

- Make the input all parse-style formatted. generate the datetime-compatible string from here wherever it is required, however the problem i was running into here is parse-stuff like `{year:2d}` will collide with `{instrument}` like placeholders on which we mean to call `.format(**kwargs)` on. All the ways I could think of pulling it off included adding a large no. of lines of code in the really early part of `__init__()`.

- Make the input all datetime formatted and generate the parse pattern from here. The problem with this is there are edge-cases when sometimes users define variables like `{Level:1d}` which we have no way of knowing beforehand. One way to go about this could've been that we tell the user to define a pattern string in addition to baseurl whenever they have any new variables like that.

We ended up going with the first. My mentor, [Nabil](https://github.com/Nabobalis) introduced me to how there's a prevalence of using double-curly brackets in places where we need to escape / use them with single curly brackets, and sure enough, the `parse` module supports that. So that took care of the problem there, I was mostly a bit concerned that I'll have to go back to the second way even though I had started with it before pivoting to the first one.

# Communication
Nabil pointed out that we weren't being very good at communicating and that has a negative effect on the project which lead me to change how I was approaching it.

A lot of times I was debugging things by including print statements here and there, and since my project now involves changing API it had me updating tests and a lot of these times the problem would just be in my understanding on how to convert the patterns and just how the new input should look like. Previously, I would only send a message on the matrix room when I had a question. And most of the times I'd still hold out hope for solving something by myself when I have the strength to pick it up later. But it also makes sense because there'd be no way to differentiate that from me not doing anything. Also around 75% of the time I've drafted out questions, I found out an answer along the way. From now on I realise instead of just asking questions, I should think of it as a logging / progress-updating activity first which sounds obvious in hindsight.

# To regex or not to regex
The next part where I was stuck at was in converting a pattern that involved custom placeholders like `{{CAR_ROT:4d}}` or `{{:3d}}` to their regex counterparts like  `(\d){3}`, `(\d){16}` etc without using like a l o t of regex, any less complex method than using a conversion function etc. However, after a quick conversation with the mentors and I realised that I had been mistakenly assuming that strftime required regex patterns. Earlier I thought that regex would no longer be part of the end-user experience but still somehow exist in the codebase but this is when I realised that we can entirely do away with it.

# Rewrite then Remove? 💀
I also realised that the `_extractDateURL()` function was made redundant _after_ rewriting it since I found out later that it was only called at one place and that part of the code was no longer required thanks to the existence of a parse-pattern. That's a nice message to keep in mind for the future.

# Modern Day Cain
Another question I encountered was if we could assume that any {year:2d} or %y type 2-digit year xx to be interpreted to be in the 21st century like 20xx or not.

# Issue found
I also found this bug in the codebase:
In the current `scraper._check_timerange()`, it takes the simpler way `if` we provide it with an extractor // parse-pattern and a more complex way if we don't, however as it is implemented right now

```python
from sunpy.net.scraper import Scraper
from sunpy.time import TimeRange
s = Scraper('%Y.fits')
s._check_timerange('2014.fits', TimeRange("2015-01-01", "2015-01-02"))
```

would return True and it is intended that way in the tests but if we passed it an extractor // parse pattern

```python
s.extractor = "{year:4d}.fits"
s._check_timerange('2014.fits', TimeRange("2015-01-01", "2015-01-02"))
```
it'd return False.

# Inclusivity is important
Failing tests required me to inqure if we want closed-intervals in the package or open, which concluded with closed. I also found other instances in the codebase where we endorse closed intervals. 

# Future steps
Cover why the rest of tests are breaking and fix em.
Ask for review once everything works.
Try to come up with a way to write parse-patterns so as the length remains less by trying to minimize repeated values.
Also fix my Hugo setup before the night -_-