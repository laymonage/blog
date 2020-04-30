+++
title = "Is TDD worth it?"
date = 2020-03-11T23:52:31+07:00
tags = ["ppl", "testing"]
toc = true
comments = true
draft = false
description = """
TDD is a good discipline to follow.
Still, it's never a good idea to follow things blindly.
"""
images = ["/images/uploads/test.jpg"]
+++

{{< figure src="/images/uploads/test.jpg"
caption="Test results that make your heart race."
attr="(Deviantart: LuigiL)"
attrlink="https://www.deviantart.com/luigil/art/Worried-639678390" >}}

## Intro

[**Testing**][testing] is an important aspect in the software development
process. It gives you insights about the quality of your software. It also
helps you find bugs and verify that your software is working correctly. Testing
is so important that there exists a software development process that is based
around it, called [**test-driven development (TDD)**][tdd].

## TDD in short

In TDD, **tests are written first**. In fact, requirements are *translated*
into test cases. This makes the requirements become clearer and easier to
understand, as you'll be able to see what is expected from your software. The
test cases are specific enough that you can basically code the implementation
by reading them.

## TDD cycle

TDD is based around the following cycle:

1. Write a **test**\
   Each new feature begins with **writing a test**. The test **briefly**
   defines the requirements for a function.
2. Run all tests and see if the new one **fails**\
   This one is important as it makes sure that the test you just wrote isn't
   flawed and **cannot pass** without requiring **new code**.
3. Write the **implementation**\
   You need to write the **implementation** just *enough* so that the test
   passes. The code doesn't need to be perfect as it can be improved in the
   fifth step.
4. Run the tests again and see if the new one **passes**\
   If the new test **passes**, then you can be sure that the implementation
   meets the test requirements. Otherwise, you need to adjust the implementation
   until it passes the test.
5. **Refactor**\
   Since you wrote the implementation just *enough* to pass the test, there may
   be room for improvements. You can continue **improving** the code in this
   stage, but ideally, you shouldn't add new code that isn't related to the
   requirements specified in the first step. Don't forget to
   **re-run the tests** to make sure the refactoring doesn't alter any existing
   functionality.

That's it! **Rinse and repeat**.

## Is it ideal?

The idea behind TDD does **make sense**. It's supposed to **ease** the
development process by explicitly defining the requirements as tests. You don't
have to think too much about the business requirements. Just focus on passing
the tests (given the tests are *high quality*) and you should be good to go. As
a bonus, the tests refrain you from writing buggy code. After all, it's
**test-driven** development.

## What's the catch?

Applying TDD in your project requires you to understand
**how tests are designed**. Test designs can **vary** across different
languages, frameworks, design patterns, etc. Therefore, you should be familiar
with your development environment before you can apply TDD effectively.

Otherwise, you'll have a hard time figuring things out. Since you haven't
written the test, you cannot write the implementation. However, in order to
write the tests, you'll need to at least have an **idea** of your development
environment and the tools used. You'll end up spending a lot of time trying to
write the tests, and having much less time to develop your project.

## How did I know that?

Well, in my case, that's what happened in the first sprint of my Software
Engineering Project course. For the four of us, it's our first time in using
NodeJS to develop a backend server. We weren't familiar with **developing**
stuff using NodeJS, let alone **writing tests** for it. Yet, the course
requires us to apply TDD in our project. We ended up getting confused.
Development was *very* slow.

To overcome this difficulty, we ended up trying some **experiments**. We
tried developing some stuff separately from the project, **without** any tests.
Once we've **grasped** how things work, we started writing some tests.

Writing the tests wasn't easy either. We had to learn how to **mock** stuff, so
that we can write unit tests that are *isolated* between one function and
another. Thankfully, [Jest][jest] provides some useful features that can be
used to write mocks.

It took us some time to get used to the **test designs**. However, things are
much easier after that. Now that we're **familiar** with the test designs, we
can finally apply TDD in our project. When we encounter some new stuff that we
don't understand, we start a new **experiment**, and redo the process.

## So, is TDD worth it?

I'd say yes, **if** you're already familiar with your development environment.
Otherwise, I think sticking with the traditional way (i.e. write the
implementation first) is more ideal, **as long as you also write the tests**.
I don't think it's *ever* a good idea to skip tests.

TDD is a good discipline to follow.
**Still, it's never a good idea to follow things blindly.** TDD, as with
other things, **isn't** something that you should always follow
*no matter what*. When applying something doesn't work for your project, you
need to take a step back and **identify** if it may be a good time to stop
doing so. At least, until you figure things out.

[testing]: https://en.wikipedia.org/wiki/Software_testing
[tdd]: https://en.wikipedia.org/wiki/Test-driven_development
[jest]: https://jestjs.io
