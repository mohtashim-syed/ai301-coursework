I opened a fix for a bug in deepeval, an open-source library used to test AI systems. When an AI grader returned no results, the library could report a perfect score of 1.0 and give an explanation that hid what actually went wrong.

I found the issue using an AI-assisted search tool I built during CodePath's AI301 course. The tool scans GitHub issues and evaluates them against rules I designed. Along the way, I had to improve those rules after my tool recommended problems that had already been resolved.

Before writing the fix, I studied the existing code and tests. I discovered that the perfect score was intentional, so I focused on making the explanation accurate instead of changing the score.

I reproduced the problem, added tests, and opened a pull request. The person who originally reported the bug reviewed my work and agreed with the approach. The pull request is still open and awaiting maintainer review.

This experience taught me that reliable AI systems need more than passing scores. They also need explanations people can trust.

https://github.com/confident-ai/deepeval/pull/3634

#OpenSource #Python #AI #CodePath