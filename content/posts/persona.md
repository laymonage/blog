+++
title = "Guide your interface design with persona"
date = 2020-05-13T22:28:19+07:00
tags = ["ppl", "ui/ux", "persona"]
toc = false
comments = true
draft = true
description = """
Interface design can be tricky to get right.
When in doubt, refer to your persona.
"""
images = ["/images/uploads/acyl.png"]
+++

{{< figure src="/images/uploads/acyl.png"
height="640px"
caption="\"Any customer can have a car painted any color that he wants so long as it is black.\" — Henry Ford"
attr="(Pantone Canvas)"
attrlink="http://canvas.pantone.com/gallery/758569/Decate-poster" >}}

When designing **user interfaces**, it can be tricky to get them right.
Beautiful user interface does not equal to good **user experience**.
They're two different things. The former might be easier to achieve,
but it's useless without the latter. A beautiful UI that's frustating
to use is no good at all.

Remember [**persona**][persona]? Yup. If you've created personas that
represent your users very well, that can be your guidance in designing
user interfaces. In this post, I'm going to show how to design user
interfaces according to the some given personas.

Consider the following persona.

{{< figure src="/images/uploads/persona-1.png"
caption="Talent seeker persona" >}}

She's a human resources staff at some company and she's struggling to
find candidates to work for her company. So, we figured it's a good
idea if she can see the reviews for her company and see what can be
improved to attract more candidates. Here's our design for her needs:

{{< figure src="/images/uploads/company-profile.png"
caption="Company profile" >}}

From a quick glance, she can see the summary of how her company is
perceived. By clicking "See more reviews", she can see all the reviews
of her company easily:

{{< figure src="/images/uploads/company-reviews.png"
caption="Company reviews" >}}

Now, consider the following persona.

{{< figure src="/images/uploads/persona-2.png"
caption="Job seeker persona" >}}

He's an economics graduate who's struggling to find a job that meets
his qualification. We need to find a way that makes him easier to
find the right jobs. So, we designed an interface to search for jobs
with specific criterias:

{{< figure src="/images/uploads/job-filtering.png"
caption="Job filtering" >}}

Once he has set the filters, he can see all jobs that match the filter.
He can also toggle the jobs' Type and Category from the same page so that
he doesn't have to go to the filters page just to change them.

{{< figure src="/images/uploads/job-filtered.png"
caption="Filtered jobs" >}}

As you can see, personas can guide us in designing user interfaces.
You shouldn't only aim to create beautiful UIs that work. You should
also **consider your users**, illustrated by your personas.

[persona]: https://en.wikipedia.org/wiki/Persona_(user_experience)
