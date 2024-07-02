# SQL assessment

CREATE TABLE artists (
artist_id INT PRIMARY KEY,
name VARCHAR(100) NOT NULL,
country VARCHAR(50) NOT NULL,
birth_year INT NOT NULL
);

CREATE TABLE artworks (
artwork_id INT PRIMARY KEY,
title VARCHAR(200) NOT NULL,
artist_id INT NOT NULL,
genre VARCHAR(50) NOT NULL,
price DECIMAL(10, 2) NOT NULL,
FOREIGN KEY (artist_id) REFERENCES artists(artist_id)
);

CREATE TABLE sales (
sale_id INT PRIMARY KEY,
artwork_id INT NOT NULL,
sale_date DATE NOT NULL,
quantity INT NOT NULL,
total_amount DECIMAL(10, 2) NOT NULL,
FOREIGN KEY (artwork_id) REFERENCES artworks(artwork_id)
);

INSERT INTO artists (artist_id, name, country, birth_year) VALUES
(1, 'Vincent van Gogh', 'Netherlands', 1853),
(2, 'Pablo Picasso', 'Spain', 1881),
(3, 'Leonardo da Vinci', 'Italy', 1452),
(4, 'Claude Monet', 'France', 1840),
(5, 'Salvador Dalí', 'Spain', 1904);

INSERT INTO artworks (artwork_id, title, artist_id, genre, price) VALUES
(1, 'Starry Night', 1, 'Post-Impressionism', 1000000.00),
(2, 'Guernica', 2, 'Cubism', 2000000.00),
(3, 'Mona Lisa', 3, 'Renaissance', 3000000.00),
(4, 'Water Lilies', 4, 'Impressionism', 500000.00),
(5, 'The Persistence of Memory', 5, 'Surrealism', 1500000.00);

INSERT INTO sales (sale_id, artwork_id, sale_date, quantity, total_amount) VALUES
(1, 1, '2024-01-15', 1, 1000000.00),
(2, 2, '2024-02-10', 1, 2000000.00),
(3, 3, '2024-03-05', 1, 3000000.00),
(4, 4, '2024-04-20', 2, 1000000.00);

select _ from artists
select _ from artworks
select \* from sales
--Section 1: 1 mark each
--1.Write a query to display the artist names in uppercase.

```sql
SELECT UPPER(name)  artists;
```

--2.Write a query to find the top 2 highest-priced artworks and the total quantity sold for each.

```sql
SELECT title, price, quantity
FROM artworks JOIN sales ON artworks.artwork_id = sales.artwork_id
ORDER BY price DESC
LIMIT 2;
```

-- 3.Write a query to find the total amount of sales for the artwork 'Mona Lisa'.

```sql
SELECT SUM(total_amount)
FROM artworks JOIN sales ON artworks.artwork_id = sales.artwork_id
WHERE title = 'Mona Lisa';
```

--4.Write a query to extract the year from the sale date of 'Guernica'.

```sql
SELECT EXTRACT(YEAR FROM sale_date)
FROM artworks JOIN sales ON artworks.artwork_id = sales.artwork_id
WHERE title = 'Guernica';
```

--Section 2: 2 marks each
--5.Write a query to find the artworks that have the highest sale total for each genre.

```sql
SELECT genre, MAX(total_amount) as highest_sale_total
FROM artworks JOIN sales ON artworks.artwork_id = sales.artwork_id
GROUP BY genre;
```

--6.Write a query to rank artists by their total sales amount and display the top 3 artists.

```sql
SELECT artists.name, SUM(sales.total_amount) as total_sales
FROM artists JOIN artworks ON artists.artist_id = artworks.artist_id
JOIN sales ON artworks.artwork_id = sales.artwork_id
GROUP BY artists.name
ORDER BY total_sales DESC
LIMIT 3;
```

--7.Write a query to display artists who have artworks in multiple genres.

```sql
SELECT artists.name
FROM artists JOIN artworks ON artists.artist_id = artworks.artist_id
GROUP BY artists.name
HAVING COUNT(DISTINCT artworks.genre) > 1;
```

--8.Write a query to find the average price of artworks for each artists.

```sql
SELECT artists.name, AVG(artworks.price) as avg_price
FROM artists JOIN artworks ON artists.artist_id = artworks.artist_id
GROUP BY artists.name;
```

