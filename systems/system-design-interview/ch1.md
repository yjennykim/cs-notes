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