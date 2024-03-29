```SQL
-- chiffre d'affaire des commandes des deux derniers mois par pays
SELECT SUM(orderdetails.quantityOrdered * orderdetails.priceEach) as CA, 
       customers.country,
       MONTH(orders.orderDate) as mois
FROM orderdetails
JOIN orders ON orderdetails.orderNumber=orders.orderNumber
JOIN customers ON customers.customerNumber=orders.customerNumber
WHERE orders.orderDate >= DATE_SUB(CURRENT_DATE, INTERVAL 2 MONTH)
GROUP BY customers.country, mois;
```



-- chiffre d'affaire des commandes des deux derniers mois par catégorie de produits

SELECT SUM(orderdetails.quantityOrdered * orderdetails.priceEach) as CA, productlines.productLine, MONTH(orders.orderDate) as mois
FROM orderdetails
JOIN products ON products.productCode=orderdetails.productCode
JOIN orders ON orderdetails.orderNumber=orders.orderNumber
JOIN productlines ON productlines.productLine = products.productLine
WHERE orders.orderDate >= DATE_SUB(CURRENT_DATE, INTERVAL 2 MONTH)
GROUP BY productlines.productLine,mois;

-- chiffre d'affaire par pays et par magazin
SELECT SUM(orderdetails.quantityOrdered * orderdetails.priceEach) as CA, customers.country, offices.city, offices.officeCode
FROM orderdetails
JOIN orders ON orderdetails.orderNumber=orders.orderNumber
JOIN customers ON customers.customerNumber=orders.customerNumber
JOIN offices ON offices.country=customers.country
GROUP BY offices.officeCode;

-- Taux de marge ( bénéfice - cout) *100 des TOP 5 produits les plus vendus
-- je calcule d'abord le cout ( quantity Ordered * buy Price)

SELECT cout, CA, (CA-cout) as marge
FROM (
   SELECT sum(orderdetails.quantityOrdered*products.buyPrice) as cout,
   sum(orderdetails.quantityOrdered*orderdetails.priceEach) as CA
   FROM orderdetails
   JOIN products ON products.productCode=orderdetails.productCode
   GROUP BY products.productCode
   ORDER BY CA desc 
   LIMIT 5
) AS subquery;
