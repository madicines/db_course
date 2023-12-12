## <font color="#E0DE71">ЗАДАНИЕ 1</font>

***
### № 4

**Задание:**

>_Выведите одним запросом: код заказчика, код заказа, дату заказа, объем заказа, объем заказа за предыдущую дату по данному заказчику, разницу между текущим и предыдущим объемом. Null значения должны быть заменены 0. Воспользуйтесь представлением Sales.OrderValues_

**Ответ:**

 ```mysql
WITH SalesOrderValues AS (  
SELECT O.custid, O.orderid, O.orderdate,  
COALESCE(SUM(OD.unitprice::NUMERIC * OD.qty), 0) AS val  
FROM "Sales"."Orders" O  
LEFT JOIN "Sales"."OrderDetails" OD ON O.orderid = OD.orderid  
GROUP BY O.custid, O.orderid, O.orderdate)  
SELECT SOV.custid, SOV.orderid, SOV.orderdate, SOV.val AS current_val,  
COALESCE(PREVIOUS.val, 0) AS previous_val,  
SOV.val - COALESCE(PREVIOUS.val, 0) AS val_difference  
FROM SalesOrderValues SOV  
LEFT JOIN ( SELECT custid, orderdate, val  
FROM SalesOrderValues) PREVIOUS ON SOV.custid = PREVIOUS.custid AND SOV.orderdate = PREVIOUS.orderdate+INTERVAL '1 day'  
ORDER BY SOV.custid, SOV.orderdate;
```


***
### № 5

**Задание:**

>_Выведите список заказчиков, пронумеровав их в пределах каждой страны отдельно в порядке убывания общей стоимости сделанных заказчиком заказов._

**Ответ:** 

 ```mysql
WITH CustomerOrderTotals AS (  
select C.country, O.custid,  
SUM(OD.unitprice * OD.qty) AS total_order_amount  
from "Sales"."Customers" C JOIN  
"Sales"."Orders" O ON C.custid = O.custid  
join "Sales"."OrderDetails" OD ON O.orderid = OD.orderid  
GROUP by C.country, O.custid)  
select country, custid, total_order_amount,  
ROW_NUMBER() OVER (PARTITION BY country ORDER BY total_order_amount DESC) AS customer_rank  
from CustomerOrderTotals  
ORDER by country, customer_rank;
```

