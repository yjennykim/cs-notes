#### Scale from Zero to Millions of Users
<b>Server Setup</b>
1. Users access website through domain names. The Domain Name System (DNS) is used for looking up IP address by domain name.
2. With the IP address, HTTP request to this server
3. Server returns with HTML pages or JSON

<b>Adding a database</b>
1. Database is added to another server. Separating web tier and data tier servers allow them to be scaled independently
2. NoSQL for specific requirements, like unstructured and/or massive amounts of data that doesn't require relational data

<b>Vertical vs Horizontal Scaling</b>
1. Vertical scaling is simple and good when there is low traffic. However, there is a hard limit on how much power you can add to a single server. There is also a risk of single-point failure.
2. Horizontal scaling is better for large scale applications.

<b>Load Balancer</b>
1. If a server is down or there is a lot of traffic, then users can't access or the responses will be slow. Load balancer resolves these issues.
2. Load balancer evenly distributes traffic among web servers. 
3. User connects to public IP of the load balancer. Load balancer talks between servers in the network via private IPs for better security (servers are no longer directly reachable over the internet).
4. If server 1 goes offline, traffic is routed to server 2. If website traffic grows rapidly, load balancer can automatically start to send requests to new servers in the web server pool.

<b>Database Replication</b>
1. Master database only support write operations (and insert, delete, update -- any data-modifying commands)
2. Usually more reads than writes, which means more slaves than masters.

<b>Advantages:</b>
- Better performance. All writes and updates happen in master nodes, and read operations are distributed across slave nodes. More queries are processed in parallel.
- Reliability: Data is replicated across multiple locations so data is preserved if something happens
- High availability: Website remains operational since you can access data in different locations

<b>What if database goes offline?</b>
- If one slave database is available and goes offline, read operations directed to the master db temporarily, and new slave db will replace the old one
- If multiple slave databases are available, read operations are redirected to other healthy slave databases
- If master database goes offline, a slave database gets promoted temporarily

<b>The new design (everything put together)</b>
1. User gets the IP address of the load balancer from DNS
2. User connects to the load balancer with this IP address
3. The HTTP request is routed to Server 1 or Server 2 (however many servers are available)
4. Web server reads user data from a slave database
5. Server routes any data-modifying operations to the master database

<b>Cache</b>
- Cache is a temporary data store layer, faster than a database
- Offers better system performance, ability to reduce database workloads, and the ability to scale the cache tier independently 
- Read-through cache: strategy for after receiving a request, server checks if cache has available response. If so, it sends data back to client. Else, it queries the database, stores the response in cache, and sends it back to clientl
- Most cache servers like Memcache provide APIs for common programming languages
`cache.set('key', 'value', 36000 * SECONDS)`
`cache.get('key')`

- When to use a cache:
    - When data is read frequently but modified infrequently. Cached data is stored in volatile memory, so important data should be saved in persistent data stores.
- Expiration policy:
    - Good practice to implement. Once cached data is expired, remove it from the cache. 
        - Without this policy, cached data would be stored in memory permanently. 
        - Too short expiration date as this will cause the system to reload data from the database too frequently. 
        - Ttoo long expiration date will make data become stale.
- Consistency:
    - Keep the data store and the cache in sync
    - Inconsistencies occur because data-modifying operationso n the data store and cache are not a single transaction 
- Distributed caching:
    - Single cache server could potentially be a single point of failure (could cause the whole system to stop working if itself fails)
    - Multiple cache servers across data centers are recommended
- Eviction policy:
    - If cache full, adding to cache means removing something first
    - LRU (Least Recently Used) - most popular
    - FIFO (First in First Out)

<b>Content Delivery Network (CDN)</b>
- CDN = network of geographically dispersed servers used to deliver static content like images, videos, Javascript files, etc.
- When a user visits website, CDN server closest to user will deliver static content
    - The further user is from CDN servers, the slower the website loads

Example: User tries to fetch image using URL
1. If CDN server does not have image in cache, it will request file from the origin (like a web server or online storage such as Amazon S3)
2. The origin returns image to CDN server, which includes optional HTTP header Time-to-Live (TTL) describing how long the image should be cached
3. CDN caches the image and returns it to User 

Considerations:
- Cost: CDNs are run by third-party providers and you are charged for data transfers. You should remove infrequently used assets from CDN
- Setting appropriate cache expiry
- CDN fallback: in the case of CDN failure, clients should be able to detect the problem and request resources from origin instead

After CDN:
1. Static assets are no longer servered by web servers, instead, they are fetched from CDN for better performance
2. The database load is lightened by caching data.

<b>Stateless Web Tier</b>
- In a stateful server, for every user, they would need to be routed to the same server in order for the user's session to be remembered (e.g. authentication). This makes it challenging to handle servers, in the case we want to add/remove them or they fail
- Stateless architecture requires all the servers sharing a shared data store dedicated to state/session data

<b>Data Centers</b>
- geoDNS is a DNS service that allows domain names to be resolved to IP addresses based on location of user
- If data center outage, direct traffic to healthy data center

<b>Messaging Queue</b>
- Durable component stored in memory that supports asynchronous communication
- Serves as a buffer and distributes asyncrhonous requests
- Producer and consumer can be scaled independently
    - Large queue -> add more workers
    - Small queue -> reduce workers

<b>Database scaling</b>
- Sharding: separate databases into smaller, more easily managed parts called shards
    - Each shard shares the same schema, but data unique to the shard
    - Anytime you access data, hash function is used to find corresponding shard
- Why?
    - Reads can happen in parallel
    - Reduce load on a single database
    - Less entries per shard, lookups are faster
- Shard key: used to decide which shard a particular piece of data belongs to. When data is written to the database, the sharding key value for that data is used to determine which shard it should be stored in. 
- Choosing a shard key:
    - The shard key should be static and not change frequently. 
    - The shard key should be unique or have a high degree of uniqueness. 
    - The shard key should be chosen based on how the data is queried. 
    - The shard key should be a single field or a combination of fields. 
- Resharding needed when 1. a single shard can't take more data 2. certain shards experience shard exhaustion faster than others due to uneven distribution
- Celebrity / hotspot key problem: excessive access to specific shard can cause server overload. For example, if all celebrities were in one shard, then there would be a lot of reads there. Might want to allocate shard per celebrity.
- Hard to do join operations on a database that is sharded across mlutiple servers. Denormalizing is the workaround.

<b>Summary</b>
1. Keep web tier stateless
2. Build redundancy at every tier
3. Cache data 
4. Support multiple data centers
5. Host static assets in CDN
6. Scale data tier by sharding
7. Split tiers into individual services
8. Monitor your system nand use automation tools