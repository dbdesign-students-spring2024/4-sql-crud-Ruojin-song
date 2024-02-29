# Report for SQL CRUD Workshop

## Part 1: Restaurant finder
Design a database table named `restaurants` that would allow an application that uses it to find restaurants and a table named `reviews` that would hold reviews for any restaurant.
### Practice Data
Here is my practive data which is a mock data named [restaurants.csv](https://github.com/dbdesign-students-spring2024/4-sql-crud-Ruojin-song/blob/main/data/restaurants.csv). In my practice data, there are 9 cloumns including: 

1. ID
2. Name (name of restaurants)
3. Category (e.g. Chinese, Thai)
4. Price Tier (divided into three different types: Cheap, Medium, and Expensive)
5. 	Neighborhood (neighborhood where restaurants are located in New York)
6. Opening Hours (From 8:00-12:30)
7. Closing Hours (at least 6 hours after opening hours)
8. Average Rating (integer from 0 to 5)
9. Good for Kids (True or False)

In addition, there are 1000 rows in the practice data.

### SQL Commands to Create Tables
When I already have my practice data, I open my terminal and get started. 
Firstly, I create a database called restaurants.db.
```
sqlite3 restaurants.db
```
Then I create two tables: `restaurants` and `reviews` in it.
```sql
CREATE TABLE restaurants (
    id INTEGER PRIMARY KEY,
    name TEXT UNIQUE NOT NULL,
    category TEXT,
    price_tier TEXT,
    neighborhood TEXT,
    opening_hours TEXT,
    closing_hours TEXT,
    average_rating REAL,
    good_for_kids BOOLEAN
);
```
```sql
CREATE TABLE reviews (
    review_id INTEGER PRIMARY KEY,
    restaurant_id INTEGER,
    review_text TEXT,
    rating INTEGER,
    FOREIGN KEY (restaurant_id) REFERENCES restaurants(id)
);
```
Then I import my practice data restaurants.csv into it. 
```sql
.mode csv
.import /Users/apple/Desktop/workshop_sql/4-sql-crud-Ruojin-song/data/restaurants.csv restaurants
```
### SQL Queries

**Task 1: Find all cheap restaurants in a particular neighborhood (pick any neighborhood as an example).**

I want to find all cheap restaurants in a neighborhood named "Lower East Side"
```sql
SELECT * FROM restaurants WHERE price_tier = 'Cheap' AND neighborhood = 'Lower East Side';
```
Here is a part of the result of this queries:
| id | name             | category   | price_tier | neighborhood    | opening_hours | closing_hours | average_rating | good_for_kids |
|----|-----------------|------------|------------|-----------------|---------------|---------------|----------------|---------------|
| 18   | Bamboo Grove Pub | Japanese   | Cheap      | Lower East Side | 08:30         | 18:30         | 1.0            | False          |
| 109 | Juicy Taphouse  | Italian    | Cheap      | Lower East Side | 12:00         | 21:00         | 3.0            | True           |
| 110 | Charming Bakery Cafe | Chinese | Cheap | Lower East Side | 12:30         | 21:30         | 3.0            | False          |
| 210 | Quaint Boulangerie | Indian   | Cheap | Lower East Side | 11:00         | 23:00         | 4.0            | True           |
| 214 | Moonlight Joint | Indian     | Cheap      | Lower East Side | 12:00         | 23:00         | 5.0            | False          |



**Task 2: Find all restaurants in a particular genre (pick any genre as an example) with 3 stars or more, ordered by the number of stars in descending order.**

I want to find Chinese food with 3 stars or more and ordered by the number of stars in descending order.

Here is SQL code:
```sql
SELECT * FROM restaurants WHERE category = 'Chinese' AND average_rating >= 3 ORDER BY average_rating DESC;
```



Here is a part of result of this queries:
| id | name             | category   | price_tier | neighborhood    | opening_hours | closing_hours | average_rating | good_for_kids |
|----|-----------------|------------|------------|-----------------|---------------|---------------|----------------|---------------|
| 864 | Bamboo Grove Kitchenette | Chinese | Expensive | Jamaica | 10:00         | 16:30         | 5.0            | False          |
| 964 | Exquisite Brasserie | Chinese | Expensive | Tribeca | 11:30         | 21:30         | 5.0            | False          |
| 115 | Quaint Cantina | Chinese | Cheap | Upper East Side | 11:00         | 21:30         | 4.0            | False          |
| 153 | Sapphire Juice Joint | Chinese | Cheap | Tompkinsville | 12:30         | 23:00         | 4.0            | True           |
| 385 | Yummy Tavern | Chinese | Expensive | Astoria | 12:30         | 19:00         | 4.0            | False          |


**Task 3: Find all restaurants that are open now.**

My current time is 7:24 pm. So I want to find all restaurants that are open now. 

Here is SQL code:
```sql
SELECT * FROM restaurants WHERE strftime('%H:%M', 'now', 'localtime') BETWEEN opening_hours AND closing_hours;
```
Here is a part of result of this queries:
| id | name             | category   | price_tier | neighborhood    | opening_hours | closing_hours | average_rating | good_for_kids |
|----|-----------------|------------|------------|-----------------|---------------|---------------|----------------|---------------|
| 2    | Zesty Palace    | Indian     | Medium     | Tribeca      | 11:30         | 21:00         | 3.0            | True           |
| 3    | Urban Brasserie | French     | Medium     | Great Kills  | 11:00         | 19:30         | 3.0            | True           |
| 6    | Harbor View Grill | Indian     | Medium     | Crown Heights | 08:30         | 20:00         | 2.0            | False          |
| 7    | Zen Speakeasy   | Thai       | Cheap      | Throgs Neck  | 09:30         | 21:30         | 0.0            | False          |
| 9    | Serenity Juice Bar | Italian   | Cheap      | Forest Hills | 11:30         | 21:30         | 1.0            | False          |

**Task 4: Leave a review for a restaurant (pick any restaurant as an example; note that leaving a review has no automatic effect on the average rating of the restaurant).**

I want to leave a review 'Great ambiance and delicious food!' and 4 stars for the restaurant "Rustic Ramen House" in reviews table.

Here is SQL code:

```sql
INSERT INTO reviews (restaurant_id, review_text, rating) VALUES ((SELECT id FROM restaurants WHERE name = 'Rustic Ramen House'), 'Great ambiance and delicious food!', 4);
```

Here is the result in reviews table:
| review_id | restaurant_id | review_text                             | rating |
|-----------|---------------|---------------------------------------|--------|
| 1         | 24           | Great ambiance and delicious food!      | 4      |

**Task 5: Delete all restaurants that are not good for kids.**

Here is SQL code:
```sql
DELETE FROM restaurants WHERE good_for_kids = 0;
```

**Task 6: Find the number of restaurants in each NYC neighborhood.**

 Here is SQL code:
 ```sql
SELECT neighborhood, COUNT(*) AS number_of_restaurants FROM restaurants GROUP BY neighborhood;
 ```
 Here is a part of result:
 | neighborhood | number_of_restaurants |
|--------------|-----------------------|
| Annadale     | 15                 |
| Astoria      | 31                    |
| Bedford-Stuyvesant | 27 |
| Belmont      | 19                    |
| Brooklyn Heights | 32 |
| Bushwick      | 23                    |
| Chelsea      | 22                    |


## Part 2: Social media app





