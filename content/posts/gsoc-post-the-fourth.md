+++
title = "Wild Wild Tests"
date = "2023-07-14T05:33:30"
tags = ["gsoc"]
+++

# Fool-Proofing the Rewrite
After the last post, I mostly kept working on fixing the other failing tests and rewriting the tests to go with the newer pattern.

# Not All Failing Tests are Built Same
I managed to take care of them in a couple of days, except the `sunpy/net/tests/test_scraper.py::testFilesRange_sameDirectory_local` which proved to be a tough one to figure out. After discussing it a bit, I was able to figure it out, the error was caused because of a very unique flow of things which I think would be interesting to mention. I found out the test was failing because in the `_localfilelist()` function, we update the pattern class variable, call different functions on it and then fix it back at the end. Though once I realised this, I was able to spot that I needed to update the second pattern as well in a similar way and I discussed different approaches to do this with the mentors but the flow in the function was a very fun and intuitive way to do things that stood out to me for reason and I just wanted to make a note of it to look back on :P.

I also discussed my doubt relating to trying to shorten parse patterns by avoiding repetitions somehow but we came to the conclusion that we don't really need to do that, if a pattern has repetitions in it then the user's got to repeat stuff!

# Was that it?
Now once all the scraper tests were passing, I thought the PR should be ready for review. I informed my mentors for the same. The PR received some suggestions, which I discussed and implemented according to the code review.

# The Plot Twist
For a few days, I remained under the impression that I had mostly rewritten the Scraper in a way that it works and only have to wait for suggestions from the code review. Nabil, Alasdair and I discussed that in the meanwhile I could look into which functions I can remove and move out of the Scraper. However, a plot twist unlike any other was still awaiting me and the realisation came when Nabil asked me if I had run the remote-tests yet. I had been running the `test-scraper.py` tests so far, even the remote ones so my response was a vehement yes. When he mentioned that some were still failing, I remembered I was still to fix the examples in the documentation and once I had fixed the doctests it'd be passing. However at this point I was beginning to see the *real* issue, so far I had only been fixing the tests limited to the scraper and NOT the rest of the codebase. 

# A Whole New World
I ran the tests to be greeted with very polite `105 failing tests`. I informed this to my mentors and have begun working on fixing all the parts of the codebases which indirectly depend on this class. So far I've been encountering functions that may / may not have possibly gone redundant and am now exploring and considering which reducing functions, or removing them while fixing tests.

# Next Steps
I'll keep on working on these new tests, while analysing both the scraper and related parts of the codebase outside for functions to rewrite, remove and/or move at the same time.
I also realised we'll be needing new documentation about how to write the new parse-style patterns.

That is all so far, until next time!