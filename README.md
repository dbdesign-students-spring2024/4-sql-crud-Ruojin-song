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

```sql
sqlite3 socialmedia1.db
```
```sql
CREATE TABLE users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    email TEXT UNIQUE NOT NULL,
    password TEXT NOT NULL,
    handle TEXT UNIQUE NOT NULL
);
```
```sql
.mode csv
.import /Users/apple/Desktop/workshop_sql/4-sql-crud-Ruojin-song/data/users.csv users
```
```sql
CREATE TABLE posts (
    user_id INTEGER NOT NULL,
    recipient_id INTEGER,
    content TEXT NOT NULL,
    post_type TEXT NOT NULL,
    created_at DATETIME,
    viewed BOOLEAN,
    FOREIGN KEY (user_id) REFERENCES users(id),
    FOREIGN KEY (recipient_id) REFERENCES users(id)
);

```
```sql
.mode csv
.import /Users/apple/Desktop/workshop_sql/4-sql-crud-Ruojin-song/data/posts.csv posts
```

**Task 1: Register a new User**

I want to register a new user which has email: rs8050@nyu.edu, password: Srjzdhpla1, and username: Ruojinya.

Here is my sql code:
```sql
INSERT INTO users (email, password, handle) VALUES ('rs8050@nyu.edu', 'Srjzdhpla1/', 'Ruojinya');
```
Here is the result:
| id | email            | password    | handle    |
|---------|------------------|-------------|-----------|
| 1001    | rs8050@nyu.edu   | Srjzdhpla1/ | Ruojinya  |

**Task 2: Create a new Message sent by a particular User to a particular User (pick any two Users for example)**

I create a message "Hello there!" sent by user with id 1 to the user with id 2. The time is the current New York time which is 2024-02-28 23:06:03.

Here is the sql code:
```sql
INSERT INTO posts (user_id, recipient_id, content, post_type, created_at) 
VALUES (1, 2, 'Hello there!', 'message', datetime('now', 'localtime'));

```
Here is the result:
| user_id | recipient_id | content    | post_type |  created_at          |viewed|
|---------|--------------|------------|-----------|---------------------|--|
| 1            | 2          | Hello there! | message   | 2024-02-28 23:06:03||

**Task 3: Create a new Story by a particular User (pick any User for example).**

I create a story "Check out my new story!" posted by user with id 1 at the current New York time which is 2024-02-28 23:10:11.

Here is my sql code:
```sql
INSERT INTO posts (user_id, content, post_type,created_at,viewed) 
VALUES (1, 'Check out my new story!', 'story', datetime('now', 'localtime'),False);
```
Here is the result:
| user_id | recipient_id | content                    | post_type | created_at          | viewed|
|---------|--------------|----------------------------|------------|---------------------|--|
| 1       |              | Check out my new story!   | story     | 2024-02-28 23:10:11|

**Task 4: Show the 10 most recent visible Messages and Stories, in order of recency.**

I select stories posted within 24 hours and unviewed messages, and list the 10 closest to the current time.

Here is my sql code: 
```sql
SELECT * FROM posts
WHERE (post_type = 'message' AND viewed = 0) OR (post_type = 'story' AND ROUND((JULIANDAY('now') - JULIANDAY(created_at)) * 24)<24) ORDER BY created_at DESC 
LIMIT 10;
```
_Tips: I noticed there were no `messages` on the list and at first I thought it might be a random accident. Finally, I discovered that the value stored in the viewed column is a string and not a boolean and cannot be positioned using `viewed=0`. Since I am using python to generate this data and I spent a long time checking where is the error and ended up failing, I decided to change the data type of this column in sqlite. These are the steps I used to change the data type._

