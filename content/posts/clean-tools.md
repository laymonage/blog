+++
title = "Code cleanly by knowing your tools"
date = 2020-04-16T02:03:09+07:00
tags = ["ppl", "nodejs"]
toc = false
comments = true
draft = false
description = """
Some tools provide multiple ways to achieve the same goal.
However, one way may be cleaner than the others.
"""
images = ["/images/uploads/ashpd-blueprint.jpg"]
+++

{{< figure src="/images/uploads/ashpd-blueprint.jpg"
caption="Blueprint of a very sophisticated device."
attr="(Half-Life Wiki)"
attrlink="https://half-life.fandom.com/wiki/Aperture_Science_Handheld_Portal_Device" >}}

When building a software, it's always a relief to see that someone has made a
**tool** to accomplish some of your use cases. Rather than spending your time
writing something that's not the main goal of your project, you can just add
(yet another) dependency. Of course, in some cases, it may be better to write
your own solution and keep your dependencies to a minimum. In other cases,
especially for complex stuff like an HTTP client or an ORM, it makes more
sense to use a **third-party package** that's already battle-tested.

Some tools provide multiple ways to use them in order to accomplish a certain
goal. In the end, they achieve the same thing. However, one way may be better
– **or at least cleaner** – than the others.

In my software engineering project course, we had to build a web service that
calls two other services: one internal (database) service, and an external web
service. This service – we call the gateway – is called by the frontend. We're
using [**`axios`**][axios] as our HTTP client for NodeJS both in the gateway
and browser.

`axios` provides multiple ways to make HTTP requests. For example, to make a
`POST` request, you can do it like this:

```js
axios({
  method: 'post',
  url: 'https://some.web/v1/user/123',
  data: {
    firstName: 'James',
    lastName: 'McGill'
  }
});
```

or this:

```js
axios.post('https://some.web/v1/user/123', {
  firstName: 'James',
  lastName: 'McGill'
});
```

As you can see, the `.post` request method alias is more convenient. It's easier
to read. It's more concise. **It's cleaner.**

However, if we didn't **know** about it, we might end up using the more verbose
API. Thankfully, the `axios` documentation uses the `.post` method alias in the
usage examples. It's introduced *before* the verbose one, so you can't miss it.

Now, let's go back to our project. With the given setup, we have to define
several requests to each connected service. It's not a big deal, we can just
do something like:

```js
function saveUser(userId, data) {
  return axios.post(`https://database.service/v1/user/${userId}`, data);
};

function getUser(userId) {
  return axios.get(`https://database.service/v1/user/${userId}`);
}

function getJobListings() {
  return axios.get('https://external.service/v1/job-listing');
}

function getJobListingDetail(jobListingId) {
  return axios.get(`https://external.service/v1/job-listing/${jobListingId}`);
}
```

Do you notice something off? We keep writing `https://database.service/v1` and
`https://external.service/v1` every time we define a request. Let's say we want
to upgrade and use database service `v2`. We would have to change **every**
request definition. Well, no big deal, just use a variable:

```js
const BASE_URL = {
  DB_SERVICE: 'https://database.service/v2',
  EXT_SERVICE: 'https://external.service/v1'
}

// ...

function getUser(userId) {
  return axios.get(`${BASE_URL.DB_SERVICE}/user/${userId}`);
}

function getJobListings() {
  return axios.get(`${BASE_URL.EXT_SERVICE}/job-listing`);
}

// ...
```

That looks much better now. However, we still need to write
`${BASE_URL.DB_SERVICE}` and `${BASE_URL.EXT_SERVICE}` for **every** request
definition. While we can do something like this:

```js
const services = {
  db: {
    get: (path) => axios.get(`${BASE_URL.DB_SERVICE}${path}`),
    post: (path, data) => axios.post(`${BASE_URL.DB_SERVICE}${path}`, data),
  },
  ext: {
    get: (path) => axios.get(`${BASE_URL.EXT_SERVICE}${path}`),
    post: (path, data) => axios.post(`${BASE_URL.EXT_SERVICE}${path}`, data),
  }
}

function saveUser(userId, data) {
  return services.db.post(userId, data);
}

// ...
```

It's not ideal as we have to repeat the service definition for each request
method and service. It also limits the way you use `axios` as it's encapsulated
in the arrow functions. We need to find another way.

If we **read** through the **documentation**, there is a section about
[creating an `axios` instance][axios-instance]. Basically, you can create an
axios instance with a custom configuration, including **`baseURL`**. You can
use the instance to call methods like `.get` and `.post` with the specified
config.

So, by using `axios` instances, we can do something like this:

```js
const DBService = axios.create({ baseURL: 'https://database.service/v1' });
const ExternalService = axios.create({ baseURL: 'https://external.service/v1' });

function saveUser(userId, data) {
  return DBService.post(`/user/${userId}`, data);
};

function getUser(userId) {
  return DBService.get(`/user/${userId}`);
}

function getJobListings() {
  return ExternalService.get('/job-listing');
}

function getJobListingDetail(jobListingId) {
  return ExternalService.get(`/job-listing/${jobListingId}`);
}
```

Great! The code is much cleaner now. As a bonus, it also reads nicely:\
`<service-name>.<request-method>(<path>)`

To take it a step further, I'd **separate** the `axios` instances in its own
file (e.g. `AxiosService.js`) and use **environment variables** to specify the
base URLs like so:

```js
const { DB_SERVICE_BASE_URL, EXT_SERVICE_BASE_URL } = process.env;

module.exports = {
  DBService: axios.create({ baseURL: DB_SERVICE_BASE_URL }),
  ExternalService: axios.create({ baseURL: EXT_SERVICE_BASE_URL })
};
```

Thus, you can easily change the base URL for different environments
without changing the code at all.

Nowadays, tools and frameworks that are mature allow you to write clean code
**if you know *how*** to make the most of them. As you can see, we're able to
write cleaner code after spending some time to read the docs and
**learn more about our tools**. Linters and
[static code analysis][static-analysis] tools will definitely help as well.
Knowledge of tools **isn't** the only *tool* <sup><sub>(heh)</sub></sup> that
helps you write clean code, though. If you spend enough time coding, you'll
develop your own **intuitions** and clean code just comes **naturally**.

Still, to make the most of your tools, please...

> **read the docs!**

[axios]: https://github.com/axios/axios
[axios-instance]: https://github.com/axios/axios/blob/master/README.md#creating-an-instance
[static-analysis]: https://en.wikipedia.org/wiki/Static_program_analysis
