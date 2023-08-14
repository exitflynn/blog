+++
title = "Docs Strange and Cleaning Up Code"
date = "2023-08-12T05:33:30"
tags = ["gsoc"]
+++

Since the last post, I worked on the moving-the-code task. Me, Nabil and Alasdair got on a call to discuss what should go where and I ended up creating a `scraper_utils.py` file to complement the `scraper.py` file. The other options were moving the functions to `.util.net` or inside the `scraper.py` file but outside the `Scraper` class. After I moved the tests as well, I wrote new tests, increased test coverage and renamed some functions which were previously named in the JavaScript-style CamelCase.
I also added and extended doc-strings to some functions which could use some updating and made fixes as asked in Code-Reviews.