--9.Write a query to display artists whose birth year is earlier than the average birth year of artists from their country.

```sql
SELECT a.name
FROM artists a
WHERE a.birth_year < (SELECT AVG(b.birth_year) FROM artists b WHERE a.country = b.country);
```

--10.Write a query to find the artists who have sold more artworks than the average number of artworks sold per artist.

```sql
CREATE INDEX idx_artwork_id ON sales(artwork_id);
```

--11.Write a query to find the artists who have created artworks in both 'Cubism' and 'Surrealism' genres.

```sql
SELECT artists.name
FROM artists JOIN artworks ON artists.artist_id = artworks.artist_id
WHERE artworks.genre IN ('Cubism', 'Surrealism')
GROUP BY artists.name
```

--12.Write a query to display artists whose birth year is earlier than the average birth year of artists from their country.

```sql
SELECT a.name
FROM artists a
WHERE a.birth_year < (SELECT AVG(b.birth_year) FROM artists b WHERE a.country = b.country);
```

--13.Write a query to find the artworks that have been sold in both January and February 2024.

```sql
SELECT artworks.title
FROM artworks JOIN sales ON artworks.artwork_id = sales.artwork_id
WHERE EXTRACT(MONTH FROM sales.sale_date) IN (1, 2) AND EXTRACT(YEAR FROM sales.sale_date) = 2024
GROUP BY artworks.title
HAVING COUNT(DISTINCT EXTRACT(MONTH FROM sales.sale_date)) = 2;
```

--14.Write a query to calculate the price of 'Starry Night' plus 10% tax.

```sql
SELECT artwork_id, title, price, price * 1.1 as price_with_tax
FROM artworks
WHERE title = 'Starry Night';
```

--15.Write a query to display the artists whose average artwork price is higher than every artwork price in the 'Renaissance' genre.

```sql
SELECT  artists.name,AVG(artworks.price)
FROM artists
JOIN artworks ON artists.artist_id = artworks.artist_id
GROUP BY artists.name
HAVING  avg_price >(SELECT MAX(price) FROM  artworks WHERE genre = 'Renaissance');
```

--Section 3: 3 Marks Questions
--16.Write a query to find artworks that have a higher price than the average price of artworks by the same artist.

```sql
SELECT a.title, a.price
FROM artworks a
WHERE a.price > (SELECT AVG(b.price)
FROM artworks b
WHERE a.artist_id = b.artist_id);
```

--17. Write a query to find the average price of artworks for each artist and only include artists
--whose average artwork price is higher than the overall average artwork price.

```sql
SELECT c.name, AVG(a.price) as avg_price
FROM artists c
JOIN artworks a ON a.artist_id = c.artist_id
GROUP BY c.name
HAVING AVG(a.price) > (SELECT AVG(price)
FROM artworks);
```

--18.Write a query to create a view that shows artists who have created artworks in multiple genres.

```sql
go
CREATE VIEW MultiGenreArtists AS
SELECT c.name
FROM artists c
JOIN artworks a ON a.artist_id = c.artist_id
GROUP BY c.name
HAVING COUNT(DISTINCT a.genre) > 1;
```

--Section 4: 4 Marks Questions
--19.Write a query to convert the artists and their artworks into JSON format.

```sql
SELECT JSON_OBJECT('name', c.name, 'artwork', JSON_ARRAYAGG(JSON_OBJECT('title', a.title, 'price', a.price)))
FROM artists c
JOIN artworks a ON a.artist_id = c.artist_id
GROUP BY c.name;
```

--20.Write a query to export the artists and their artworks into XML format.

```sql
SELECT XMLElement("artist",
XMLAttributes(c.name AS "name"),
(SELECT XMLAgg(XMLElement("artwork",XMLAttributes(a.title AS "title", a.price AS "price")))FROM artworks a
WHERE a.artist_id = c.artist_id)) FROM artists c;
```

--Section 5: 5 Marks Questions
--21.Create a trigger to log changes to the artworks table into an artworks_log table,
----capturing the artwork_id, title, and a change description.
--SYNTAX

```sql
create trigger triggername
BEFORE/AFTER ON tablename
FOR EACH ROW
BEGIN
--IF NEEDED
DECLARE STMNTS
EXCEPTION HANDLING STMNTS
END
```

