# Design a Rate Limiter
<b>Benefits</b>
- Reduce cost, esp important for companies that use paid third party APIs
- Prevent servers from being overloaded

<b>Where to put rate limiter</b>
- Can be either client or server side
    - Client is unreliable place because client requests can be easily forged by malicious actor
- We can also create a separate middleware service
- HTTP 429 error: too many requests by client
- Usually implemented within a component called API gateway
    - Fully managed service that supports rate limiting, SSL termination, authentication, IP whitelisting, servicing static content, etc.

Should we put rate limiter in server or API gateway? Depends; do we already have a API gateway for other uses (if so, we may want to add to that)? Do we have the resources (time) to implement our own server-side algorithm + is our tech stach efficient enough -- if not, we may want to use a third-party gateway. 

## Algorithms
### Token Bucket Algorithm
<b>Summary</b>
- Buckets for independent requirements/APIs. T
- Two parameters: bucket size + refill rate
- Pros
    - Easy to implement
    - Memory efficient
    - Allows a burst of traffic for short periods. A request goes through when there are tokens remaining
- Cons
    - May be difficult to tune the parameters to meet needs

<b>How it works</b>
- Container with pre-defined capacity.
    - Fill container at a rate
    - If full, then "overflow" happens and they aren't added
- Each request consumes one token
- When request arrives, check if there are enough tokens in bucket
    - Yes: take out token and pass request through
    - No: drop request
- How many buckets?
    - Different buckets for different API endpoints
    - Examples:
        - If user can make 1 post per second, add 150 friends per day, and like 5 posts per second, we need 3 buckets per user
        - If we need to throttle requests based on IP address, each IP address needs a bucket
        - One global bucket f system allows maximum of 10,000 total requests per day
