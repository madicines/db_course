## <font color="#E0DE71">ЗАДАНИЕ 1</font>

***
### № 1

**Задание:**

>_Выведите таблицу из трех столбцов: максимальная, минимальная и средняя стоимость продуктов._  

**Ответ:**

 ```mysql
SELECT  
MAX(unitprice) AS max_price,  
MIN(unitprice)AS min_price,  
AVG(unitprice::numeric) AS avg_price  
FROM "Sales"."OrderDetails"
```

Понял, что приведение к numeric нужно только для функции AVG()

![](https://github.com/madicines/db_spbtsu/blob/0dd1c2083e8b0a7d36cd90afcceb677032322ae2/Pasted%20image%2020231212110137.png)

***
### № 4

**Задание:**

>_Выведите таблицу из трех столбцов: Код заказчика, максимальная и минимальная стоимость продуктов в заказах, сделанных этим заказчиком._

**Ответ:** 

 ```mysql
SELECT o.CUSTID , MAX(od.UNITPRICE) AS max_cost, MIN(od.UNITPRICE) AS min_cost
FROM "Sales"."Orders" o join "Sales"."OrderDetails" od using (ORDERID)
GROUP BY o.CUSTID;
```

Использовал только "Sales"."OrderDetails"

![](https://github.com/madicines/db_spbtsu/blob/c823d78bb02cc4d9a22136b25264cc4a06a56a68/Pasted%20image%2020231212111836.png)

***
### № 5

**Задание:**

>_Выведите 5 самых выгодных заказчиков (с точки зрения суммарной стоимости их заказов)._

**Ответ:**

 ```mysql
SELECT o.CUSTID, p.UNITPRICE * od.QTY as "SUM"  
FROM "Sales"."Orders" o join "Sales"."OrderDetails" od using (ORDERID) JOIN "Production"."Products" p  
USING (PRODUCTID)  
GROUP BY p.UNITPRICE, o.CUSTID, od.QTY  
order by "SUM" desc  
limit 5;
```

***
### № 7

**Задание:**

>_Выведите список только тех заказов, общая стоимость которых превышает 1000._

**Ответ:** 

 ```mysql
SELECT o.CUSTID, p.UNITPRICE * od.QTY as top_sum  
FROM "Sales"."Orders" o join "Sales"."OrderDetails" od using (ORDERID) JOIN "Production"."Products" p USING (PRODUCTID)  
WHERE p.UNITPRICE::NUMERIC * od.QTY > 1000  
GROUP BY p.UNITPRICE, o.CUSTID, od.QTY  
order by top_sum desc;
```

***
## <font color="#E0DE71">ЗАДАНИЕ 2</font>

***
### № 1

**Задание:**

>_Выведите список заказов клиента, который был зарегистрирован в БД последним._

**Ответ:** 

 ```mysql
SELECT custid as "Last registered", orderid  
FROM "Sales"."Orders"  
WHERE custid in (SELECT max(custid)  
FROM "Sales"."Customers");
```

***
### № 2

**Задание:**

>_Выведите полные данные по клиентам, которые сделали заказ в самую последнюю дату._ 

**Ответ: 

 ```mysql
SELECT c.*  
FROM "Sales"."Orders" left join "Sales"."Customers" c using (custid)  
WHERE orderdate in (SELECT orderdate  
FROM "Sales"."Orders" order by orderdate DESC NULLS last limit 1);
```

IN использую, чтобы найти последнюю дату из числа всех заказов. Подзапрос вернет одно значение:
![[Pasted image 20231212114401.png]]

***
### № 3

**Задание:**

>_Выведите список клиентов, которые не делали заказов._

**Ответ:**

 ```mysql
SELECT custid as "ID Клиента без заказов"  
FROM "Sales"."Customers"  
WHERE custid not in (SELECT custid  
FROM "Sales"."Orders");
```
