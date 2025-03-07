# Final Project Proposal

**Team:** Tina Feng, Silvia Gu, Zhiyuan Guo, Anita Sun

**GitHub Repo URL**: https://github.com/CMU-IDS-2022/final-project-flight-traffic-brain


## Overall Topic 
Flight Traffic Brain

## Track ##
Interactive Visualization/Application 
## Questions to Ask ##
**In this project we want to:**
1) Help airports better manage airlines and control airline traffic;
2) Help passengers make wiser decisions about airline flights; 

**Our exploration will be centered around 4 directions to offer help for both airports and passengers:**

1. **Design for Airports**
    1) Create a flight network graph to visualize the connections between airports and flights
    2) Develop a ML model to predict potential delay time of certain flights

2. **Design for Passengers**
    1) Develop a ML model to predict delay time (for both airports and passengers) and price of flights 
    2) Provide passengers with flight planning recommendations based on information about major airports and flights using historical data


## Data Processing

Our data srouces are from [FlightRadarAPI](https://github.com/JeanExtreme002/FlightRadarAPI) and a [Kaggle Dataset](https://www.kaggle.com/datasets/zernach/2018-airplane-flights) about flight prices from 2018.

FlightRadarAPI provides us an API to retrieve real-time air traffic data, 
including airlines list, flights list, and information regarding a flight such
as time, altitude, speed, source, destination, and so on. The data format
mostly comes in json, and we would need to do some processing to convert data
into our desired format. We plan to use real-time flight information, such as
flight locations, source and destination, and departing and landing time.
The data processing will be implemented in a class called `AirData` in 
`airdata.py`, where we request data from API and parse data into a dataframe.
Below is an example of such dataframe.

```
>>> from airdata import AirData
>>> ad = AirData()
>>> df = ad.get_flights_df()
>>> df
           id icao_24bit  latitude  ...  vertical_speed  callsign  airline_icao
0    2b6b0475     AC0D21   37.7189  ...               0   FDX1800           FDX
1    2b6b4868     A6C826   35.6307  ...               0    EJA536           EJA
2    2b6b5cdc     896464   60.7218  ...             -64    ETD93C           ETD
3    2b6b6ae7     781D6D   33.4156  ...            -960    CKK221           CKK
4    2b6b7226     896488   53.1064  ...               0    ETD11B           ETD
..        ...        ...       ...  ...             ...       ...           ...
202  2b6c2c2a     A086B4   44.2131  ...               0    DAL852           DAL
203  2b6c2c45     AD237F   30.0230  ...               0   AAL2813           AAL
204  2b6c2c8d     A14872   33.8755  ...             128    DAL768           DAL
205  2b6c2cfa     A06010   34.0600  ...               0   AAL1820           AAL
206  2b6c2d0f     A202FD   18.6110  ...            -832    FFT100           FFT

[207 rows x 19 columns]
```

We also processed data for the Kaggle dataset as follows.
The original raw dataframe looks as follows.
![Raw data price](sketch/price-prediction-raw_dataframe.png)

Then, we perform data cleaning and processing as follows:

* Take a random sample of 10000 observations
* Only keep the following variables: 
	- PricePerTicket, Miles, NumTicketsOrdered, Quarter, OriginWac, DestWac, AirlineCompany
* Encode OriginWac (51 unique values)  and DestWac (50 unique values) into actual state names, then conduct OneHot Encoding 
* Conduct OneHot Encoding on selected categorical features including Quarter (4 unique values), AirlineCompany (11 unique values)
* Given the different scales of numerical features, we take log of PricePerTicket and Miles

The resulting dataset include 10000 observations with 118 columns.
* 1 target variable: log_price
* 2 numerical features: log_miles and NumTicketsOrdered
* 115 dummy variables 
![Cleaned data price](sketch/price-prediction-cleaned_dataframe.png)



## System design

### Flight Map

![Sketch map](sketch/map_flights.jpg)

The real-time map of flights will contain the current flights and flights just landed in the US. In the map, we also
show connections between airports and flights when users drag their mouse over the flight. Under the map,
we display using scatter plots and histogram relevant information such as longtitude/latitude, speed and altitude.
The scatter plots and histogram will be linked together and also interact with the map.

### Flight Delay Visual Analysis

![Sketch delay](sketch/Flight_Delay_Analysis_Sketch.jpg)

Sketch set 1:
The first two plot graphs above explore the changes of average delay time of all flights at different times in a day. The users can filter the data by using the drop-down UI components at the top to select the specific origin airport, departure airport and airline. The users can then see how the average delay time change based on the different scheduled departure time (left sketch) and the different scheduled arrival time (right sketch) of a day.

Sketch set 2:
The last two sketches at the bottom compare the average delay time of different airlines in the United States. The users can filter the data by using the drop-down UI components at the top to select the specific origin airport and the departure airport. The left sketch uses a bar graph to visualize the differences of average delay time between different airlines. The right sketch uses a stripplot graph to visualize the punctuality and delay of all flights of different airlines.


### Flight Price Prediction

![Sketch price 1](sketch/price-prediction-sketch_1.png)

We allow users to select some basic information like time and destination, and predict a price range accordingly. We would visualize the price and interact it’s location on the overall price histogram.

![Sketch price 2](sketch/price-prediction-sketch_2.png)

We enable users to select the origin and destination on the US map and generate a ranked(ascendingly rank by price) list of airline information.

![Sketch price 3](sketch/price-prediction-sketch_3.png)
![Sketch price 4](sketch/price-prediction-sketch_4.png)

		
## Specifics ##
Nowadays, air traffic control has become a complicated task as there are more and more flights and airlines. It becomes harder for the airports and air traffic control specialists to manage those flights coming from different locations in the world. Because of the dependencies between flights, a delay in one can cause delays in other flights if they are not properly managed. An interactive data science solution can help alleviate this problem. A real-time map of flights with interactive information such as speed and altitude can help the specialists to make better decisions. Meanwhile, an interactive network graph that shows the connections between airports and flights can also improve the handling of dependencies among the traffic.

Besides the real-time interactive network graph, we also plan to develop a ML model with historical flight data including airlines, routes, dates, departure time and arrival time in order to effectively predict the potential delay time of certain flights in real time. The predicted delay time will help airport specialists to better communicate with the airports and passengers and make better decisions in terms of resource distribution and cost reduction.

Aside from delay time, we will build a ML model using historical data to predict flight price as well. Candidate parameters include airline code, flight time (flying hours, long-haul or short-haul), point of departure, destination, etc. This model will help passengers estimate the potential fare of flight of their interest. It will also allow them to compare different flight prices by modifying parameters of interest, thus helping optimize their travel plan. 

People are provided a lot of information about airlines when they plan trips. However, these pieces of information are quite disordered, which makes it difficult to choose. We hope to design a platform and help customers design and plan trips. The interactive options include the place of departure and destination, date, price ranges they can accept, etc. We will visualize the recommended airlines in a map that mark the departure, destination, transition places, the airline routes. Users can also choose two specific airlines to compare features like price, duration, distance. We will visualize the comparison module through a jointed bar chart, in which each column represents a feature. 





