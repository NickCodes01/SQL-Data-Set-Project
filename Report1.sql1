--Report #1
--Compute min/ max sales quantities, with products, dates and states and average
--I used logic AND
WITH part1 AS -- /* compute aggregates first */
(
        SELECT cust, MIN(quant) min_q, MAX(quant) max_q, round(AVG(quant),0) avg_q
                FROM sales
        GROUP BY cust
        ORDER BY cust
),


part2 AS -- compute the details of MIN_Q based on p1.cust and p1.min_q by joining p1 with sales
(
        SELECT p1.cust, p1.min_q, s.prod min_prod, s.date min_date, s.state min_state, p1.max_q, p1.avg_q
                FROM part1 p1, sales s
        WHERE p1.cust = s.cust AND p1.min_q = s.quant
),


part3 AS -- compute the details of MAX_Q based on p2.cust and p2.max_q by joining p2 with sales
(
        SELECT p2.cust, p2.min_q, p2.min_prod, p2.min_date, p2.min_state, p2.max_q, s.prod max_prod, s.date max_date, s.state max_state, p2.avg_q
                FROM part2 p2, sales s
        WHERE p2.cust = s.cust AND p2.max_q = s.quant
)


        SELECT * FROM part3
        ORDER BY cust




-------------------------------------------------------------------------------------------------------------------------------
--Report #2
--For year/ month combination, find busiest and slowest day with total sales quantities
--I used logic AND, and ON (CH.4)


-- to compute the base information (aggregate function sum(quant) of year, month, and day)
WITH part1 AS
(
        SELECT year, month, day, SUM(quant) total
                FROM sales
        GROUP BY year, month, day
        ORDER BY year, month, day
),


-- to compute the details of Busiest_Total_Q and Slowest_Total_Q
part2 AS
(        --next line, we want the busiest day (s.day) Busiest_Day corresponding to the maximum quantity (p1.max_q)... p1.min_q and p1.total is next step
        SELECT DISTINCT year, month, MAX(total) Busiest_Total_Q, MIN(total) Slowest_Total_Q
                FROM part1 p1
        -- we want matching tables from part1 and sales in regard to year, month, and maximum quant
        GROUP BY year, month
        ORDER BY year, month
),


-- to join part1 and part2 to find the days that correspond to the Busiest_Total_Q and Slowest_Total_Q for each year, month combination
part3 AS
(
        SELECT p2.year, p2.month, p1.day Busiest_Day, p2.Busiest_Total_Q, part3.day Slowest_Day, p2.Slowest_Total_Q
                FROM part2 p2
        INNER JOIN part1 p1 ON p2.year = p1.year AND p2.month = p1.month AND p2.Busiest_Total_Q = p1.total
        INNER JOIN part1 part3 ON p2.year = part3.year AND p2.month = part3.month AND p2.Slowest_Total_Q = part3.total
)
        SELECT * FROM part3








-------------------------------------------------------------------------------------------------------------------------------
--Report #3
--For each customer, find most favorite product and least favorite product
--I used logic ON (CH.4)


-- to compute the base information (aggregate function) the sum of all quantities of products (eg. how many total eggs, apples, etc per customer)
WITH part1 AS
(
        SELECT cust, prod, SUM(quant) sumQ
                FROM sales
        GROUP BY cust, prod
        ORDER BY cust, prod
),


-- to find the largest and the smallest quantities for each customer
part2 AS
(
        SELECT DISTINCT p1.cust, MAX(sumQ) largest_quantity, MIN(sumQ) smallest_quantity
                FROM part1 p1
        GROUP BY cust
        ORDER BY cust
),


-- to find the most favorite product for each customer
part3_m AS
(
        SELECT DISTINCT p2.cust, p2.largest_quantity, p1.prod Most_Fav_Prod
                FROM part1 p1
        JOIN part2 p2 ON p1.cust = p2.cust
        WHERE p1.sumQ = p2.largest_quantity
),


-- to find the least favorite product for each customer
part3_l AS
(
        SELECT DISTINCT p2.cust, p2.smallest_quantity, p1.prod Least_Fav_Prod
                FROM part1 p1
        JOIN part2 p2 ON p1.cust = p2.cust
        WHERE p1.sumQ = p2.smallest_quantity
),


-- to display a table showing the customer, their most favorite product, and their least favorite product
part4 AS
(
        SELECT p3m.cust, p3m.Most_Fav_Prod, /*p3m.largest_quantity,*/ p3l.Least_Fav_Prod /*,p3l.smallest_quantity*/
                FROM part3_m p3m
        JOIN part3_l p3l ON p3m.cust = p3l.cust
)
        SELECT * FROM part4






-------------------------------------------------------------------------------------------------------------------------------
--Report #4
--For each cust/ prod, show avg sales quant for the four seasons, the avg, total quant, and count
--I used logic AND, IN (CH.3), and ON (CH.4)


WITH part1 AS -- /* compute aggregates first... avg, sum, and count */
(
        SELECT cust, prod, round(AVG(quant),0) average_sales_quant, SUM(quant) total_quant, COUNT(quant) quant_count
                FROM sales
        GROUP BY cust, prod
        ORDER BY 1
),


part2 AS -- compute the details of the average sales quantities for Spring (1, 2, 3), ignoring the year component
(
        SELECT p1.cust, p1.prod, round(AVG(quant),0) Spring_Avg
                FROM part1 p1
        JOIN sales s ON p1.cust = s.cust AND p1.prod = s.prod
        WHERE EXTRACT(month from s.date) IN (1, 2, 3)
        GROUP BY p1.cust, p1.prod
        ORDER BY 1
),


