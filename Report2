1.
/*
For each customer, product and month, count the number of sales transactions that were between the previous and the following month's average sales quantities. For January and December, display <NULL> or 0. Logic includes ON, BETWEEN, AND, and OR
*/


WITH part1 AS
(
--calculate sales 
        SELECT DISTINCT s.cust, s.prod, s.month, 
--average sales before current month
                round(AVG(s_before.quant),0) avg_sales_before, 
--average sales during current month
/*                round(AVG(s.quant),0) avg_sales_during, */
--average sales after current month
                round(AVG(s_after.quant),0) avg_sales_after


                FROM sales s
--use left joins to get data for months before and after current month
                LEFT JOIN sales s_before ON s.cust = s_before.cust AND s.prod = s_before.prod AND
                s.month = s_before.month + 1


        
                LEFT JOIN sales s_after ON s.cust = s_after.cust AND s.prod = s_after.prod AND 
                s.month = s_after.month - 1


        
--group by must match select clause
                GROUP BY s.cust, s.prod, s.month
        
                ORDER BY s.cust, s.prod, s.month
),


part2 AS
(
        SELECT p1.cust, p1.prod, p1.month, COUNT(quant) sales_count_between_avgs
                FROM part1 p1, sales s
                WHERE p1.month = s.month
                        AND (s.quant BETWEEN p1.avg_sales_before AND p1.avg_sales_after)
                        OR (s.quant BETWEEN p1.avg_sales_after AND p1.avg_sales_before)
                GROUP BY p1.cust, p1.prod, p1.month
                ORDER BY p1.cust, p1.prod, p1.month
        
),


/* part3 adds to the resulting table, the months 1 and 12 as well as their 
corresponding null values in the sales_count_between_avgs columns, information that wasn't present in part 2 */
part3 AS
(
	SELECT DISTINCT p1.cust, p1.prod, p1.month, sales_count_between_avgs
        	FROM part1 p1
        	LEFT JOIN part2 p2 ON p1.cust = p2.cust AND p1.prod = p2.prod AND p1.month = p2.month
        	ORDER BY cust, prod, month
)




        SELECT * FROM part3


-------------------------------------------------------------

2.
/*
For customer and product, show the average sales before, during and after each month (e.g., for February, show average sales of January and March. For “before” January and “after” December, display <NULL> . The “YEAR” attribute is not considered for this query – for example, both January of 2017 and January of 2018 are considered January regardless of the year. Logic includes ON and AND.
*/


WITH part1 AS
(
--calculate sales 
        SELECT DISTINCT s.cust, s.prod, s.month, 
--average sales before current month
                round(AVG(s_before.quant),0) avg_sales_before, 
--average sales during current month
                round(AVG(s.quant),0) avg_sales_during,
--average sales after current month
                round(AVG(s_after.quant),0) avg_sales_after


        FROM sales s
--use left joins to get data for months before and after current month
        LEFT JOIN sales s_before ON s.cust = s_before.cust AND s.prod = s_before.prod AND
        s.month = s_before.month + 1


        
        LEFT JOIN sales s_after ON s.cust = s_after.cust AND s.prod = s_after.prod AND 
        s.month = s_after.month - 1


        
--group by must match select clause
        GROUP BY s.cust, s.prod, s.month
        
        ORDER BY s.cust, s.prod, s.month
)




        SELECT * FROM part1


--------------------------------------------------------------  


3. 
/*
For each customer, product and state combination, compute (1) the product’s average sale of this customer for the state (i.e., the simple AVG for the group-by attributes – this is the easy part), (2) the average sale of the product and the state but for all of the other customers, (3) the customer’s average sale for the given state, but for all of the other products, and (4) the customer’s average sale for the given product, but for all of the other states. Logic includes ON and AND.
*/

--for prod_avg
WITH part1 AS
(
        SELECT cust, prod, state, round(AVG(quant), 0) prod_avg
                FROM sales
                GROUP BY cust, prod, state
                ORDER BY cust, prod, state
),

