## Apartemnts sales research

### Project Description:
We have data from one of the Real Estate services, which represented by ads of apartments in the city of St. Petersburg and neighboring areas for a period of several years. We need to learn how to determine the market value of real estate. Our assignment is to set necessary parameters. This will allow us to build an automated system, such as tracking for anomalies and any fraudulent activity.
Every apartment ad has two types of data. Firts one the data entered by user himself, the second one parameters obtained automatically based on geopositioning data. For example, distance to the center of the city, distance to the airport, distance to the nearest park and any nearest ponds.

### Data Description:
The original table contains 22 columns, with a description of the data on the sold apartments and their parameters:

- `airports_nearest` — distance to the nearest airport in meters (m)
- `balcony` — number of balconies
- `ceiling_height` - ceiling height (m)
- `citycenters_nearest` - distance to the city center (m)
- `days_exposition` - how many days the ad was active (from initial publication to removal)
- `first_day_exposition` — publication date
- `floor` - floor
- `floors_total` - total floors in the building
- `is_apartment` — apartments (boolean type)
- `kitchen_area` - kitchen area in square meters (m²)
- `last_price` - price at the time of removal from publication
- `living_area` - living area in square meters (m²)
- `locality_name` - the name of the locality
- `open_plan` - free layout (boolean type)
- `parks_around3000` - number of parks within 3 km radius
- `parks_nearest` — distance to the nearest park (m)
- `ponds_around3000` - number of ponds within a radius of 3 km
- `ponds_nearest` - distance to the nearest body of water (m)
- `rooms` - number of rooms
- `studio` — studio apartment (boolean type)
- `total_area` - total area of the apartment in square meters (m²)
- `total_images` - the number of photos of the apartment in the ad

Performing a complete pre-processing of the data and study it, we found interesting features and dependencies that exist in the real estate market. We confirmed the dependences on the graphs using correlation and Pearson's coefficient. Based on our analysis, we can draw the following final conclusions:

- The strongest influence on the price is exerted by the total area of the property and the maximum proximity to the city center (on the example of St. Petersburg). The closer to the center, the higher the price of the object. The number of rooms also affects the price of the property, which is logical to associate with the total area of the apartment. The peak of property prices came in 2014. It can be suggested that this was due to the increased interest in investing money in the purchase of real estate, which caused an increase in prices in the market.

- Based on the timing of the sale of objects, most often the apartment was sold in 95 days. We came to the conclusion that if a publication was listed for less than 45 days, then it is considered a quick sale, and if it is longer than 230 days, it is a long sale. There were a large number of apartments sold in just a few days after publication.

- The first floor turned out to be much cheaper than the other categories of floors. Also, the cost of an apartment on the top floor is lower than on the rest, except for the first one.

- We found out the cost per square meter in the top 10 settlements by the number of ads. The leader is the city of St. Petersburg with a price of 114,848 rubles per sq.m.
