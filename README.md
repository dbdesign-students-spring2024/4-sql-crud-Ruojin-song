# Report for SQL CRUD Workshop

## Part 1: Restaurant finder

### Practice Data
Here is my practive data which is a mock data named [restaurants.csv](https://github.com/dbdesign-students-spring2024/4-sql-crud-Ruojin-song/blob/main/data/restaurants.csv). In my practice data, there are 9 cloumns including: 

1. ID
2. Name (name of restaurants)
3. Category (e.g. Chinese)
4. Price_Tier (divided into three different types: Cheap, Medium, and Expensive)
5. 	Neighborhood (neighborhood where restaurant is located)
6. Opening Hours
7. Closing Hours
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
```
CREATE TABLE restaurants (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    category TEXT,
    price_tier TEXT,
    neighborhood TEXT,
    opening_hours TEXT,
    closing_hours TEXT,
    average_rating REAL,
    good_for_kids BOOLEAN
);
```
```
CREATE TABLE reviews (
    review_id INTEGER PRIMARY KEY,
    restaurant_id INTEGER,
    review_text TEXT,
    rating INTEGER,
    FOREIGN KEY (restaurant_id) REFERENCES restaurants(id)
);
```
Then I import my practice data restaurants.csv into it. 
```
.mode csv
.import /Users/apple/Desktop/workshop_sql/4-sql-crud-Ruojin-song/data/restaurants.csv restaurants
```
### SQL Queries

**Task 1: Find all cheap restaurants in a particular neighborhood (pick any neighborhood as an example).**

I want to find all cheap restaurants in a neighborhood named "Lower East Side"
```
SELECT * FROM restaurants WHERE price_tier = 'Cheap' AND neighborhood = 'Lower East Side';
```
Here is a part of the result of this queries:
| id | name             | category   | price_tier | neighborhood    | opening_hours | closing_hours | average_rating | good_for_kids |
|----|-----------------|------------|------------|-----------------|---------------|---------------|----------------|---------------|
| 2  | Charming Cafe    | Mexican    | Cheap      | Lower East Side | 15:30         | 17:30         | 4.0            | True          |
| 29 | Moonlight Wine Bar | French | Cheap      | Lower East Side | 20:30         | 21:00         | 2.0            | True          |
| 39 | Scrumptious Court | Japanese | Cheap      | Lower East Side | 17:30         | 19:00         | 1.0            | False         |
| 54 | Ivy League Seafood Shack | American | Cheap      | Lower East Side | 18:00         | 19:00         | 5.0            | True          |
| 89 | Hilltop Taproom  | Mediterranean | Cheap     | Lower East Side | 21:30         | 22:00         | 3.0            | False         |


**Task 2: Find all restaurants in a particular genre (pick any genre as an example) with 3 stars or more, ordered by the number of stars in descending order.**

I want to find Chinese food with 3 stars or more and ordered by the number of stars in descending order.

Here is SQL code:
```
SELECT * FROM restaurants WHERE category = 'Chinese' AND average_rating >= 3 ORDER BY average_rating DESC;
```



Here is a part of result of this queries:
| id | name             | category   | price_tier | neighborhood    | opening_hours | closing_hours | average_rating | good_for_kids |
|----|-----------------|------------|------------|-----------------|---------------|---------------|----------------|---------------|
| 692 | Irresistible Alehouse | Chinese | Medium      | Fordham         | 18:30         | 19:00         | 5.0            | True          |
| 731 | Authentic Tapas Bar | Chinese | Medium      | Pelham Bay      | 20:00         | 21:30         | 5.0            | False         |
| 18 | Wholesome Parlor   | Chinese | Expensive  | Tompkinsville   | 12:00         | 13:00         | 4.0            | False         |
| 50 | Crisp Frozen Yogurt Shop | Chinese | Cheap      | Williamsburg    | 18:00         | 20:30         | 4.0            | False         |
| 87 | Coastal Cider House | Chinese | Medium      | Forest Hills    | 14:30         | 15:30         | 4.0            | False         |

**Task 3: Find all restaurants that are open now.**

My current time is 9:02 pm. So I want to find all restaurants that are open now. 

Here is SQL code:
```
SELECT * FROM restaurants WHERE strftime('%H:%M', 'now', 'localtime') BETWEEN opening_hours AND closing_hours;
```
Here is a part of result of this queries:
| id | name             | category   | price_tier | neighborhood    | opening_hours | closing_hours | average_rating | good_for_kids |
|----|-----------------|------------|------------|-----------------|---------------|---------------|----------------|---------------|
| 5  | Irresistible Juice Joint | Thai | Medium      | SoHo            | 21:00         | 22:00         | 4.0            | True          |
| 6  | Aromatic Grill   | Mediterranean | Medium | Flatbush       | 19:00         | 21:30         | 4.0            | False         |
| 16 | Oceanic Court    | Japanese | Expensive  | Greenwich Village| 20:00         | 21:30         | 1.0            | True          |
| 17 | Fresh Tea Room   | Indian     | Medium      | Westerleigh     | 21:00         | 22:30         | 4.0            | False         |
| 30 | Maplewood Cuisine | Japanese | Medium      | Belmont         | 20:30         | 21:30         | 0.0            | True          |

**Task 4: Leave a review for a restaurant (pick any restaurant as an example; note that leaving a review has no automatic effect on the average rating of the restaurant).**

I want to leave a review 'Great ambiance and delicious food!' and 4 stars for the restaurant "Classic Burger Joint" in reviews table.

Here is SQL code:

```
INSERT INTO reviews (restaurant_id, review_text, rating) VALUES ((SELECT id FROM restaurants WHERE name = 'Classic Burger Joint'), 'Great ambiance and delicious food!', 4);
```

Here is the result in reviews table:
| review_id | restaurant_id | review_text                             | rating |
|-----------|---------------|---------------------------------------|--------|
| 1         | 1             | Great ambiance and delicious food!      | 4      |

**Task 5: Delete all restaurants that are not good for kids.**

Here is SQL code:
```
DELETE FROM restaurants WHERE good_for_kids = 0;
```

**Task 6: Find the number of restaurants in each NYC neighborhood.**

 Here is SQL code:
 ```
 SELECT neighborhood, COUNT(*) FROM restaurants GROUP BY neighborhood;
 ```
 Here is a part of result:
 | neighborhood | Number of Restaurants |
|--------------|-----------------------|
| Annadale     | 27                    |
| Astoria      | 19                    |
| Bedford-Stuyvesant | 29 |
| Belmont      | 17                    |
| Brooklyn Heights | 21 |
| Bushwick      | 11                    |
| Chelsea      | 22                    |
| Crown Heights | 28                    |
| Eltingville  | 32                    |
| Flatbush     | 29                    |

## Part 2: Social media app