```sql
-- Step 1: Add a new column with the desired data type
ALTER TABLE posts ADD COLUMN viewed_new BOOLEAN;

-- Step 2: Update the new column with the values from the old column
UPDATE posts 
SET viewed_new = CASE WHEN viewed = 'False' THEN 0 ELSE 1 END;

-- Step 3: Drop the old column
ALTER TABLE posts DROP COLUMN viewed;

-- Step 4: Rename the new column to match the old column name
ALTER TABLE posts RENAME COLUMN viewed_new TO viewed;
```
Here is the result:
| user_id | recipient_id | content                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | post_type | created_at          | viewed |
|---------|---------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------|----------------------|--------|
| 1       |         | Check out my new story!                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  | story     | 2024-02-28 23:10:11  | 0      |
| 689 | 0         | Error illo eum ipsam laborum iusto voluptas, tempora labore alias voluptatum magni maxime reiciendis consequatur odit inventore, ullam unde est harum eaque praesentium, illum asperiores adipisci quaerat ut, tempore corporis ea dolore sed accusamus quis nihil excepturi? Expedita tenetur voluptate non totam, quasi ut nam atque sint doloremque eius facilis dolorum sed voluptas. | story   | 2024-02-28 22:57:04 | 0      |
| 282 | 0         | Natus similique itaque blanditiis alias ullam libero, omnis dolores tempore iste ex libero ipsa magnam officia, accusamus quisquam impedit tempore repudiandae, assumenda nisi odio eligendi dolorem. Id aspernatur aliquid pariatur doloribus, vitae vero nostrum voluptatem impedit deleniti libero exercitationem dolor provident, sunt voluptate eaque odit vitae vel magni distinctio, recusandae ducimus fugiat quam provident tenetur aspernatur nemo reiciendis vel iusto, esse quidem nesciunt magnam aut impedit eum aperiam. Ipsam expedita libero cupiditate aut enim consectetur mollitia assumenda quibusdam, iure consequatur vel velit sequi natus culpa nulla molestiae impedit error non, perspiciatis odit qui dolore vero quibusdam odio a aspernatur. | story   | 2024-02-28 22:57:04 | 0      |
| 497 | 0         | Vero praesentium quas consequuntur, eius praesentium aliquid id, inventore laborum culpa vero neque ipsam mollitia? Saepe nulla rem repellendus facilis sit perspiciatis est minima doloremque, repellendus distinctio inventore ad nihil laboriosam provident hic laborum dicta. Quo magnam at natus voluptatum saepe, libero aliquam asperiores suscipit officia molestias aperiam dolor tenetur corporis iusto animi, repudiandae hic quidem eius beatae ad aperiam saepe.                                                                                                                           | story   | 2024-02-28 22:57:04 | 0      |
| 292 | 187       | Fugiat itaque vero nihil, vitae corporis iusto laboriosam nobis illo inventore unde quis tenetur? Rerum maiores ea odit obcaecati non dolorem tempora laudantium ipsam, adipisci accusamus eum modi corporis commodi eveniet tenetur perferendis, quidem sequi sunt pariatur omnis esse praesentium explicabo nostrum nobis mollitia, a sapiente voluptatum sit ut nisi suscipit esse numquam aperiam repellat? Commodi minus voluptatibus ratione amet, inventore vel voluptatem quisquam vero facere obcaecati accusantium.                                                                                                | message | 2024-02-28 22:57:04 | 0      |
| 577 | 0         | Ullam harum rerum consectetur, rem facere molestias ea quisquam sed rerum, accusantium odio earum quidem porro ex sint quibusdam adipisci laborum voluptatem, officiis consequuntur officia quibusdam quis ullam hic reiciendis natus, expedita illum at eveniet a asperiores eligendi praesentium voluptates doloribus culpa impedit. Labore nihil magni a odio ab iste nisi ea sunt, veniam libero consequatur ea voluptas corporis et officia quibusdam commodi, dicta harum quae ratione sunt ullam. Consequatur quas illum distinctio id, reiciendis cupiditate quia tenetur aut expedita, aspernatur praesentium modi nam ex, ipsum omnis unde deleniti optio dolores facilis esse praesentium repellat, natus amet veritatis facere aliquid iste possimus ipsum commodi in debitis culpa?              | story   | 2024-02-28 22:50:53 | 0      |
| 86  | 0         | Architecto labore ullam possimus magnam aut culpa tenetur, corrupti error a nam dolores consequatur asperiores repudiandae ipsum. Maiores eaque molestias et nihil excepturi, assumenda ab enim, temporibus ad inventore modi tenetur libero nemo cupiditate fugiat. Explicabo quae id adipisci excepturi vel reiciendis, pariatur culpa error delectus, ea similique sunt ipsa vitae expedita porro adipisci, ad saepe nihil consectetur quos accusantium numquam. Voluptatibus cum aspernatur beatae voluptatem corrupti earum reprehenderit porro commodi fugit, odio cum officiis sequi provident omnis, veniam unde saepe officia in ut a quibusdam libero eligendi qui natus, corporis voluptatem consequatur cupiditate voluptate vitae neque vel distinctio, asperiores assumenda necessitatibus eveniet voluptatem.                                           | story   | 2024-02-28 18:04:12 | 0      |
| 260 | 0         | Doloribus tenetur sed numquam placeat aliquam illum saepe ad architecto amet, placeat asperiores corrupti veritatis blanditiis eaque error, facere ab ipsa omnis nisi nemo repellat? Doloribus dolor corporis omnis dignissimos totam molestiae eius quae, vel voluptas tenetur ipsam modi dignissimos provident quis possimus ratione, consequuntur provident beatae minima molestiae omnis voluptate aspernatur dolorum? Aspernatur numquam harum impedit expedita quos veritatis perspiciatis nihil recusandae itaque error, inventore iusto blanditiis corrupti odio? Porro unde est libero reiciendis sapiente autem, voluptatem rem possimus deleniti, incidunt id totam nisi esse error nulla atque odit ad? | story   | 2024-02-28 17:56:46 | 0      |
| 89  | 0         | Voluptate ex explicabo voluptates ipsum, reprehenderit odio perferendis ipsa maiores deserunt, ratione cupiditate porro dolores facilis non nemo, perferendis cum qui. Vel cumque est repellat saepe possimus asperiores ducimus veritatis laborum nemo rem,                                                                                                                                                  | story   | 2024-02-28 16:51:20 | 0      |
| 548 | 0         | Fugiat velit exercitationem accusantium sit alias iste nostrum minima quibusdam aliquam ducimus, ea laudantium nostrum recusandae deserunt ipsa, ex veniam autem numquam voluptates. Minus quae assumenda harum saepe, modi quibusdam quidem quis dolor sint eaque cupiditate ipsum, nesciunt recusandae est placeat repellat ea beatae, doloribus nam sed nisi ipsam facere, voluptatem fugit porro ipsa nostrum ab rem temporibus reiciendis? | story   | 2024-02-28 15:31:03 | 0      |




