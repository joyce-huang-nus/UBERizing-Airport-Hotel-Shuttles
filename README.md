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

<img width="398" alt="08" src="https://user-images.githubusercontent.com/88580416/146171326-edee7ae1-ee9c-4c10-985e-7968032193a0.PNG">

The objective function (1) is designed to maximise time savings based on Clarke and Wrights savings algorithm . This is computed by considering time saved when two nodes are joined to be serviced by one route instead of separating them into two routes. Mathematically, said time savings can be computed as follows:

<img width="377" alt="09" src="https://user-images.githubusercontent.com/88580416/146171458-7dbe0fd4-f23d-405d-b2b0-1df5b2648dd2.PNG">

### Arc travel constraints

<img width="370" alt="10" src="https://user-images.githubusercontent.com/88580416/146171557-e8b9d1dd-dd48-48fa-b926-b02f0a344f42.PNG">

Model constraint (2) indicates that the number of outgoing arcs - i.e., leaving node i = 0 (Changi Airport) is equal to the number of outgoing buses (p). Constraint (3) is similar indicating that the number of incoming travels arcs – i.e., returning to node j = 0 (Changi Airport) is equal to the number of outgoing buses (p). 
Assuming demand for hotels in the cluster is not zero (as modelled by zi), constraints (4) and (5) ensure that hotel drop off points (nodes 1 to n) have only one route arriving to them and one route leaving them. In like manner, if there is no demand (by zi=0), the same constraints ensure that there is no route arriving or leaving the drop off point. 

### Bus capacity constraints and demand

<img width="359" alt="11" src="https://user-images.githubusercontent.com/88580416/146171667-68dfbbf6-eda2-434b-86f9-6562a098de45.PNG">

Constraint (6) serves as a continuity constraint for the number of people dropped off by the bus. This ensures that the net number of people dropped off by the bus is always increasing for each route. Constraint (7) limits the maximum number of people dropped off by a bus to be less than the maximum capacity of the bus. Constraint (11) forces the demand indicator to be 1 if there is demand at that node and forces it to be 0 if there is no demand at that node

### Subtour breaking

<img width="380" alt="12" src="https://user-images.githubusercontent.com/88580416/146171836-6cd2db2c-5f0c-41a9-937c-742cea43d9af.PNG">

Constraints (6) and (8) are subtour breaking constraints besides serving as demand and time constraints, respectively.

### Time constraints

<img width="368" alt="13" src="https://user-images.githubusercontent.com/88580416/146172207-e671ddf2-9919-4c6c-8b79-cc6d1cf26ee8.PNG">

Constraint (8) is also a continuity constraint, ensuring the time taken for a particular route is always increasing. Constraint (9) restricts the time taken for a bus to service all hotels in its route and return to Changi by expected return time of the bus (F). Constraint (10) guarantees that all hotels in a route are serviced within the guaranteed time (T).

### non-negativity and binary constraints

<img width="378" alt="14" src="https://user-images.githubusercontent.com/88580416/146175936-9672287b-06ee-4508-aa19-5939a6863bda.PNG">

Constraints (12) and (13) are non-negativity and binary constraints.

### Model 2 specific elements:

As the team will demonstrate in later sections, Model 1 is used to determine the optimal number of buses for lease or purchase. 
This in turn is used as an input to Model 2 – a more business as usual (BAU) type of model geared at optimizing the number of routes, the nodes for that route and the order of nodes visited by the bus serving said route. With that goal in mind, 2 components are introduced as outlined in Section 3.3. 

	p_max  represents the number of buses purchased/leased as per Model 1
  
	p≤p_max caps the number of routes to the number of available buses purchased or leased. 
 
## IMPLEMENTATION & RESULTS

The below flowchart is a condensed how to guide for business implementation. For the sake of clarity, the team takes you through an actual end to end implementation showing the configuration, dependencies and business decision element at each stage.

<img width="652" alt="flow" src="https://user-images.githubusercontent.com/88580416/146176299-8bad04b1-96d1-4ce1-81e1-de4e9f210875.PNG">

### Step 1: Determine the optimal route distribution with Model 1 
We assume the hotel demand for each time window follows a normal distribution X~N(μ,σ^2) and is approximated by three components: Mean demand, variance and time window multipliers.

As such, mean hotel demand at a given time window is given by the following equation, which uses total room capacity of hotels in the cluster, multiplied by the time window multiplier and a 5% factor to account for an assumed shuttle bus demand ratio:
Mean Hotel Demand at time window

µ= ∑_(i=1)^n▒∑_(j=1)^m▒total room capacity*0.05*time window multiplier
for i=1,…,n in each cluster                                                                     
for j=1,…,m in each hotels                                                                      

The second component, variance, is given to the model in the form of a covariance matrix. This is calculated by calculating the standard deviations of flight arrival numbers gathered over 1+ weeks. Each standard deviation is calculated for each time window period and multiplied by a scaling factor of 0.001.
Lastly, the time window multipliers are approximated based on flight arrival distributions over a 1+ week period. This information can be seen on annexed table 1.
Using the above stochastic demand, Model 1 is run multiple times for each time window to identify the number of routes for the given demand distributions. Results (p route distributions) of  Model 1 for each time window and an aggregated version for the whole day are shown in Figures 8 and 9 respectively.

<img width="422" alt="15" src="https://user-images.githubusercontent.com/88580416/146176678-710157bf-8ac3-4296-bd33-a74212dfbb45.PNG">