- Section 5: 5 Marks Questions
- 1.Create a trigger to log changes to the artworks table into an artworks_log table,
  capturing the artwork_id, title, and a change description.

```sql
CREATE TABLE artworks_log (
    log_id INT AUTO_INCREMENT PRIMARY KEY,
    artwork_id INT NOT NULL,
    title VARCHAR(200) NOT NULL,
    change_description VARCHAR(255) NOT NULL);

GO
CREATE TRIGGER artworks_change_log
AFTER UPDATE ON artworks
FOR EACH ROW
BEGIN
    INSERT INTO artworks_log (artwork_id, title, change_description)
    VALUES (OLD.artwork_id, OLD.title, CONCAT('Updated to: ', NEW.title));
END;
```

- 2.Create a scalar function to calculate the average sales amount for artworks in a given genre and write a query to use this function for 'Impressionism'.

```sql
CREATE FUNCTION averageSalesAmount(genre VARCHAR(50)) RETURNS DECIMAL(10,2)
BEGIN
    DECLARE avg_sales_amount DECIMAL(10,2);
    SELECT AVG(sales.total_amount)
    INTO avg_sales_amount
    FROM artworks
    JOIN sales ON artworks.artwork_id = sales.artwork_id
    WHERE artworks.genre = genre;
    RETURN avg_sales_amount;
END;

SELECT averageSalesAmount('Impressionism') AS avg_sales_amount_impressionism;
```

- 3.Create a stored procedure to add a new sale and update the total sales for the artwork.
  Ensure the quantity is positive, and use transactions to maintain data integrity.

```sql

SYNTAX:CREATE PROCEDURE spnewSalesandUpdates()
AS
BEGIN
END
EXEC spnewSalesandUpdates
----------------------------------
CREATE PROCEDURE AddSaleAndUpdateTotal(@ artwork_id INT, @quantity INT, @total_amount DECIMAL(10,2))
BEGIN
    DECLARE EXIT HANDLER FOR SQLEXCEPTION
    BEGIN
        -- ROLLBACK IF ANY SQL STATEMENT FAILS
        ROLLBACK;
    END;
    START TRANSACTION;
    IF quantity > 0 THEN
        INSERT INTO sales (artwork_id, sale_date, quantity, total_amount)
        VALUES (artwork_id, NOW(), quantity, total_amount);
        UPDATE artworks
        SET total_sales = total_sales + total_amount
        WHERE artwork_id = artwork_id;
    ELSE
        SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Error: Quantity must be positive';
    END IF;
    COMMIT;
END;
```

---

- 4.Create a multi-statement table-valued function (MTVF) to return the total quantity
  sold for each genre and use it in a query to display the results.

```sql
CREATE FUNCTION totalQuantityPerGenre()
RETURNS TABLE
AS
RETURN
(SELECT genre, SUM(quantity) as TotalQuantity
 FROM artworks
JOIN sales ON artworks.artwork_id = sales.artwork_id
GROUP BY genre);
SELECT * FROM totalQuantityPerGenre();
```

- Normalization
- 26.Question: Given the denormalized table ecommerce_data with sample data:

```sql
CREATE TABLE customer
(id PRIMARY KEY,customer_name NOT NULL,customer_email)
CREATE TABLE Customers (
customer_id INT PRIMARY KEY,
customer_name VARCHAR(255) NOT NULL,
customer_email VARCHAR(255) NOT NULL UNIQUE);


CREATE TABLE Products(
Product_name VARCHAR(255) NOT NULL,
product_category INT NOT NULL,
UNIQUE (product_name,product_category)
product_price INT NOT NULL CHECK (product_price >= 0));


CREATE TABLE Orders(
order_date DATE NOT NULL,
order_quantity INT NOT NULL,
order_total_amount INT NOT NULL CHECK (order_total_amount >= 0)
FOREIGN KEY (customer_id) REFERENCES Customers(customer_id));


CREATE TABLE Injuctions (
    injuction_id INT PRIMARY KEY,
    FOREIGN KEY order_id REFERENCES Orders order_id,
    FOREIGN KEY product_id REFERENCES Products product_id);
```

- 27. ER DIAGRAM

      ![alt text](image-61.png)
