## Research on data about Russian film distribution.

### Description of the project

The Ministry of Culture of the Russian Federation is the client for this research. We need to study the Russian film distribution market and identify current trends. We will focus on films that have received government support. We will try to answer the question of how interesting such films are to the viewer. Our task is to preprocess the data and study it in order to find interesting characteristics and dependencies that exist in the Russian film distribution market. We will work with data published on the portal of open data of the Ministry of Culture of the Russian Federation. The data set contains information on film distribution licenses, collections and government support for films, as well as information from the Kinopoisk website.

### Data Description:
The mkrf_movies table contains information from the state licensing registry. One film can have several distribution licenses. The budget column already includes the full amount of government support. The data in this column is only for those films that received government support.

- `title` — movie title;
- `puNumber` — license number;
- `show_start_date` - movie premiere date;
- `type` — movie type;
- `film_studio` - production studio;
- `production_country` - country of origin;
- `director` — director;
- `producer` — producer;
- `age_restriction` - age category;
- `refundable_support` — amount of state support repayable funds;
- `nonrefundable_support` - the amount of non-refundable state support funds;
- `financing_source` - source of government funding;
- `budget` - the total budget of the movie;
- `ratings` — movie rating on KinoPoisk website;
- `genres` — movie genre;
- `box_office` — revenue in RUB.

The `mkrf_shows` table contains information about movie screenings in Russian cinemas.

- `puNumber` — license number;
- `box_office` — revenue in RUB.
