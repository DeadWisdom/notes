You gotta ask the server to do something, so here you are making POST after POST, and maybe we should use PUT or PATCH, but what's the difference again?

It's important to get right, and even senior devs often have a hard time keeping it straight (or however you like to keep it). So let's jump in and talk about it with some simple diagrams.

Later on we'll get into how you can change your thinking from your HTTP server as a remote procedure call system to an actual REST API.

### TODO

- Querying / Collections

## PUT

So let's imagine our web server is a warehouse. We've got bins everywhere and each has a label.

[ ] [ ] [ ] [ ] [ ]

Now the cool thing about this warehouse is it makes bins, magically whenever you want. You just tell it: here put this  juice box (🧃) in a bin, and label it "/lunch/juice-box". So we say:

    PUT 🧃 into the [bin labeled "/lunch/beverage"]

Or since it's HTTP, we do:

    Request: PUT /lunch/beverage
    Body: {type: 🧃, flavor: "apple"}
    
    Response: 200 OK

One of the cool things about HTTP is we can do it over and over, we can PUT 🧃 into "/lunch/beverage" a million times, and nothing changes. We call this being **idempotent**.

## GET

So now we can get it. Pretty obvious:

    GET whatever is in the [bin labeled "/lunch/beverage"].

And we get back:

    🧃

But HTTP so:

    Request: PUT /lunch/beverage
    Body: Nothing, GET's shouldn't have bodies
    
    Response: 200 OK
    Response Body: {type: 🧃, flavor: "apple"}

One of the cool things about this is it's **cacheable**. If we have a truck that goes and gets the 🧃 for us, the second time we ask for it, it can just immediately give us 🧃 without having to make the whole trip across town. That's part of why we don't have a body in a GET, because we want to be able to cache based only on the label, the URL.

## PUT #2

So we want to level up our lunch, want to feel a bit more sophisticated, more like an adult. Let's replace our juice-box with green tea (🍵).

    PUT 🍵 into the [bin labeled "/lunch/beverage"]

HTTP detail:

    Request: PUT /lunch/beverage
    Body: {type: 🍵, type: "green"}
    
    Response: 200 OK

Now, *this is important*, when you PUT something into an existing bin, it *completely* replaces what was in there already. Nothing of our juice-box remains, not the flavor, not any of its metadata. The warehouse takes that bin, tosses it in the garbage, and puts only the 🍵 back in.

## PATCH

This tea is great, but I like a little milk in my tea. Let's update it, but this time, instead of using a PUT, which would change the entire contents, we only want to make one change:

     PATCH [bin labeled "/lunch/beverage"] and change it so that it has a little milk in it.

HTTP though:

     Request: PATCH /lunch/beverage
     Body: { milk: "2 tsp" }

     Response: 200 OK
     Response Body: {type: 🍵, leaf: "green", milk: "2 tsp"}

We could also overwrite a property, like:

     Request: PATCH /lunch/beverage
     Body: { leaf: "oolong" }

     Response: 200 OK
     Response Body: {type: 🍵, leaf: "oolong", milk: "2 tsp"}

But notice, we can't remove a property easily. For that we would need to PUT exactly as we want the resource to be.

## And the REST

We got other methods, briefly:

- **DELETE /lunch/beverage** - Trashes the bin completely, gone.
- **HEAD /lunch/beverage** - Let's do a dry-run of a GET, but honestly I don't even need you to give me the actual resource body (🍵), I'm only testing something, or looking at headers, like checking to see if my cache has expired.
- **OPTIONS** - What methods does the warehouse even support? What are my options? And like, am I authenticated properly? 

And then there's CONNECT and TRACE which are mostly to test connectivity. And any system can make up their own.

And of course there's...

## POST

Ah POST, the every-man. The hammer in every web developers toolbox. The most overused thing in web development.

There was a time when web browsers only supported POST and GET. We couldn't even use any other method, and we had to send requests with query param: 

     POST /lunch/beverage?realmethod=PUT

What a dark time. It's also what enshrined POST as the default.

## Warehouse as State

Imagine taking all the bins and all the resources in them, and writing them all down onto one long piece of paper. That paper is your *state*. Some methods like (GET, HEAD, OPTIONS) don't change that state, or at least they shouldn't. These methods are called *safe*.

You can call a safe method over and over again, and you will never alter the state of the server. Bots and crawlers use this to their advantage by freely visiting urls with GET, so never have an action or process triggered by a GET call even if it has complex query params.

Also, anything like an access log, or view stats should be put somewhere outside of the rest of the state of your server, if possible.

## Reframe

Now here's where we reframe our thinking...

A good REST API is focused on telling the warehouse how to change its state, not by calling the foreman and telling him what to do, but by putting resources into and out of bins or changing their state with a patch.

Instead of thinking "create a new user", we add one ourselves. Not `POST /users/createUser`, but rather:

     Request: PUT /users/deadwisdom
     Request Body: {type: 🤹, name: "Brantley Harris"}
     
     Response: 200 OK

Instead of thinking "create and email an order", we add the order, and we let the warehouse foreman know it should queue up an email when a new order is created:

Not `POST /orders/email`, rather:

     Request: PUT /orders/882484844
     Request Body: {item: 📗, title: "Book: Volume 2"}
     
     Response: 200 OK

And the server automatically sends an email off to the correct person. 

This way, if the state is changed from some other process, maybe an API call or an admin action, the email is sent.

In other words: *Behavior should emerge from changes in state and the ruleset, not from external directives.*

## When POST is Cool

Like all system frameworks, REST is a sort of an ideal. We want to focus on good practices, but practicality sometimes should win.

Sometimes the endpoint is designed to come from a web browser, and HTML forms still don't support extra methods, which is pretty wild. So unless you're using javascript to send the request, you'll have to use POST.

Sometimes you want to create a resource, but you don't know exactly what bin to put it in. This is often because the server needs to generate an ID. Normally you'll know the root of it, so you can do:

     Request: POST /users
     Body: {type: 🤹, name: "Brantley Harris"}

The server should really send a redirect here, directing you to exactly the bin that was just created:

     Response: 201 Created
     Response Body: /users/2557374734

Finally, sometimes you are actually simply trying to invoke an action. Creating a resource would be just awkward. So, POST and forget it.