part3 AS -- compute the details of the average sales quantities for Summer (4, 5, 6), ignoring the year component
(
        SELECT p1.cust, p1.prod, round(AVG(quant),0) Summer_Avg
                FROM part1 p1
        JOIN sales s ON p1.cust = s.cust AND p1.prod = s.prod
        WHERE EXTRACT(month from s.date) IN (4, 5, 6)
        GROUP BY p1.cust, p1.prod
        ORDER BY 1
),


part4 AS -- compute the details of the average sales quantities for Fall (7, 8, 9), ignoring the year component
(
        SELECT p1.cust, p1.prod, round(AVG(quant),0) Fall_Avg
                FROM part1 p1
        JOIN sales s ON p1.cust = s.cust AND p1.prod = s.prod
        WHERE EXTRACT(month from s.date) IN (7, 8, 9)
        GROUP BY p1.cust, p1.prod
        ORDER BY 1
),


part5 AS -- compute the details of the average sales quantities for Fall (10, 11, 12), ignoring the year component
(
        SELECT p1.cust, p1.prod, round(AVG(quant),0) Winter_Avg
                FROM part1 p1
        JOIN sales s ON p1.cust = s.cust AND p1.prod = s.prod
        WHERE EXTRACT(month from s.date) IN (10, 11, 12)
        GROUP BY p1.cust, p1.prod
        ORDER BY 1
),


part6 AS -- join the tables to form final table
(
        SELECT p1.cust customer, p1.prod product, p2.Spring_Avg, p3.Summer_Avg, p4.Fall_Avg, p5.Winter_Avg, p1.average_sales_quant, p1.total_quant, p1.quant_count
                FROM part1 p1 
         JOIN part2 p2 ON p1.cust = p2.cust AND p1.prod = p2.prod
         JOIN part3 p3 ON p1.cust = p3.cust AND p1.prod = p3.prod
         JOIN part4 p4 ON p1.cust = p4.cust AND p1.prod = p4.prod
         JOIN part5 p5 ON p1.cust = p5.cust AND p1.prod = p5.prod
)




SELECT * FROM part6




-------------------------------------------------------------------------------------------------------------------------------
--Report #5
--For each prod, find max sales quant for each quarter and corresponding dates
--I used logic AND, IN (CH.3), and ON (CH.4)


WITH part1 AS -- /* use part1 to just hold the products in order  */
(
        SELECT prod
                FROM sales
        GROUP BY prod
        ORDER BY 1
),


-----------------------
--compute Q1_max and corresponding Q1_max_date
part2 AS
(
        SELECT prod, MAX(quant) Q1_max
                FROM sales s
        WHERE EXTRACT(MONTH FROM date) IN (1, 2, 3)
                GROUP BY prod
        
),


part3 AS
(
        SELECT p2.prod, p2.Q1_max, s.date Q1_max_date
                FROM part2 p2
        JOIN sales s ON p2.prod = s.prod
        AND s.quant = p2.Q1_max
        AND EXTRACT (MONTH FROM s.date) IN (1, 2, 3)
        ORDER BY prod


),


-------------------------
--compute Q2_max and corresponding Q2_max_date
part4 AS 
(
        SELECT prod, MAX(quant) Q2_max
                FROM sales s
        WHERE EXTRACT(MONTH FROM date) IN (4, 5, 6)
                GROUP BY prod
        ORDER BY prod
        
),


part5 AS 
(
        SELECT p4.prod, p4.Q2_max, s.date Q2_max_date
                FROM part4 p4
        JOIN sales s ON p4.prod = s.prod
        AND s.quant = p4.Q2_max
        AND EXTRACT(MONTH FROM s.date) IN (4, 5, 6)
        ORDER BY prod


),


--------------------------------------
--compute Q3_max and corresponding Q3_max_date
part6 AS 
(
        SELECT prod, MAX(quant) Q3_max
                FROM sales s
        WHERE EXTRACT(MONTH FROM date) IN (7, 8, 9)
                GROUP BY prod
        ORDER BY prod
),


part7 AS
(
        SELECT p6.prod, p6.Q3_max, s.date Q3_max_date
                FROM part6 p6
        JOIN sales s ON p6.prod = s.prod
        AND s.quant = p6.Q3_max
        AND EXTRACT(MONTH FROM s.date) IN (7, 8, 9)
        ORDER BY prod


),


---------------------------------
--compute Q4_max and corresponding Q4_max_date
part8 AS 
(
        SELECT prod, MAX(quant) Q4_max
                FROM sales s
        WHERE EXTRACT(MONTH FROM date) IN (10, 11, 12)
                GROUP BY prod
        ORDER BY prod
),


part9 AS
(
        SELECT p8.prod, p8.Q4_max, s.date Q4_max_date
                FROM part8 p8
        JOIN sales s ON p8.prod = s.prod
        AND s.quant = p8.Q4_max
        AND EXTRACT(MONTH FROM s.date) IN (10, 11, 12)
        ORDER BY prod




),


part10 AS -- join the tables to form final table
(
        SELECT p1.prod, p2.Q1_max, p2.Q1_max_date, p4.Q2_max, p4.Q2_max_date, p6.Q3_max, p6.Q3_max_date, p8.Q4_max, p8.Q4_max_date
                FROM part1 p1
        JOIN part3 p2 ON p1.prod = p2.prod
        JOIN part5 p4 ON p1.prod = p4.prod
        JOIN part7 p6 ON p1.prod = p6.prod
        JOIN part9 p8 ON p1.prod = p8.prod
)






        SELECT * FROM part10
