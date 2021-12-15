# UBERizing-Airport-Hotel-Shuttles

## BUSINESS CONTEXT
With most of the country’s population vaccinated against COVID-19 and a similar movement taking place concurrently across the globe, Singapore has accelerated its shift towards a COVID-endemic strategy. Prompted by the need to jumpstart the economy, measures have been passed to allow the country and various sectors of the economy to re-open gradually.  Part and parcel of these reforms is the relaxation of border control measures. As of October 2021, vaccinated travellers from eight (8) countries were allowed to enter the country without the need for self-quarantine. That list has since expanded to include thirteen (13) countries on top of comparatively looser restrictions for jurisdictions not yet part of Singapore’s Vaccinated Travel Lanes (VTL) program.

![01](https://user-images.githubusercontent.com/88580416/146135268-5faba9f1-15f1-4352-8cbd-cee7c0cdc3e7.png)

The above chart  shows a longitudinal view of current air traffic alongside volumes before and during the pandemic. While still a far cry from the annual pre-COVID peak of 68 million travellers, the trend of air traffic shows a slow but steady increase - an encouraging sign that both business and leisure travellers are starting to come back into the country.

## THE SERVICE PROPOSITION
To complement the government’s reopening strategy, Changi Travel aims to provide safe and convenient transit for arriving passengers through “a flexible shuttle bus service to ferry guests from the airport to drop off points near their hotel of choice within a specified time guarantee of 45 minutes”. Interest in booking the service is indicated at point of ticket purchase or 24 hours before the flight. 
Given the tight time window promised, a more detailed study of the problem and its individual components is warranted. In this section, the team breaks down the service proposition into its core elements with the underlying assumptions embedded into the below analysis and subsequently translates these into two (2) model objectives to be optimized. 

![02](https://user-images.githubusercontent.com/88580416/146135654-c486a1d5-d625-4496-8e67-964a08bc817b.png)

### Starting Node & Service Demand: 
All passenger journeys begin at Changi Airport  with the shuttle service catering to guests across all 24 time windows. Demand for the service is assessed on a per time window basis with each window equivalent to a 60-minute period starting from 00:00 and ending at 23:59. Demand is assumed to be normally distributed and computed stochastically based on:
a)	the volume of passengers arriving in Singapore at a particular time window, estimated through the number of flights arriving at a particular time window
b)	an inferred preference for a particular hotel, which in turn is estimated by multiplying the target hotel’s room capacity with a 5% loading factor 
c)	variance matrices for both arrivals and hotel preference
An illustration of how this works is provided in Section 4, where the team goes through an implementation example end to end. 

### Service Area and Drop-off Points:
As part of the study, geodata from all ~ 480 hotels across Singapore were pulled from data.gov.sg  and analysed to determine a sensible service area. Post the analysis, accommodations lying to the far west were dropped. Focused service area were picked as shown below in Fig 2.

<img width="316" alt="03" src="https://user-images.githubusercontent.com/88580416/146136075-e7b35e04-7c4b-4e78-a913-dccb67edd844.PNG">

As a pre-processing step, hotels were clustered using the K-medoids algorithm, effectively designating actual hotels as drop off points. Figure 4 shows the targeted area now expressed in color-coded clusters alongside designated drop off points for each cluster. 

<img width="295" alt="04" src="https://user-images.githubusercontent.com/88580416/146136096-42333c59-a2a0-433f-a31d-a29198200f37.PNG">

Note that the number nodes can dynamically be adjusted as demand picks up in line with the government’s phased reopening. 

### Service Routes:
Unlike traditional bus routes, routes for Changi’s shuttle service are not fixed. Instead, they vary based on the demand at the starting node (referenced in Section 2.1.1) for a particular day and time of day. The concept operates around a pseudo on-demand model enabled by passengers pre-booking interest in the service at least 24 hours before their arrival. This has important implications - in particular,  

a.	The number of routes across the day and time of day may vary.
b.	The scope of drop off points serviced by each route may change.

