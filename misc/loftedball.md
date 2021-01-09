---
title: Loftedball
layout: article

tags: Loftedball, About
---

![alt text][LB_logo]

Loftedball was allowing broadcasters to sell football
match streams. It was dealing with payments, scaling and security.
The idea was bring all of the football matches in one place so that
people wouldn't have to maintain different accounts. This would also
allow viewers to only pay for the matches they were interested in
without long term commitments and with ease.
I implemented an Android application in Java. I will use
a few screenshots to explain how it was working.

Viewers could simply register with their email. Broadcasters would
register in two steps: the application's basic registration and Stripe's
verification process. This was done to verify the identity of each
broadcaster and reliably create a set of streaming permissions.
Each user would be able to see match scores and statistics as
well as LIVE and NEXT broadcasts as you can see in the images below.

![alt text][Scores]
![alt text][Match_stats]

The verified broadcasters were able to select one of the mathces in the list
and create a broadcast by selecting parameters like currency, price and
commentary language. That is only if the broadcaster was allowed to broadcast
a certain match in any country.

![alt text][Create_broadcast]

Each user could then select one of the available broadcasts for a match,
pay and watch.

![alt text][Live_broadcast]
![alt text][Broadcast_selection]
![alt text][Live_stream]

The application's back-end was implemented with Python using the excellent
Django framework. I built an API using GraphQL which was talking to the
Android application. I was using Stripe's *Connect* product to handle payment flows
from customers to broadcasters with a commission going to Loftedball's account.
I was also using the SportMonks API to fetch football data, the Wowza Cloud
(on Fastly CDN) for the streaming and Heroku for the deployments.

[Scores]: /assets/images/loftedball_screenshots/Scores.jpg "Scores."
[Broadcast_selection]: /assets/images/loftedball_screenshots/Broadcast_selection.jpg "Broadcast selection."
[Create_broadcast]: /assets/images/loftedball_screenshots/Create_broadcast.jpg "Create broadcast."
[Live_broadcast]: /assets/images/loftedball_screenshots/Live_broadcasts.jpg "Live broadcasts."
[Live_stream]: /assets/images/loftedball_screenshots/Live_stream.jpg "Live stream."
[Match_stats]: /assets/images/loftedball_screenshots/Match_stats.jpg "Match stats."
[LB_logo]: /assets/images/loftedball_screenshots/Logo.png "Loftedball's logo."