**Task 5: Show the 10 most recent visible Messages sent by a particular User to a particular User (pick any two Users for example), in order of recency.**

I want to show the 10 most recent visible messages sent by user with id 411 to user with id 51 ordered by decreasing time.

```sql
SELECT * FROM posts
WHERE post_type = 'message' AND viewed = 0 AND user_id = 441 AND recipient_id = 51
ORDER BY created_at DESC
LIMIT 10;
```
Here is the result:
| user_if  | recipient_id | content                                                                                                                                                                                                                                                                                                                                                                                                  | post_type    |    created_at             | viewed |
|-----|-----------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------|---------------------|--------|
| 441 | 51        | Magni ullam perspiciatis dolorum eaque deleniti aperiam velit reiciendis obcaecati vel, facere nihil quidem saepe corporis sequi eum quasi culpa dignissimos enim neque, accusamus commodi atque iusto incidunt expedita molestias, minus enim ipsa debitis, nemo provident sunt animi pariatur veniam non assumenda sint. Maxime repellendus dolorum eveniet unde qui mollitia neque, expedita facere magnam harum laboriosam deserunt dolore enim. | message | 2024-02-11 07:43:46 | 0      |

tips: unfortunately, these two people only have one message among the 1000 columns.

**Task6: Make all Stories that are more than 24 hours old invisible.**

Firstly, I created a new column called visible to represent whether the message or story are still visible (0 is invisible and 1 is visible). Then I use `UPDATE` to update the value. For story, if it is more than 24 hours, the corresponding value will be 0. Same, for message, if it is viewed, the corresponding value will also be 0. 

The current time is 2024-02-29 01:00:00
```sql
ALTER TABLE posts ADD COLUMN visible BOOLEAN DEFAULT TRUE;

UPDATE posts
SET visible = 0
WHERE post_type = 'story' AND ROUND((JULIANDAY('now') - JULIANDAY(created_at)) * 24)>24;

UPDATE posts
SET visible = 0
WHERE post_type = 'message' AND viewed = 1;
```
**Task 7: Show all invisible Messages and Stories, in order of recency.**