Distance and time travel between points in a route were calculated using the Google Distance Matrix API. Both values are assumed to be symmetric (AB=BA) and required travel time is assumed to be constant across the day. This latter assumption is a simplification made by the team for modelling purposes and can be expanded on in future studies.
Furthermore, route configurations must ensure all passengers are dropped off within forty-five (45) minutes as per the service guarantee. 

### Bus Fleet
Another critical element is the bus fleet. In the case of Changi Travel, all routes are served by 50-seater coaches. In its current configuration, each route is served by a single bus with seats being booked beforehand. To allow sufficient time to disinfect and prepare the bus for the next load, all buses need to return to Changi within 60 minutes from the initial time of departure. 

The number of buses required are evaluated on two levels: (a) An initial number of buses seeded to serve current demand and (b) a semi-annual review of additional buses required based on the uptick in inbound traffic and service adoption of the passengers.

### Model Objectives
With the core components and assumptions outlined, there are two (2) key modelling outcomes the team wishes to achieve:

![05](https://user-images.githubusercontent.com/88580416/146136300-b6675bda-b71f-4bd2-93b3-13e9e62d79ae.png)

## MATHEMATICAL FORMULATION

### Model Overview
In this section, the business objectives are formulated mathematically as a modified capacitated vehicle routing problem . The analysis starts off with a big picture view of key variables, the objective function and constraints. This is followed by subsections featuring a more in-depth treatment of each of these components. 
For the sake of clarity, the business objectives are mapped to corresponding model formulations, Model 1 and Model 2 – terms which the paper will repeatedly reference. These models are defined as follows:

•	Model 1 determines the optimal number of buses needed to run the operation at a particular point in time. 

•	Model 2 determines the day-to-day optimization of the number of routes, the nodes for that route and the order of nodes visited by the bus serving said route.

As a starting point, note that both models share the same base variables, objective function and constraints. Model 2, however, has an additional variable and one additional constraint – the details of which are show in the succeeding sections. 

### Model Variables

<img width="523" alt="06" src="https://user-images.githubusercontent.com/88580416/146136581-f1a62626-7b6f-40c4-af5e-d547ed973966.PNG">

### Full Model Formulation

<img width="425" alt="07" src="https://user-images.githubusercontent.com/88580416/146169868-bbc9452c-e27b-42c4-a340-b56ef0775789.PNG">

### Objective Function

<img width="287" alt="08" src="https://user-images.githubusercontent.com/88580416/146136750-7d46f4e9-6824-4deb-919e-845686cc353d.PNG">

The objective function (1) is designed to maximise time savings based on Clarke and Wrights savings algorithm . This is computed by considering time saved when two nodes are joined to be serviced by one route instead of separating them into two routes. Mathematically, said time savings can be computed as follows:

<img width="287" alt="08" src="https://user-images.githubusercontent.com/88580416/146161316-5b1f2900-9220-4c33-824a-e1b6ff78b113.PNG">

### Arc travel constraints

<img width="268" alt="10" src="https://user-images.githubusercontent.com/88580416/146162405-9cc33eb2-e1d6-4ff2-becd-99d0a9f38f0e.PNG">

Model constraint (2) indicates that the number of outgoing arcs - i.e., leaving node i = 0 (Changi Airport) is equal to the number of outgoing buses (p). Constraint (3) is similar indicating that the number of incoming travels arcs – i.e., returning to node j = 0 (Changi Airport) is equal to the number of outgoing buses (p). 
Assuming demand for hotels in the cluster is not zero (as modelled by zi), constraints (4) and (5) ensure that hotel drop off points (nodes 1 to n) have only one route arriving to them and one route leaving them. In like manner, if there is no demand (by zi=0), the same constraints ensure that there is no route arriving or leaving the drop off point. 

### Bus capacity constraints and demand

<img width="284" alt="11" src="https://user-images.githubusercontent.com/88580416/146168586-b267ba6c-c851-4658-a7af-72b08e138f7f.PNG">

Constraint (6) serves as a continuity constraint for the number of people dropped off by the bus. This ensures that the net number of people dropped off by the bus is always increasing for each route. Constraint (7) limits the maximum number of people dropped off by a bus to be less than the maximum capacity of the bus. Constraint (11) forces the demand indicator to be 1 if there is demand at that node and forces it to be 0 if there is no demand at that node


