# Challenge 1 - Network Diagram

## What you will find here

- [`diagram.png`](diagram.png)
  - Network diagram (AWS) that supports variable load and HA.

- [`description.md`](description.md)
  - Short architecture description.

## Summary

The proposed AWS architecture uses:

- Route 53 + CloudFront + WAF for edge entry and protection
- ALB for load balancing
- Backends in private subnets across at least 2 AZs
- RDS Multi-AZ (relational) and DynamoDB (NoSQL)
- NAT Gateway for controlled egress to two external microservices

[go to back](../README.md)