Based on results, descriptive stats on the optimal routes are derived. Of particular interest are the minimum, average and maximum values for p. , which form the basis of some key decisions discussed in Step 2. For this run, results are as follows:
	
  Min optimal p routes = 2
	
  Avg. optimal p routes = 4
	
  Max optimal p routes = 6

### Step 2: Match demand with resources to start or maintain operations  
Flowing from Model 1 outcomes, Changi Travel needs to decide and match resource levels to service demand. This entails business decisions such as leasing or purchasing buses, hiring drivers, assigning shifts and provisioning parking slots for the buses acquired.
For this application, the decision is to satisfy 100% of the demand. Hence, the team uses max optimal p routes (6) as reference – i.e. p_max=6  routes and directs Changi Travel to resource accordingly. 
Note that this exercise needs to be done periodically (e.g. quarterly, semi-annually) to allow Changi Travel to adjust to shifts in the demand and  effectively deliver on its service guarantee. 

### Step 3: Optimize path and number of routes with model 2
With Model 1 setting the upper cap for the number of service routes, Model 2 processes more recent deterministic demand for each time window to determine the following:
	
  the number of routes to serve (capped at 6 by Model 1 since we are bounded by the number of buses leased purchased in the short term)
	
  the nodes for each route 
	
  the order of nodes visited by the bus serving that route

As mentioned in earlier narratives, service routes are NOT FIXED and vary based on volume of arriving passengers and their hotel preference. Service routes will consequently reflect this and be dynamic in nature.  Furthermore, due to time and demand constraints imposed, there are a few things to note. 

Hotel clusters that are further away are often served by individual buses so that passengers can reach their destination within the guaranteed time (45 mins) and the bus can return to the airport within an hour. 

Hotel clusters in the downtown area may be served by only one bus as those hotels happen to have more demand than others. 
If there is no demand at a hotel cluster, the bus would not go to the hotel even if it is part of a given route.
To better ground these comments, sample results from running Model 2 are shared as follows.

<img width="336" alt="16" src="https://user-images.githubusercontent.com/88580416/146176878-71f8c327-ea37-429f-9a41-655b185f4dbd.PNG">

As shown in Figure 10, hotels with no demand are not serviced by any route.

<img width="350" alt="17" src="https://user-images.githubusercontent.com/88580416/146176942-bf68fb3f-e8df-4267-990d-1663ce5c683c.PNG">

Figure 11 shows a standard result of the model, where multiple routes service all hotels that have demand. Areas like Sentosa Island that are further away from Changi airport are serviced only by one bus so that they can return to Changi within the hour. Other routes that can meet both the demand requirements and time constraints service multiple stops.  

<img width="371" alt="18" src="https://user-images.githubusercontent.com/88580416/146177038-0d1e674a-e97d-4422-9c1f-b30c35ebbb31.PNG">

During specific time windows where there is high demand, there are more individual routes. Figure 12 is such a case where six (pmax) routes are used as some clusters in the downtown area require their own bus to satisfy demand. 

## CONCLUSION & NEXT STEPS

### Synthesis

In this case study, the team successfully modelled a modified capacitated vehicle routing problem to solve the business problem of planning and optimizing day-to-day shuttle bus routes between Changi Airport and hotels in downtown Singapore. 
To mirror the nature of the industry, a stochastic variation of the model was built to allow more flex in managing business uncertainty all the way from the planning of vehicle leasing, driver hiring and service fulfilment (pmax). At the same time, a further constrained model dynamically optimizes the number of routes, the number and order of hotels visited by each shuttle bus based on more recent deterministic demand for each time window. 
While the current implementation utilizes  k-medoids clustering to reduce the number of nodes, the model can easily be applied as-is to any number of destinations and easily modified to follow any probabilistic distribution of demand across desired time windows provided the complete travel time network is available.

### Next Steps

The following improvements are listed as backlog items to explore in the next iteration: 

(1)	Pick-up & Drop-off Model: 
The current model describes a situation where a bus only drops off passengers. To generate more revenue, the bus service can incorporate pickups from hotels to the airport as well. This model can also be extended to tourist attractions where there is a pick-up and drop off  service for visitors from their hotels. 

(2)	Multiple Bus Capacities:
The current configuration assumes 1 type of bus capacity servicing all routes. Since some hotel clusters may have significantly lower or higher demands,  a mix of multiple types of buses may be more optimal – that is: 
•	If demand is low for a specific cluster, using a smaller capacity bus will be more cost efficient. 
•	If demand is high for a cluster, using a larger bus may be cheaper than dividing the cluster into two and leasing two buses.

(3)	Different Time Window Loading Factors:
In the base model, travel time remains is assumed to be constant throughout the day. Future models can take traffic data into account and identify peak hours which can then be baked into the time window multiplier used to forecast service demand.

(4)	 Dynamic Auto-choosing of K values:

In its current form, the number of clusters is fixed until the next configuration. The ability to dynamically choose the number of clusters before running the model would be an enhancement. To elaborate,
•	Where clusters have extremely high demand, the model may need to consider more hotel clusters – i.e. break down the service area and dynamically add more centroids.
•	Where clusters have low demand, the model may need to consider fewer hotel clusters – i.e. reduce the number of centroids through consolidation.  

## APPENDIX

<img width="484" alt="19" src="https://user-images.githubusercontent.com/88580416/146177311-6e59f0ee-4a07-423c-9c46-d3e652934693.PNG">
