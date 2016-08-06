---
layout: post
title: Scientific Experiments
date: 2016-08-05 16:28:13
description: Evaluate and analyse new code paths without interfering with your users' experience.
---

Back in February, [GitHub officialy released](http://githubengineering.com/scientist/) Scientist. [Scientist](https://github.com/github/scientist) is a Ruby library for carefully refactoring critical paths.

Their blog post explains very well what the purpose of Scientist is, but here is a quick recap.

#### Problem

You have a piece of code which isn't performing well enough and it's pretty obvious that you need to refactor this. But, this is a critical piece of code for your business. Making a potential change which potentially leads to different results could mean horror for your business. This is the main reason why this piece of legacy code is still around.

Tests will only get you as far as building confidence that the new piece of code does what you want it to do with the tests that you write. Real world usage however is always a little bit different, and there will be edge cases on which you didn't count and haven't tested for.

#### Solution

This is where Scientist comes to play. With Scientist, you can write your new code path alongside of your old code path. Both codepaths will then get executed and the outcome - the actual value as well as the run duration - of these tests can then be compared. This enables you to see how correct and performant your new code path actually is.

To not influence your users, Scientist will _always_ return the output of the `control` - your old code path - to the user. This means that when there is an issue with the new code path, the user would never notice, but you would still get notified.

This is great as it means that you can test your newly refactored code with real data and get meaningful feedback. This allows you to tweak where needed before actually releasing it officially.

### Experiment

This is all great when you're writing Ruby applicaitons. But, unlike a year and a half ago where I was actually writing Ruby applications, I am not anymore. I am currently doing the majority of my work in [Go](https://golang.org/).

About 4 months ago, I started porting [GitHub's Scientist](https://github.com/github/scientist) to Go. The main reason behind this was learn some new concepts and have a little side project. I wanted to have some metrics for a test that I ran in a development environment so I put together the initial version of [Experiment](https://github.com/jelmersnoeck/experiment).

Now, after reworking the interfaces, doing some optimisations and having it running in production for a while, I'm happy to announce the [first official release](https://github.com/jelmersnoeck/experiment/releases/tag/v1.0.0).

#### Caveats

As with Scientist, there are some caveats for using this package.

**Stateless**

Experiment should only be used in a stateless environment. This means that actions with side-effects could cause unwanted behaviour. Imagine updating a database twice, this would result your data being in a bad state.

**Concurrent access to the context**

To run an experiment, you need to pass in a `context.Context` type (which can be `nil`). This could be used to pass along data. It is important to note that within your test functions, you should not update the values within your context. An example is given [here](https://github.com/jelmersnoeck/experiment#concurrent-access-to-context).

**Performance**

Although all the experiments run at the same time (with goroutines), it could be that new tests introduce a performance degradation. New tests should be rolled out slowly and monitored closely. Using the `Config` `Percentage` option is a good first step for this.