--for other_cust_avg
part2 AS
(
        SELECT p1.cust, p1.prod, p1.state, round(AVG(s.quant), 0) other_cust_avg
                FROM part1 p1, sales s
                WHERE p1.prod = s.prod AND p1.state = s.state AND p1.cust != s.cust
                GROUP BY p1.cust, p1.prod, p1.state
),

--for other_prod_avg
part3 AS
(
        SELECT p1.cust, p1.prod, p1.state, round(AVG(s.quant), 0) other_prod_avg
                FROM part1 p1, sales s
                WHERE p1.prod != s.prod AND p1.state = s.state AND p1.cust = s.cust
                GROUP BY p1.cust, p1.prod, p1.state
),

--for other_state_avg
part4 AS
(
        SELECT p1.cust, p1.prod, p1.state, round(AVG(s.quant), 0) other_state_avg
                FROM part1 p1, sales s
                WHERE p1.prod = s.prod AND p1.state != s.state AND p1.cust = s.cust
                GROUP BY p1.cust, p1.prod, p1.state
),

--use LEFT JOIN to create final table
part5 AS
(
        SELECT p1.cust, p1.prod, p1.state, p1.prod_avg, p2.other_cust_avg, p3.other_prod_avg, p4.other_state_avg
                FROM part1 p1
                LEFT JOIN part2 p2 ON p1.cust = p2.cust AND p1.prod = p2.prod AND p1.state = p2.state
                LEFT JOIN part3 p3 ON p1.cust = p3.cust AND p1.prod = p3.prod AND p1.state = p3.state
                LEFT JOIN part4 p4 ON p1.cust = p4.cust AND p1.prod = p4.prod AND p1.state = p4.state
)


        SELECT * FROM part5


---------------------------------------------------------

  
4.
/*
For each customer, find the top 3 highest quantities purchased in New Jersey (NJ). Show the customer’s name, the quantity and product purchased, and the date they purchased it. If there are ties, show all – refer to the sample output below. Logic includes AND.
*/


WITH part1 AS
(
--select customer, their top three highest quantities, the corresponding products and dates (if tie, show all ie. could have more than 3 displayed)
        SELECT s.cust, s.quant, s.prod, s.date
                FROM sales s
--only from the state of NJ
                WHERE s.state = 'NJ' AND (SELECT COUNT(DISTINCT s1.quant)
                        FROM sales s1
                        WHERE s1.cust = s.cust AND s1.state = 'NJ' AND s1.quant >= s.quant) 
--only top three highest quantities (less than or equal to 3... <= 3)
        <= 3
--order so table matches assignment and quant is descending making it easier to read
                ORDER BY s.cust, s.quant DESC, s.date
)




        SELECT * FROM part1



----------------------------------------------------------


5.
/*
For each product, find the median sales quantity (assume an odd number of sales for simplicity of presentation). (NOTE – “median” is defined as “denoting or relating to a value or quantity lying at the midpoint of a frequency distribution of observed values or quantities, such that there is an equal probability of falling above or below it.” E.g., Median value of the list {13, 23, 12, 16, 15, 9, 29} is 15. Logic includes AND.
*/

--create base table for all combinations of prod and quant
WITH base AS
(
        SELECT DISTINCT prod, quant
                FROM sales
                ORDER BY 1, 2
),

--for each (prod, quant), compute the “position” --> count (quant), where quant <= current quant.
pos AS
(
        SELECT b.prod, b.quant, COUNT(s.quant) pos
                FROM base b, sales s
                WHERE b.prod = s.prod AND b.quant >= s.quant --where quant is <= current quant
                GROUP BY b.prod, b.quant
),

--find the “median position”
med_pos AS
(
--ceiling is from the help file
        SELECT prod, CEILING(COUNT(quant)/ 2.0) median_pos
                FROM base
                GROUP BY prod
),

--create table from pos and med_pos
meds AS
(
        SELECT p.prod, p.quant, p.pos
                FROM pos p, med_pos m
                WHERE p.prod = m.prod AND p.pos >= m.median_pos
                ORDER BY 1, 2, 3
),

--find the quant corresponding to the MIN(quant)
FINAL AS
(
        SELECT prod, MIN(quant) median_quant
                FROM meds
                GROUP BY prod
)


        SELECT * FROM FINAL
