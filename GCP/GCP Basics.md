# Before Cloud

![[GCP before cloud.png]]

## Example 1 Online Shopping App

Challenge:
- Peak usage during holidays and weekends
- Less load during rest of the time

Solution (before the Cloud):
- PEAK LOAD provisioning : Procure (Buy) infrastructure for peak load
	- What would the infrastructure be doing during periods of low loads?
## Example 2 - Startup

Challenge:
- Startup suddenly becomes popular
- How to handle the sudden increase in load?

Solution (before the Cloud):
- Procure (Buy) infrastructure assuming they would be successful
	- What if they are not successful?
## Challenges

- High cost of procuring infrastructure
- Needs ahead of time planning (Can you guess the future?)
- Low infrastructure utilization (PEAK LOAD provisioning)
- Dedicated infrastructure maintenance team (Can a startup afford it?)

# Silver Lining in the Cloud

How about provisioning (renting) resources when you want them and releasing them back when you do not need them?
- On-demand resource provisioning
- Also called Elasticity

## Cloud - Advantages
- Trade "capital expense" for "variable expense"
- Benefit from massive economies of scale
- Stop guessing capacity
- Stop spending money running and maintaining data centers
- "Go global" in minutes

# Google Cloud Platform (GCP)

- One of the Top 3 cloud service providers
- Provides a number of services (200+)
- Reliable, secure and highly-performant:
	- Infrastructure that powers 8 services with over 1 Billion Users: Gmail, Google Search, YouTube, etc.
- "Cleanest cloud"
	- Net carbon-neutral cloud (electricity used matched 100% with renewable energy)

# Regions and Zones

## Regions

- Imagine setting up data centers in different regions around the world
	- Would that be easy?
- (Solution) Google provides 20+ regions around the world
	- Expanding every year
- Region : Specific geographical location to host your resources
- Advantages:
	- High Availability
	- Low Latency
	- Global Footprint
	- Adhere to government regulations

![[GCP R&Z Regions 1.png]]
## Zones

- How to achieve high availability in the same region (or geographic location)?
	- Enter Zones
- Each Region has three or more zones
- (Advantage) Increased availability and fault tolerance within same region
- (Remember) Each Zone has one or more discrete clusters
	- Cluster : distinct physical infrastructure that is housed in a data center
- (Remember) Zones in a region are connected through low-latency links

![[GCP R&Z Zones 1.png]]

## Regions and Zones example

| Region Code   | Region                            | Zones | Zones List                                            |
| ------------- | --------------------------------- | ----- | ----------------------------------------------------- |
| us-west1      | The Dalles, Oregon, North America | 3     | us-west1-a<br>us-west1-b<br>us-west1-c                |
| europe-north1 | Hamina, Finland, Europe           | 3     | europe-north1-a<br>europe-north1-b<br>europe-north1-c |
| asia-south1   | Mumbai, India APAC                | 3     | asia-south1-a<br>asia-south1-b<br>asia-south1-c       |

## Application in Single Zone

![[GCP R&Z 1.png]]

- Imagine that your application is deployed in a data center in London
- What would be the challenges?
	- Challenge 1 : Slow access for users from other parts of the world (high latency)
	- Challenge 2 : What if the data center crashes?
		- Your application goes down (low availability)

## Application in Multiple Zones

![[GCP R&Z 2 1.png]]

- Let's add in one more data center in London
- What would be the challenges?
	- Challenge 1 : Slow access for users from other parts of the world
	- Challenge 2 (SOLVED) : What if one data center crashes?
		- Your application is still available from the other data center
	- Challenge 3 : What if entire region of London is unavailable?
		- Your application goes down

## Application in Multiple Regions

![[GCP R&Z 3 1.png]]

- Let's add a new region : Mumbai
- What would be the challenges?
	- Challenge 1 (PARTLY SOLVED) : Slow access for users from other parts of the world
		- You can solve this by adding deployments for your applications in other regions
	- Challenge 2 (SOLVED) : What if one data center crashes?
		- Your application is still live from the other data centers
	- Challenge 3 (SOLVED) : What if entire region of London is unavailable?
		- Your application is served from Mumbai

# Types of GCP Services

- [[GCP Compute Engine Basics]]