Here is my sql code:
```sql
SELECT * FROM posts
WHERE (post_type = 'message' AND visible = 0) OR (post_type = 'story' AND visible = 0)
ORDER BY created_at DESC;
```
Here is part of the result:
| id  | user_id | content                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | post_type | timestamp           | viewed | visible |
| --- | ------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------- | ------------------- | ------ | ------- |
| 689 | 0       | Error illo eum ipsam laborum iusto voluptas, tempora labore alias voluptatum magni maxime reiciendis consequatur odit inventore, ullam unde est harum eaque praesentium, illum asperiores adipisci quaerat ut, tempore corporis ea dolore sed accusamus quis nihil excepturi? Expedita tenetur voluptate non totam, quasi ut nam atque sint doloremque eius facilis dolorum sed voluptas.                                                                                                                                                                                                                                                                                           | story     | 2024-02-28 22:57:04 | 0      | 0       |
| 394 | 344     | Cumque amet sapiente, ad perspiciatis commodi accusantium voluptate neque, maxime ipsam obcaecati velit sed? Assumenda maxime excepturi officiis totam, atque obcaecati hic ad, blanditiis voluptatem voluptatum nihil quisquam deleniti temporibus tempora optio, fugiat sapiente provident perferendis saepe error, quis perspiciatis corporis mollitia voluptatum quisquam tempore excepturi fugit dolores minima? Obcaecati culpa et distinctio ab tenetur voluptate, nesciunt distinctio ullam fugit quis? Sint pariatur alias, ad similique nemo sapiente amet eligendi deserunt quisquam sequi eum dolor, dolor fuga veniam odit amet beatae vitae, excepturi nisi quod ad mollitia eius maxime eos? | message   | 2024-02-28 22:57:04 | 1      | 0       |
| 282 | 0       | Natus similique itaque blanditiis alias ullam libero, omnis dolores tempore iste ex libero ipsa magnam officia, accusamus quisquam impedit tempore repudiandae, assumenda nisi odio eligendi dolorem. Id aspernatur aliquid pariatur doloribus, vitae vero nostrum voluptatem impedit deleniti libero exercitationem dolor provident, sunt voluptate eaque odit vitae vel magni distinctio, recusandae ducimus fugiat quam provident tenetur aspernatur nemo reiciendis vel iusto, esse quidem nesciunt magnam aut impedit eum aperiam. Ipsam expedita libero cupiditate aut enim consectetur mollitia assumenda quibusdam, iure consequatur vel velit sequi natus culpa nulla molestiae impedit error non, perspiciatis odit qui dolore vero quibusdam odio a aspernatur.                                                                                                  | story     | 2024-02-28 22:57:04 | 0      | 0       |
| 497 | 0       | Vero praesentium quas consequuntur, eius praesentium aliquid id, inventore laborum culpa vero neque ipsam mollitia? Saepe nulla rem repellendus facilis sit perspiciatis est minima doloremque, repellendus distinctio inventore ad nihil laboriosam provident hic laborum dicta. Quo magnam at natus voluptatum saepe, libero aliquam asperiores suscipit officia molestias aperiam dolor tenetur corporis iusto animi, repudiandae hic quidem eius beatae ad aperiam saepe.                                                                                                                                                                                                                                                                                                           | story     | 2024-02-28 22:57:04 | 0      | 0       |
| 292 | 187     | Fugiat itaque vero nihil, vitae corporis iusto laboriosam nobis illo inventore unde quis tenetur? Rerum maiores ea odit obcaecati non dolorem tempora laudantium ipsam, adipisci accusamus eum modi corporis commodi eveniet tenetur perferendis, quidem sequi sunt pariatur omnis esse praesentium explicabo nostrum nobis mollitia, a sapiente voluptatum sit ut nisi suscipit esse numquam aperiam repellat? Commodi minus voluptatibus ratione amet, inventore vel voluptatem quisquam vero facere obcaecati accusantium.                                                                                                                                                                                                                                                                      | message   | 2024-02-28 22:57:04 | 0      | 0       |
| 577 | 0       | Ullam harum rerum consectetur, rem facere molestias ea quisquam sed rerum, accusantium odio earum quidem porro ex sint quibusdam adipisci laborum voluptatem, officiis consequuntur officia quibusdam quis ullam hic reiciendis natus, expedita illum at eveniet a asperiores eligendi praesentium voluptates doloribus culpa impedit. Labore nihil magni a odio ab iste nisi ea sunt, veniam libero consequatur ea voluptas corporis et officia quibusdam commodi, dicta harum quae ratione sunt ullam. Consequatur quas illum distinctio id, reiciendis cupiditate quia tenetur aut expedita, aspernatur praesentium modi nam ex, ipsum omnis unde deleniti optio dolores facilis esse praesentium repellat, natus amet veritatis facere aliquid iste possimus ipsum commodi in debitis culpa?                                                                                                                           | story     | 2024-02-28 22:50:53 | 0      | 0       |
| 86  | 0       | Architecto labore ullam possimus magnam aut culpa tenetur, corrupti error a nam dolores consequatur asperiores repudiandae ipsum. Maiores eaque molestias et nihil excepturi, assumenda ab enim, temporibus ad inventore modi tenetur libero nemo cupiditate fugiat. Explicabo quae id adipisci excepturi vel reiciendis, pariatur culpa error delectus, ea similique sunt ipsa vitae expedita porro adipisci, ad saepe nihil consectetur quos accusantium numquam. Voluptatibus cum aspernatur beatae voluptatem corrupti earum reprehenderit porro commodi fugit, odio cum officiis sequi provident omnis, veniam unde saepe officia in ut a quibusdam libero eligendi qui natus, corporis voluptatem consequatur cupiditate voluptate vitae neque vel distinctio, asperiores assumenda necessitatibus eveniet voluptatem.                                                                                                                                                 | story     | 2024-02-28 18:04:12 | 0      | 0       |
| 260 | 0       | Doloribus tenetur sed numquam placeat aliquam illum saepe ad architecto amet, placeat asperiores corrupti veritatis blanditiis eaque error, facere ab ipsa omnis nisi nemo repellat? Doloribus dolor corporis omnis dignissimos totam molestiae eius quae, vel voluptas tenetur ipsam modi dignissimos provident quis possimus ratione, consequuntur provident beatae minima molestiae omnis voluptate aspernatur dolorum? Aspernatur numquam harum impedit expedita quos veritatis perspiciatis nihil recusandae itaque error, inventore iusto blanditiis corrupti odio? Porro unde est libero reiciendis sapiente autem, voluptatem rem possimus deleniti, incidunt id totam nisi esse error nulla atque odit ad?                                                                                                                                          | story     | 2024-02-28 17:56:46 | 0      | 0       |
| 89  | 0       | Voluptate ex explicabo voluptates ipsum, reprehenderit odio perferendis ipsa maiores deserunt, ratione cupiditate porro dolores facilis non nemo, perferendis cum qui. Vel cumque est repellat saepe possimus asperiores ducimus veritatis laborum nemo rem, voluptatum maxime aliquam cum accusamus ullam veniam voluptate aspernatur esse expedita, quo reprehenderit accusamus molestias quia consequatur dolorum, ex architecto aut ducimus, ducimus similique corrupti ullam laborum itaque facere repellat doloremque repudiandae vitae explicabo.                                                                                                                                                                                                                                                                                               | story     | 2024-02-28 16:51:20 | 0      | 0       |
| 548 | 0       | Fugiat velit exercitationem accusantium sit alias iste nostrum minima quibusdam aliquam ducimus, ea laudantium nostrum recusandae deserunt ipsa, ex veniam autem numquam voluptates. Minus quae assumenda harum saepe, modi quibusdam quidem quis dolor sint eaque cupiditate ipsum, nesciunt recusandae est placeat repellat ea beatae, doloribus nam sed nisi ipsam facere, voluptatem fugit porro ipsa nostrum ab rem temporibus reiciendis?                                                                                                                                                                                                                                                                                                                                                                                                                                                    | story     | 2024-02-28 15:31:03 | 0      | 0       |
| 641 | 0       | Minima aspernatur vero, veniam a natus rerum exercitationem odit mollitia. A ex in repudiandae nam, illum maiores pariatur possimus nostrum cum voluptatibus, itaque aliquid laudantium, debitis nisi reiciendis aliquid omnis et animi numquam earum?                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          | story     | 2024-02-28 13:45:56 | 0      | 0       |

