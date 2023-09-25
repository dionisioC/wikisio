---
title: Rest API with express
date: "20230925"
author: Dionisio
---

# Basics

In order to get a `NodeJs` project up and running we will use the `npm`
command.
`Npm` stands for node package manager, so we need node installed.
`Nvm` (node version manager) is recommended to manage the `NodeJs` versions.
We will type in the terminal:

```shell
npm init
```

And it will guide us to create the project. We can put a name to out project
and accept the defaults.

After the command is complete, we will have a `package.json` file. Inside that file
inside the scrips object, we are going to add the following:

```json
"start": "node index.js"
```

So the scripts section of the `package.json` will look like this:

```text
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "node index.js"
  }
```

We are doing this because the default main file is called index.js
(you can check it) in the main property of the `package.json` file.

Other thing we have to do is create the `index.js` file. We can do it with

```shell
touch index.js
```

for example.

# Express

Now we need `express`. We will install it with

```shell
npm install --save express
```

A new file `package-lock.json` will appear containing all the metadata as
express has some dependencies.

Let think we are making a brain. In order to test that everything is up
and running we can add the following to the previously created `index.js`.

```js
const express = require('express')
const app = express()
const port = 3000

app.get('/', (req, res) => {
    res.send('Your thought sir');
})

app.listen(port, () => console.log(`Brain running on port ${port}`));
```

Now running

```shell
node index.js
```

We will see the message that we have in the `console.log` statement.
If we go to `http://localhost:3000/` we will see the message that
is returned in the get.

# Middlewares

Express is a simple HTTP server, and it does not come with a lot of features
out of the box, so we need to install some middlewares to get more features.
One of them is body-parser, which helps us decode the body from an HTTP
request. We install it with:

```shell
npm install --save body-parser
```

With this we can advance in the development of the rest API as we can
get the body of a post.

We are going to use an array as a database, in the future we will use a
real database

Our array will be this one:

```js
let thoughts = [
    {id: crypto.randomUUID(), content: 'Good thought'},
    {id: crypto.randomUUID(), content: 'Everything is fine'}]
```

We add the id, so we can retrieve them, because when you have bad thoughts
they usually come again and again.

To generate the id we have to add the following import:

```js
const crypto = require('crypto')
```

Now, we can return all the thoughts in the get endpoint.
Notice that the endpoint now has the `thoughts` path.

```js
app.get('/thoughts', (req, res) => {
    res.send(thoughts);
})
```

Before the get, we are going to config the body parser with this two lines:

```js
app.use(bodyParser.urlencoded({ extended: false }));
app.use(bodyParser.json());
```

When you submit form data with a POST request, that form data can be encoded
in many ways. The default type for HTML forms is application/x-www-urlencoded,
but you can also submit data as JSON or any other arbitrary format.

bodyParser.urlencoded() provides middleware for automatically parsing forms
with the content-type application/x-www-urlencoded and storing the result as
a dictionary (object) in req.body. The body-parser module also provides
middleware for parsing JSON, plain text, or just returning a raw Buffer object
for you to deal with as needed.

With the extended set to false the values can be an array or string.

```js
app.post('/thoughts', (req, res) => {
    const thoughtContent = req.body.content;
    const thought = {id: crypto.randomUUID(), content: thoughtContent}
    thoughts.push(thought);

    res.status(201).send(thought);
});
```

Sometimes, the thoughts leave our brain, so we will add a new endpoint to
delete them:

```js
app.delete('/thoughts/:id', (req, res) => {
    const id = req.params.id;

    for (const thought of thoughts) {
        if (thought.id === id) {
            const index = thoughts.indexOf(thought);
            thoughts.splice(index, 1);
            res.json(thought);
            return;
        }
    }

    res.status(404).send('Thought not found');

});
```

Other times, we just change our mind, so we update our thoughts. To do that
we are going to create an endpoint to update them:

```js
app.put('/thoughts/:id', (req, res) => {
    const id = req.params.id;

    for (const thought of thoughts) {
        if (thought.id === id) {
            const index = thoughts.indexOf(thought);
            thoughts.splice(index, 1);
            const updatedThought = {id: id, content: req.body.content}
            thoughts.push(updatedThought);
            res.json(updatedThought);
            return;
        }
    }

    res.status(404).send('Thought not found');

});
```

This is incomplete, things to improve

Put inside a docker
Make it modular, not just in one file
use nodemon
