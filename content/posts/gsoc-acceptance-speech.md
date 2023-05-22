+++
title = "Getting Selected As A GSoC student at SunPy"
date = "2023-05-21T11:58:10"
tags = ["gsoc", "WIP"]
+++

# What is The Google Summer of Code?
It is a program by Google for promoting students to contribute to open-source communities.
It is also a  G R E A T  opportunity for us to work on projects with such big impact. I'm so glad I came to know about it. I loved that I could get this opportunity to work with people involved in developing software that is used by many.
Now one could argue that that's the opportunity Open-Source provides in general, which is true. It's difficult to admit but I did need that initial push, some motivating factor to *truly* make the effort and get my hands wet with contributing to an open-source project. However it's as they say, the beginning's the hardest part and that once you start contributing it becomes so much more easy to keep going. I think they've done a great job at incentivising Open-Source contributions.

# Choosing an Org
A lot of Organisations participate in GSoC. I had worked on a couple of #good-first-issues on a few projects before this period and had achieved that level of confidence where the language / tech-stack wasn't the deciding factor for me anymore but it was now the project idea / the community.
I __love__ Astronomy and feel quite strongly about it. I am super-curious about how I can use my skills / to-be skills in tech in the field of Astronomy, some sort of an intersection of the two would actually be one of the most meaningful and enjoyable work I could find for myself.
Naturally, I looked into **OpenAstronomy** as a GSoC org. 
I've seen a lot of people prioritising those organisations which make for nicer addition to a resume from a product-based tech companies pov and it indeed *is* a very smart way to go about it to be honest but I decided that I didn't want to miss this unique opportunity to work on something I had wanted to do since a long time.
Under the sub-orgs for OpenAstronomy, I found **SunPy**. 

# First Experience with SunPy
Now, SunPy is a python library relating to solar physics. Though heliophysics wasn't exactly something that I thought of when thinking of astrophysics or space-stuff, I was *really* impressed by how active and supportive the community is. Besides, I found myself interested in the networking side of things more than the solar-physics stuff anyways as for now.

While first going through the issues I came across one which included [detecting gzipped files from more than just the extension](https://github.com/sunpy/sunpy/issues/6692) (via file-signatures / magic-bytes). I love when my familairity with using linux-based systems comes into play during development and I felt the issue to be quite approachable. During the PR Review, I remember going through the documentation for python's `gzip` module to verify that it doesn't need to decompress a whole gzipped file and that we could decompress just enough to get the first couple of bits which include the file-signature (since otherwise it'd mean way too much overhead for the solution to be practical) and information on that is *not* as easily available on the web as I'd like.

# Subsequent Experience
I kept on making code-contributions which I felt approachable for some parts of January and February. And then I got a bit busy due to some health-reasons and college and organsing OSDHack '23 (I'll totally write a blog-post on that later, it was such a nice success). I was a bit apprehensive because I was also looking forward to participate in the Google Summer of Code as a SunPy contributor and I didn't want to give off the wrong impression so I wrote a (not)brief text to [Nabil](https://github.com/Nabobalis) who was super-supportive. When the proposal submission period came around, it wasn't the most ideal process since I ended up having to make the proposal in the last 2 days. Thankfully again, the community is super responsive and I reached out to both Nabil and [Will](https://github.com/wtbarnes) for some feedback on my proposal and they both replied immediately.

# Contributing to Open-Source is GOATed
People in tech / software development should actually be talking about this so much more than they are. Some of the things that come to my mind right now are:

## It's a great way to learn stuff
One of the main factors why I think people have a hard time contributing is that they think "I can't contribute here because I don't know nearly enough tech to do stuff". The thing with being involved in this form of software development is that it gets you used to learning on the fly which is central to software development and doing well in any field, tbh.

Contributing to big open-source projects helped me gained TREMENDOUS self-confidence. I could look at any issue on any repo now, and never think that it's un-doable. No matter what domain or what language the project is in, it's only a question of how much time it'd take to get through the relevant documentation and concepts. This is certainly a long way from "I can't do $#!+ in this codebase if my life depended on it, it scares me" and I'm very much glad I could have this experience.

## The best practices ever
You get to learn how to write well-tested, well-documented and maintainable Production-Ready code following the best software development practices.

## You get to choose what you *want* to work on
YEP! And most of the communities would love to have you work on it, that's such an exciting thing if you ask me.

(this post is a bit rough around the edges, I definitely want to add stuff to it but owing to my exams right now I'll get to it at some later point of time, thanks for reading!)