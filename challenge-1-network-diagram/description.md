A typical AWS architecture for a web application with variable load and high
availability (HA) clearly separates the edge (entry from the Internet), the
backend compute layer, and the data layers. For a JavaScript frontend, an S3
bucket is used to host the compiled static assets (HTML/CSS/JS), and CloudFront
acts as the CDN to serve them from locations close to users, reducing latency
and offloading work from the backend. CloudFront also terminates TLS and enables
caching for static content, which improves overall performance during traffic
peaks.

A recommended entrypoint for web traffic is: Route 53 (DNS) -> CloudFront (CDN)
-> ALB (Application Load Balancer) -> backends. In this flow, AWS WAF is used
as an application firewall, but technically it is not an "intermediate server";
it is associated with CloudFront and/or the ALB to filter common attacks
(injections, XSS, bots, volumetric abuse) before requests reach the backend.
The ALB distributes dynamic requests across multiple backend instances,
supporting variable loads through horizontal scaling and improving availability
by detecting unhealthy instances.

To guarantee HA at the network and compute levels, the backend is deployed
inside a VPC spread across at least two Availability Zones, using public
subnets (for the ALB and NAT Gateways) and private subnets (for backend
tasks/instances and databases). In private subnets, the backend runs as a
service with autoscaling (for example on ECS/Fargate), keeping at least two
replicas in different AZs. Public subnets connect to an Internet Gateway, while
outbound access from private subnets to the Internet (egress) is done through a
NAT Gateway, avoiding direct exposure of the backend.

At the data layer, two storage types are combined. For the relational database
(transactions, consistency, joins) Amazon RDS in Multi-AZ mode (PostgreSQL or
MySQL) is recommended, providing managed failover. For the non-relational
database, DynamoDB is recommended, a distributed and scalable service suited
for low-latency and high-volume patterns (for example sessions, catalogs,
profiles, or event logs). This meets the requirement of having both a
relational and a non-relational database by using managed services.

Because the backend consumes two external microservices, the recommended
network-level connectivity is: backends in private subnets -> NAT Gateway (with
Elastic IP) -> Internet Gateway -> Internet -> external API A/B. This allows
controlled egress, keeps stable public IP(s) (useful if the third party requires
whitelisting), and keeps the backend without a public IP. If the external
provider supports private connectivity, a more secure alternative is
VPN/PrivateLink, avoiding the public Internet.

Two entrypoint options were considered. (1) CloudFront + WAF + ALB (chosen): the
most standard pattern for public web apps, easy to explain and meeting
HA/scaling/network requirements without extra complexity. (2) Amazon API Gateway
as the primary entrypoint (not chosen for this submission): it adds
authentication, rate limiting, and API versioning, but introduces additional
complexity (VPC Link and/or integrations) and is not required to satisfy the
requested network diagram requirements. In the final architecture implemented
for this challenge, API Gateway is not used (neither for ingress nor egress);
egress to external microservices is handled through a NAT Gateway. From a
security perspective, least-privilege Security Groups are applied: the ALB
exposes HTTPS, the backend only accepts traffic from the ALB, and RDS only
accepts connections from the backend.

[go to back](README.md)
