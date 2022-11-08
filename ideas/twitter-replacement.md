## The Idea

A new twitter.

## Features

- Accounts
- Profile
  - Multiple
  - Facets
  - Follow a certain facet of someone
  - Suggested follows are for facets of people you already follow
- Post
- Reply
- Repost
- Like
- Prop (spend a token to up the post) (you buy tokens with money)
- Tips (spend tokens to tip someone)
- Badges
  - Affinity for a tag
- Tags
- Follow
- Following / Followers
- DM / Message
- Group Chat
- Block
- Ignore
- Search
- Streams
  - Moderated kind of groups
- Living posts - Video or Audio posts that live for a while
- Big Posts
  - No limit on posts, you just have to have a description
- Flag missinformation
- Double Post
- Mastadon Integration
- Threadview
- Graph explorer
- View from another person's perspective
- Canon
  - Posts/Replies that are super important and distill an idea
- Two kinds of consumption:
  - Complete (Allows Mark Read)
  - Suggested
- RSS!!!
- Email.

## Endpoints

- POST Register
- GET/PUT/PATCH/DELETE Profile
- GET/PUT/PATCH/DELETE Tag
- GET/POST/DELETE Post / Reply / Message
- POST Like / Reaction / Prop / Flag / Canonize / Read
- POST/GET Tips
- PUT/DELETE Relation (Follow / Block / Ignore)
- PUT/DELETE RSS Feed
- GET Search

## Entities

- Account
- Profile
- Tag
- Post
- Stream
- Tip
- Relation (Follow / Block / Ignore / Etc)
- Subscription (External subscription, like for RSS)

