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
