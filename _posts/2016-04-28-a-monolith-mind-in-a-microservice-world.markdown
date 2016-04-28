---
published: false
layout: post
title: A Monolith Mind in a MicroService world
---
There is a lot of demand and interest in 'the cloud' amongst companies whose core business is not software and a result platforms have to adapt to stay in touch but many of these products grew up in a land where client server interactions were simple, many of the people who grew these products don't live in a multi touchpoint land. Many of these platforms are still wrestling with a mobile first world.

## An Authentication Example

You could just carry on as you always did but eventually your customers will expect (maybe they already do) some services from other providers. Likely your first experience of this will be authentication and user federation. That's only authentication, I hear you say thats easy. Let's stop and have a think about that:

1. The user/guid is probably the identifier you use in your system for all user related operations? 
1. User permissions and roles are probably part of the user, if your lucky they are just keyed from their id.
1. You keep the user in the servers session
1. You probably have user account/profile page

## Logging 

Your probably awful at logging, you've never had any need to be good at it, when it broke it was always someone else's problem.

When you move into a multi service model it could now be someone else's problem, you want to identify that early, that would be great.