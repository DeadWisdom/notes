# The Many Faces of the User

One of the opportunities I want to give users is the ability to have many different "profiles" but one account. 
So I should be able to sign up for SocialPlatform with one login and password and create multiple profiles.
Other users would not be able to discover my other profiles from any individual profile. They would see each as
completely on its own.

Users can already do this; they just have to use a new email address. Twitter, in fact, started grouping them 
together, and so does Google. But each account needs its own email/password etc, for no really good reason except
the systems weren't designed for it and that's a hard thing to change.

And lots of people want to do this. I have multiple twitter accounts, one for personal life and one for tech. 
For some, it's a matter of their very identity. One is not necessarily the same from context to context
and often a person need to hide other contexts for very good reasons, including their own personal safety.

The question I have: is it useful to have one master account and allow them to have many profiles? Or is it too
confusing to be useful. Having multiple accounts is straightforward. Yes there are accrobatics around dealing
with it, but fundmentally it makes sense in most user's mind. They are not used to a system that allows multiple
profiles.

This hints at the benefits of the grouping strategy that Twitter and Google use. It's super easy to understand
what's happening for a user. Yes, they have to save another password, probably their browser does it for them;
and yes, they have to switch accounts, but they would need to do that anyway.

It also simplifies things. Our models can be pretty simple and expected. All we need to do is store a list
of accounts in the client, with a current active one (which we already do), and perhaps a `relatedAccounts` list 
on the Account object itself.

From a security perspective, it's clearly a better choice because if one account is compromised the others
aren't. If anything we want the user to have *more* auth partitions.

Finally it pushes most handling of this multiplicty down to the client level and out of pretty much all server
code. This is a huge bonus and hints at a fundemental boundary in the design.
