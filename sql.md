# Data Retrieval
## Lession 1
- SELECT 
    - The result of a query is often called a result set.
    - Additional clauses of the SELECT statement:
        - WHERE : filter rows in the result set.
        - ORDER BY: sort rows in the result set by one or more columns.
        - GROUP BY: group rows into groups.
        - HAVING: filter groups.
    - ```sql
        SELECT
            select_list
        FROM
            schema_name.table_name;
        ```
    - ```SQL
        use AdventureWorks2025

        select * from HumanResources.Employee
        ```
    - ```SQL
        use AdventureWorks2025

        select JobTitle, BirthDate, Gender from HumanResources.Employee
        ```
    - DISTINCT
        - ```sql
            SELECT 
            DISTINCT column_name 
            FROM 
            table_name;
            ```
        - The query will return unique values from the column column_name. In other words, it removes duplicate values from the column_name in the result set.
        - ```sql
            use AdventureWorks2025

            select distinct Color from Production.Product
            ```
        - ```sql
            use SampleDatabase

            SELECT 
            DISTINCT city, state 
            FROM 
            sales.customers
            ```
    - TOP
        - The SELECT TOP clause allows you to limit the rows or percentage of rows returned by a query. It is useful when you want to retrieve a specific number of rows from a large table.
        - ```sql
            SELECT TOP (expression) [PERCENT]
                [WITH TIES]
            FROM 
                table_name
            ORDER BY 
                column_name;
             ```
        - The PERCENT keyword indicates that the query returns the first N percentage of rows, where N is the result of the expression.
        - The WITH TIES allows you to return additional rows with values that match those of the last row in the limited result set. Note that WITH TIES may result in more rows being returned than specified in the expression.
        - ```sql
            use SampleDatabase

            SELECT TOP 3 WITH TIES
                product_name, 
                list_price
            FROM
                production.products
            ORDER BY 
                list_price DESC;
             ```
- WHERE
    - To retrieve rows that satisfy one or more conditions, you use the WHERE clause in the SELECT statement.
    - ```sql
        SELECT
            select_list
        FROM
            table_name
        WHERE
            search_condition;
        ```
    - ```sql
        use AdventureWorks2025

        select * from Production.Product
        where Color = 'Silver' and ListPrice > 200
        ```
    - ![alt text](media/image-6.png)
    - (IS (NOT)) NULL
        - The results of the following comparisons are UNKNOWN:
            - NULL = 0
            - NULL <> 0
            - NULL > 0
            - NULL = NULL
        - ```sql
            select distinct Color from Production.Product
            where Color is not null
            ```
    - LIKE
        - %: Any string of zero or more characters.
        - _: Any single character.
        - [character-character]: Any single character within the specified set.
        - ^: Any character that is not within a list or a range (komplementer).
            - ```sql
                select top 5 * from HumanResources.Employee
                where JobTitle like '[CE]%'
                order by JobTitle
                ```
            - ```sql
                select * from HumanResources.Employee
                where JobTitle like '[C-E]%'
                order by JobTitle
                ```
            - ESCAPE
                - ```sql
                    SELECT 
                        feedback_id, 
                        comment
                    FROM 
                        sales.feedbacks
                    WHERE 
                        comment LIKE '%30!%%' ESCAPE '!';
                    ```
                - In this query, the  ESCAPE clause specified that the character ! is the escape character.
                - It instructs the LIKE operator to treat the % character as a literal string instead of a wildcard. Note that without the ESCAPE clause, the query would return an empty result set.
        - BETWEEN
            - The BETWEEN operator is a logical operator that allows you to specify a range to test.
            - ```sql
                column | expression (NOT) BETWEEN start_expression AND end_expression
                ```
            - ```sql
                use AdventureWorks2025

                select ProductNumber, Name, Color, ListPrice as Ar
                from Production.Product
                where ListPrice between 50 and 80
                ```
        - IN
            - ```sql
                use SampleDatabase

                SELECT
                    product_id,
                    product_name,
                    category_id,
                    model_year,
                    list_price
                FROM
                    production.products
                WHERE
                    list_price IN (299.99, 369.99, 489.99)
                ORDER BY
                    list_price DESC;
                ```
    - Boolean műveletek
        - A következő sorrendben hajtódnak végre: NOT, AND, OR
            - AND

                | AND | TRUE  | FALSE | UNKNOWN |
                |---------|-------|-------|---------|
                | TRUE    | TRUE  | FALSE | UNKNOWN |
                | FALSE   |  | FALSE | FALSE   |
                | UNKNOWN |  |  | UNKNOWN |
            - OR

                | OR | TRUE | FALSE | UNKNOWN |
                |---------|------|-------|---------|
                | TRUE    | TRUE | TRUE  | TRUE    |
                | FALSE   |  | FALSE | UNKNOWN |
                | UNKNOWN |  |  | UNKNOWN |


- ORDER BY
    - To sort rows in a result set based, you use the ORDER BY clause.
    - The columns in the ORDER BY clause must match either column in the select list or columns defined in the table specified in the FROM clause.
    - If you use multiple columns, the ORDER BY clause will sort rows by the first column first, then sort the sorted rows by the second column, and so on. 
    - ```sql
        SELECT
            select_list
        FROM
            table_name
        ORDER BY 
            column_name | expression [ASC | DESC ];
        ```
    - ```sql
        use AdventureWorks2025

        SELECT
            *
        FROM
            Sales.SalesPerson
        WHERE
            TerritoryID is not null
        ORDER BY
            Bonus desc;
        ```
    - ![alt text](media/image-7.png)
    - ```sql
        use SampleDatabase

        SELECT
            city,
            first_name,
            last_name
        FROM
            sales.customers
        ORDER BY
            city,
            first_name;
        ```
    - ```sql
        use SampleDatabase

        SELECT
            first_name,
            last_name
        FROM
            sales.customers
        ORDER BY
            LEN(first_name) DESC;
        ```
    - In this example, the numbers 1 and 2, which appear after the ORDER BY clause, denote the first_name and last_name columns respectively.
    - ```sql
        SELECT
            first_name,
            last_name
        FROM
            sales.customers
        ORDER BY
            1,
            2;
        ```
        - OFFSET FETCH
            - The OFFSET and FETCH clauses are options of the ORDER BY clause. They allow you to limit the number of rows returned by a query.
            - ```sql
                ORDER BY column_list [ASC |DESC]
                OFFSET offset_row_count {ROW | ROWS}
                FETCH {FIRST | NEXT} fetch_row_count {ROW | ROWS} ONLY
            - The OFFSET clause specifies the number of rows to skip before starting to return rows from the query. Greater or equal to zero.
            The FETCH clause specifies the number of rows to return after the OFFSET clause has been processed. Greater or equal to one.
            - The OFFSET clause is mandatory, while the FETCH clause is optional.
            - ![alt text](media/image-9.png)
            - ```sql
                use SampleDatabase

                SELECT
                    product_name,
                    list_price
                FROM
                    production.products
                ORDER BY
                    list_price,
                    product_name 
                OFFSET 10 ROWS 
                FETCH NEXT 10 ROWS ONLY;
                ```
- GROUP BY
    - The GROUP BY clause allows you to arrange the rows of a query in groups. The groups are determined by the columns that you specify in the GROUP BY clause.
    - ```sql
        SELECT
            select_list
        FROM
            table_name
        GROUP BY
            column_name1,
            column_name2 ,...;
        ```
    - ```sql
        use AdventureWorks2025

        SELECT
            Color,
            Count(*)
        FROM
            Production.Product
        WHERE
            SafetyStockLevel > 800 and 
            color is not null
        GROUP BY
            Color
        ORDER BY
            Color;
        ```
    - ![alt text](media/image-8.png)
    - ```sql
        SELECT
            customer_id,
            YEAR (order_date) order_year
        FROM
            sales.orders
        WHERE
            customer_id IN (1, 2)
        GROUP BY
            customer_id,
            YEAR (order_date)
        ORDER BY
            customer_id;
        ```
- HAVING
    - The HAVING clause is often used with the GROUP BY clause to filter groups based on a specified list of conditions. The following illustrates the HAVING clause syntax:
    - ```sql
        SELECT
            select_list
        FROM
            table_name
        GROUP BY
            group_list
        HAVING
            conditions;
        ```
    - ```sql
        use AdventureWorks2025

        SELECT
            Color,
            Count(*)
        FROM
            Production.Product
        WHERE
            SafetyStockLevel > 800 and 
            color is not null
        GROUP BY
            Color
        HAVING
            Count(*) > 1
        ORDER BY
            Color;
        ```
    - ```sql
        SELECT
            customer_id,
            YEAR (order_date),
            COUNT (order_id) order_count
        FROM
            sales.orders
        GROUP BY
            customer_id,
            YEAR (order_date)
        HAVING
            COUNT (order_id) >= 2
        ORDER BY
            customer_id;
        ```
- Alias: AS
    - ```sql
        column_name | expression  AS column_alias
        ```
    - ```sql
        select JobTitle as Munka from HumanResources.Employee
        where JobTitle like '%[^0-3]'
        ```
    - ```sql
        select Title + ' ' + FirstName + ' ' + LastName as Nev 
        from Person.Person
        where Title is not null
        ```

## Lession 2
- (inner) join = metszet
    - Inner join produces a data set that includes rows from the left table, and matching rows from the right table.
    - ![alt text](media/image-17.png)
    - ```sql
      select FirstName + ' ' + LastName as Name, JobTitle, VacationHours, SickLeaveHours 
      from HumanResources.Employee as e inner join Person.Person p
      on e.BusinessEntityID = p.BusinessEntityID
      ```
    - ```sql
        SELECT  
            c.id candidate_id,
            c.fullname candidate_name,
            e.id employee_id,
            e.fullname employee_name
        FROM 
            hr.candidates c
            INNER JOIN hr.employees e 
                ON e.fullname = c.fullname;
        ```
    - ![alt text](media/image-10.png)

- right (outer) join
    - The RIGHT JOIN clause starts selecting data from the right table and matching it with the rows from the left table. The RIGHT JOIN returns a result set that includes all rows in the right table, whether or not they have matching rows from the left table.
    - jobb + metszet:
        - ```sql
            select FirstName + ' ' + LastName as Name, JobTitle, VacationHours, SickLeaveHours 
            from HumanResources.Employee as e right outer join Person.Person p
            on e.BusinessEntityID = p.BusinessEntityID
            ```
        - ```sql
            SELECT  
                c.id candidate_id,
                c.fullname candidate_name,
                e.id employee_id,
                e.fullname employee_name
            FROM 
                hr.candidates c
                RIGHT JOIN hr.employees e 
                    ON e.fullname = c.fullname;
            ```
        - ![alt text](media/image-13.png)
    - csak a jobb:
        - ```sql
            SELECT  
                c.id candidate_id,
                c.fullname candidate_name,
                e.id employee_id,
                e.fullname employee_name
            FROM 
                hr.candidates c
                RIGHT JOIN hr.employees e 
                    ON e.fullname = c.fullname
            WHERE
                c.id IS NULL;
            ```
        - ![alt text](media/image-14.png)

- left (outer) join
    - The LEFT JOIN returns all rows from the left table and the matching rows from the right table. If no matching rows are found in the right table, NULL are used.
    - bal + metszet:
        - ![alt text](media/image-18.png)
        - ```sql
            select FirstName + ' ' + LastName as Name, JobTitle, VacationHours, SickLeaveHours 
            from HumanResources.Employee as e left outer join Person.Person p
            on e.BusinessEntityID = p.BusinessEntityID
            ```
        - ```sql
            SELECT  
                c.id candidate_id,
                c.fullname candidate_name,
                e.id employee_id,
                e.fullname employee_name
            FROM 
                hr.candidates c
                LEFT JOIN hr.employees e 
                    ON e.fullname = c.fullname;
            ```
        - ![alt text](media/image-11.png)
    - csak a bal:
        - ```sql
            SELECT  
                c.id candidate_id,
                c.fullname candidate_name,
                e.id employee_id,
                e.fullname employee_name
            FROM 
                hr.candidates c
                LEFT JOIN hr.employees e 
                    ON e.fullname = c.fullname
            WHERE 
                e.id IS NULL;
            ```
        - ![alt text](media/image-12.png)

- full (outer) join
    - uió
        - ```sql
            select FirstName + ' ' + LastName as Name, JobTitle, VacationHours, SickLeaveHours 
            from HumanResources.Employee as e full outer join Person.Person p
            on e.BusinessEntityID = p.BusinessEntityID
            ```  
        - ```sql
            SELECT  
                c.id candidate_id,
                c.fullname candidate_name,
                e.id employee_id,
                e.fullname employee_name
            FROM 
                hr.candidates c
                FULL JOIN hr.employees e 
                    ON e.fullname = c.fullname;
            ``` 
        - ![alt text](media/image-15.png)
    - unió kivéve a metszet
        - ```sql
            SELECT  
                c.id candidate_id,
                c.fullname candidate_name,
                e.id employee_id,
                e.fullname employee_name
            FROM 
                hr.candidates c
                FULL JOIN hr.employees e 
                    ON e.fullname = c.fullname
            WHERE
                c.id IS NULL OR
                e.id IS NULL;
            ```
        -![alt text](media/image-16.png)

- cross join
    - A cross join allows you to combine rows from the first table with every row of the second table. In other words, it returns the Cartesian product of two tables.
    - ![alt text](media/image-19.png)
    - ```sql
        SELECT
            select_list
        FROM
            T1
        CROSS JOIN T2;
        ``` 
    - ```sql
        SELECT
            product_id,
            product_name,
            store_id,
            0 AS quantity
        FROM
            production.products
        CROSS JOIN sales.stores
        ORDER BY
            product_name,
            store_id;
        ``` 

- self-join
    - A self join allows you to join a table to itself. It helps query hierarchical data or compare rows within the same table.
    - A self join uses the inner join or left join clause. Because the query that uses the self join references the same table, the table alias is used to assign different names to the same table within the query.
    - ```sql
        SELECT
            select_list
        FROM
            T t1
        [INNER | LEFT]  JOIN T t2 ON
            join_predicate; 
        ```
    - ```sql
        use AdventureWorks2025

        select a.PurchaseOrderID, b.PurchaseOrderID
        from Purchasing.PurchaseOrderHeader as a
        join Purchasing.PurchaseOrderHeader as b
        on a.VendorID = b.VendorID and a.ShipMethodID = b.ShipMethodID
        where a.PurchaseOrderID < b.PurchaseOrderID;
        ```
    - összegyűjti azokat a rendelés id-kat, ahol azonos volt a szállítás és az eladó
    - ```sql
        SELECT
            e.first_name + ' ' + e.last_name employee,
            m.first_name + ' ' + m.last_name manager
        FROM
            sales.staffs e
        LEFT JOIN sales.staffs m ON m.staff_id = e.manager_id
        ORDER BY
            manager;
        ```

## Lesson 3
- Aggregate Functions
    - https://learn.microsoft.com/en-us/sql/t-sql/functions/checksum-agg-transact-sql?view=sql-server-ver17
    - AVG
        - ```sql
            select AVG(ListPrice) from Production.Product
            ```
    - CHECKSUM_AGG: (valamileyen hash) This function returns the checksum of the values in a group. CHECKSUM_AGG ignores null values. The result does not depend on the order of the rows in the table. Also, CHECKSUM_AGG functions allow the use of the DISTINCT keyword and the GROUP BY clause.
        - ```sql
            SELECT CHECKSUM_AGG(CAST(ListPrice as int))  
            FROM Production.Product 
            ```
    - CHECKSUM: computes a hash value, called the checksum, over its argument list. Use this hash value to build hash indexes.
        - ```sql
            SELECT CHECKSUM(ListPrice)
            FROM Production.Product
            ```
    - COUNT
        - ```sql
            SELECT COUNT(*)
            FROM Production.Product 
            ```
        - ```sql
            SELECT COUNT(distinct SafetyStockLevel)
            FROM Production.Product 
            where MakeFlag = 1
            ```
    - COUNT_BIG: csak a bigint típusokat számolja, de egyébként egy sima count
        - This function returns the number of items found in a group. COUNT_BIG operates like the COUNT function. These functions differ only in the data types of their return values. COUNT_BIG always returns a bigint data type value. COUNT always returns an int data type value.
    - GROUPING
        - Az MS SQL Serverben a GROUPING függvényt a GROUP BY záradékban használt ROLLUP, CUBE vagy GROUPING SETS összesítésekkel együtt használják, hogy megkülönböztessék a tényleges adatsorokat az összesítő (részösszeg/főösszeg) soroktól. A függvény 1-et ad vissza, ha a sor összesítés, és 0-t, ha tényleges adat. 
        - ```sql
            SELECT SalesQuota, SUM(SalesYTD) 'TotalSalesYTD', GROUPING(SalesQuota) AS 'Grouping'  
            FROM Sales.SalesPerson  
            GROUP BY SalesQuota WITH ROLLUP; 
            ```
    - MAX
        - ```sql
            SELECT MAX(ListPrice)
            FROM Production.Product 
            ```
    - MIN
        - ```sql
            SELECT MIN(ListPrice)
            FROM Production.Product 
            WHERE ListPrice > 0
            ```
    - SUM
        - ```sql
            SELECT SUM(ListPrice)
            FROM Production.Product 
            ```
    - STDEV: Returns the statistical standard deviation of all values in the specified expression. SZórás.
        - ```sql
            SELECT STDEV(Bonus)
            FROM Sales.SalesPerson
            ```
    - STDEVP: Returns the statistical standard deviation for the population for all values in the specified expression. Teljes sokaságra a szórás.
        - ```sql
            SELECT STDEVP(Bonus)
            FROM Sales.SalesPerson
            ```
    - VAR: Returns the statistical variance of all values in the specified expression. Mintavételi variancia.
        - ```sql
            SELECT VAR(Bonus)
            FROM Sales.SalesPerson
            ```
    - VARP: Returns the statistical variance for the population for all values in the specified expression. (Kiszámítja a teljes sokaság (populáció) statisztikai szórásnégyzetét a megadott kifejezés összes értékére.)
        - ```sql
            SELECT VARP(Bonus)
            FROM Sales.SalesPerson
            ```
- GROUP BY
    - Creates a group for each combination of column expressions. In addition, it "rolls up" the results into subtotals and grand totals. To do this, it moves from right to left, decreasing the number of column expressions over which it creates groups and the aggregations.
    - ```sql
        use AdventureWorks2025

        select Production.Product.ProductSubcategoryID,
            AVG(ListPrice) as 'Average',
            MIN(ListPrice) as 'Minimum',
            MAX(ListPrice) as 'Maximum'
        from Production.Product
        where ListPrice <> 0
        group by Product.ProductSubcategoryID
        ```
    - WITH ROLLUP
        - The SQL Server ROLLUP is a subclause of the GROUP BY clause which provides a shorthand for defining multiple grouping sets.
        - Unlike the CUBE subclause, ROLLUP does not create all possible grouping sets based on the dimension columns.
        - When generating the grouping sets, ROLLUP assumes a hierarchy among the dimension columns and only generates grouping sets based on this hierarchy.
        - ROLLUP(d1,d2,d3) => d1 > d2 > d3:
        - ```
            (d1, d2, d3)
            (d1, d2)
            (d1)
            ()
            ```
        - ```sql
            SELECT
                brand,
                category,
                SUM (sales) sales
            FROM
                sales.sales_summary
            GROUP BY
                ROLLUP(brand, category);
            ```
        - ```sql
            use AdventureWorks2025

            select Production.ProductCategory.ProductCategoryID,
                Production.Product.ProductSubcategoryID,
                AVG(ListPrice) as 'Average',
                MIN(ListPrice) as 'Minimum',
                MAX(ListPrice) as 'Maximum'
            from Production.Product
            join Production.ProductSubcategory
            on Production.ProductSubcategory.ProductSubcategoryID = 
                Production.Product.ProductSubcategoryID
            join Production.ProductCategory
            on Production.ProductSubcategory.ProductCategoryID = 
                Production.ProductCategory.ProductCategoryID
            where ListPrice <> 0
            group by Production.ProductCategory.ProductCategoryID, Product.ProductSubcategoryID
            with rollup
            ```
        - A kategóriákon  belül az alkategóriákat is összeszámolja és a végén va egy öszzesítő sor is.
    - WITH CUBE
        - Kombinatórikus: Az oszlopok összes lehetséges kombinációját kiszámítja
        - The CUBE is a subclause of the GROUP BY clause that allows you to generate multiple grouping sets. The following illustrates the general syntax of the CUBE:
        - ```sql
            SELECT
                d1,
                d2,
                d3,
                aggregate_function (c4)
            FROM
                table_name
            GROUP BY
                CUBE (d1, d2, d3);     
            ```
        - The above query returns the same result set as the following query, which uses the  GROUPING SETS:
        - ```sql
            SELECT
                d1,
                d2,
                d3,
                aggregate_function (c4)
            FROM
                table_name
            GROUP BY
                GROUPING SETS (
                    (d1,d2,d3), 
                    (d1,d2),
                    (d1,d3),
                    (d2,d3),
                    (d1),
                    (d2),
                    (d3), 
                    ()
                );
            ```
        - If you have N dimension columns specified in the CUBE, you will have 2^N grouping sets.
        - ```sql
            use AdventureWorks2025

            select Production.ProductCategory.ProductCategoryID,
                Production.Product.ProductSubcategoryID,
                AVG(ListPrice) as 'Average',
                MIN(ListPrice) as 'Minimum',
                MAX(ListPrice) as 'Maximum'
            from Production.Product
            join Production.ProductSubcategory
            on Production.ProductSubcategory.ProductSubcategoryID = 
                Production.Product.ProductSubcategoryID
            join Production.ProductCategory
            on Production.ProductSubcategory.ProductCategoryID = 
                Production.ProductCategory.ProductCategoryID
            where ListPrice <> 0
            group by Production.ProductCategory.ProductCategoryID, Product.ProductSubcategoryID
            with cube
            ```
        - (ennek így sok értelme nincs)
    - GROUPING 
        - Az MS SQL Serverben a GROUPING függvényt a GROUP BY záradékban használt ROLLUP, CUBE vagy GROUPING SETS összesítésekkel együtt használják, hogy megkülönböztessék a tényleges adatsorokat az összesítő (részösszeg/főösszeg) soroktól. A függvény 1-et ad vissza, ha a sor összesítés, és 0-t, ha tényleges adat. 
        - ```sql
            use AdventureWorks2025

            select Production.ProductCategory.ProductCategoryID,
                grouping(Production.ProductCategory.ProductCategoryID),
                Production.Product.ProductSubcategoryID,
                grouping(Production.Product.ProductSubcategoryID),
                AVG(ListPrice) as 'Average',
                MIN(ListPrice) as 'Minimum',
                MAX(ListPrice) as 'Maximum'
            from Production.Product
            join Production.ProductSubcategory
            on Production.ProductSubcategory.ProductSubcategoryID = 
                Production.Product.ProductSubcategoryID
            join Production.ProductCategory
            on Production.ProductSubcategory.ProductCategoryID = 
                Production.ProductCategory.ProductCategoryID
            where ListPrice <> 0
            group by Production.ProductCategory.ProductCategoryID, Product.ProductSubcategoryID
            with rollup
            ```
        - feldolgozásnál bitos hasznos, de a NULL-ok szerintem jobban látszódnak
    - GROUPING SETS
        - Lehetővé teszi több különböző csoportosítási szint (halmaz) eredményének egyetlen lekérdezésben történő kiszámítását és egyesítését.
        - By definition, a grouping set is a group of columns by which you group. Typically, a single query with an aggregate defines a single grouping set.
        - ```sql
            use AdventureWorks2025

            select Production.ProductCategory.ProductCategoryID,
                Production.Product.ProductSubcategoryID,
                AVG(ListPrice) as 'Average'
            from Production.Product
            join Production.ProductSubcategory
            on Production.ProductSubcategory.ProductSubcategoryID = 
                Production.Product.ProductSubcategoryID
            join Production.ProductCategory
            on Production.ProductSubcategory.ProductCategoryID = 
                Production.ProductCategory.ProductCategoryID
            where ListPrice <> 0
            group by grouping sets (Production.ProductCategory.ProductCategoryID , Product.ProductSubcategoryID), (Product.ProductSubcategoryID)
            ```
        - ``` sql
            SELECT
                brand,
                category,
                SUM (sales) sales
            FROM
                sales.sales_summary
            GROUP BY
                GROUPING SETS (
                    (brand, category),
                    (brand),
                    (category),
                    ()
                )
            ORDER BY
                brand,
                category;
            ```
    - HAVING
        - Specifies a search condition for a group or an aggregate. You can use HAVING only with the SELECT statement. 
        - ```sql
            use AdventureWorks2025

            select Production.ProductCategory.ProductCategoryID,
                Production.Product.ProductSubcategoryID,
                AVG(ListPrice) as 'Average',
                MIN(ListPrice) as 'Minimum',
                MAX(ListPrice) as 'Maximum'
            from Production.Product
            join Production.ProductSubcategory
            on Production.ProductSubcategory.ProductSubcategoryID = 
                Production.Product.ProductSubcategoryID
            join Production.ProductCategory
            on Production.ProductSubcategory.ProductCategoryID = 
                Production.ProductCategory.ProductCategoryID
            where ListPrice <> 0
            group by Production.ProductCategory.ProductCategoryID, Product.ProductSubcategoryID
            with rollup
            having MIN(ListPrice) > 200
            ```

## Lesson 4
- UNION
    - Két vagy több SELECT utasítás eredményhalmazát fűzi össze egyetlen, kombinált eredménylistává.
    - The following are requirements for the queries in the syntax above:
        - The number and the order of the columns must be the same in both queries.
        - The data types of the corresponding columns must be the same or compatible.
    - ```sql
        use AdventureWorks2025

        select  convert(Char(8), HumanResources.Employee.BusinessEntityID) from HumanResources.Employee
        union
        select Sales.Customer.CustomerID from Sales.Customer
        ```
    - ```sql
        use AdventureWorks2025

        select Name, ModifiedDate from Production.Product
        union
        select Name, ModifiedDate from Production.ProductModel
        ```
    - ALL - Includes duplicates.
    - ```sql
        use AdventureWorks2025

        select  convert(Char(8), HumanResources.Employee.BusinessEntityID) from HumanResources.Employee
        union all
        select Sales.Customer.CustomerID from Sales.Customer
        ```
    - ![alt text](media/image-20.png)
- EXCEPT
    - Returns distinct rows from the left input query that aren't output by the right input query.
    - ```sql 
        use AdventureWorks2025

        select Sales.Customer.CustomerID from Sales.Customer
        except
        select  convert(Char(8), HumanResources.Employee.BusinessEntityID) from HumanResources.Employee
        ```
- INTERSECT
    - Returns distinct rows that are output by both the left and right input queries operator.
    - ```sql 
        use AdventureWorks2025

        select Sales.Customer.CustomerID from Sales.Customer
        intersect
        select  convert(Char(8), HumanResources.Employee.BusinessEntityID) from HumanResources.Employee
        ```
- APPLY
    - Specifies that the right_table_source of the APPLY operator is evaluated against every row of the left_table_source.
    - ```sql
        select BusinessEntityID from Person.PersonPhone
        apply
        select BusinessEntityID from Person.Person
        ```
    - CROSS is specified, no rows are produced when the right_table_source is evaluated against a specified row of the left_table_source and returns an empty result set.
    - ```sql
        select gci.FirstName, pe.EmailAddress from Person.EmailAddress as pe
        cross apply
        dbo.ufnGetContactInformation(pe.BusinessEntityID) as gci
        ```
    - OUTER is specified, one row is produced for each row of the left_table_source even when the right_table_source evaluates against that row and returns an empty result set.
    - ```sql
        select gci.FirstName, pe.EmailAddress from Person.EmailAddress as pe
        outer apply
        dbo.ufnGetContactInformation(pe.BusinessEntityID) as gci
        ```
    - dbo.ufnGetContactInformation is a user-defined table-valued function in the AdventureWorks SQL Server database that retrieves contact details for a specific person.

## Lesson 5
- Date and Time Functions
    - SYSDATETIME: Returns a datetime2(7) value that contains the date and time of the computer on which the instance of SQL Server is running.
    - SYSDATETIMEOFFSET: Returns a datetimeoffset(7) value that contains the date and time of the computer on which the instance of SQL Server is running. The time zone offset is included.
    - SYSUTCDATETIME: Returns a datetime2 value that contains the date and time of the computer on which the instance of SQL Server is running. The date and time is returned as UTC time. 
        - ```sql
            use AdventureWorks2025

            select SYSDATETIME(), 'SYSDATETIME'
            union
            select SYSDATETIMEOFFSET(), 'SYSDATETIMEOFFSET'
            union
            select SYSUTCDATETIME(), 'SYSUTCDATETIME'
            ```
    - GETDATE: Returns the current database system timestamp as a datetime value without the database time zone offset. This value is derived from the operating system of the computer on which the instance of SQL Server is running.
    - GETUTCDATE: Returns the current database system timestamp as a datetime value. The database time zone offset is not included. This value represents the current UTC time.
    - CURRENT_TIMESTAMP: This function returns the current database system timestamp as a datetime value, without the database time zone offset. CURRENT_TIMESTAMP derives this value from the operating system of the computer on which the instance of SQL Server runs.
        - ```sql
            use AdventureWorks2025

            select GETDATE(), 'GETDATE'
            union
            select CURRENT_TIMESTAMP, 'CURRENT_TIMESTAMP'
            union
            select GETUTCDATE(), 'GETUTCDATE'
            ```
    - DATENAME: Returns a character string representing the specified datepart of the specified date.
    - DATEPART: Returns an integer representing the specified datepart of the specified date.
    - DATEADD: Adds a number (a signed bigint) to a datepart of an input date, and returns a modified date/time value. 
    - DATEDIFF: Returns the count (as a signed integer value) of the specified datepart boundaries crossed between the specified startdate and enddate.
        - ```sql
            use AdventureWorks2025

            select DATENAME(MONTH, GETDATE()), 'DATENAME'
            select DATEPART(MONTH, GETDATE()), 'DATEPART'
            select DATEADD(YY,2,GETDATE()), 'DATEADD'
            select DATEDIFF(DD,GETDATE(),'4/12/2026'), 'DATEDIFF'
            ```
- System Functions
    - CONVERT és CAST
    - ```sql
        select CONVERT(varchar(30),GETDATE()),'Alapertelmezett' as Silus
        union
        select  CONVERT(varchar(30),GETDATE(),111),'Normalis'
        union
        select  CONVERT(varchar(30),GETDATE(),23),'Normalis'
        union
        select CAST(GETDATE() AS varchar(30)), 'CAST-tal'
        ```
- String Functions
    - LEFT, RIGHT: Returns the left/right part of a character string with the specified number of characters.
        - ```sql
            SELECT RIGHT(FirstName, 5) AS 'First Name'  
            FROM Person.Person  
            WHERE BusinessEntityID < 5  
            ORDER BY FirstName;  
            ```
    - UPPER, LOWER: Returns a character expression with lowercase/uppercase character data converted to uppercase/lowercase.
        - ```sql
            SELECT UPPER(FirstName) AS 'First Name'  
            FROM Person.Person  
            WHERE BusinessEntityID < 5  
            ORDER BY FirstName;  
            ```
    - SUBSTRING: Returns part of a character, binary, text, or image expression in SQL Server.
        - ```sql
            SELECT FirstName,
                SUBSTRING(FirstName, 1, 3) AS 'First Name',
                SUBSTRING(FirstName, 2, 3) AS 'First Name2',
                SUBSTRING(FirstName, 2) AS 'First Name3'
            FROM Person.Person  
            WHERE BusinessEntityID < 5  
            ```
    - REPLACE: Replaces all occurrences of a specified string value with another string value.
        - ```sql
            SELECT REPLACE('abcdefghicde','cde','xxx')
            ```
    - LEN: Returns the number of characters of the specified string expression, excluding trailing spaces.
        - ```sql
            SELECT LEN(FirstName + ' ' + LastName) AS Length,
                FirstName,
                LastName
            FROM Sales.vIndividualCustomer
            WHERE CountryRegionName = 'Australia';
            ```
- Additional Functions
    - DATALENGTH: Returns the number of bytes used to represent any expression.
        - ```sql
            SELECT LDATALENGTHN(FirstName + ' ' + LastName) AS Length,
                FirstName,
                LastName
            FROM Sales.vIndividualCustomer
            WHERE CountryRegionName = 'Australia';
            ```
    - PATINDEX: Returns the starting position of the first occurrence of a pattern in a specified expression, or zero if the pattern isn't found, on all valid text and character data types.
        - ```sql
            SELECT PATINDEX('%ter%', 'interesting data') AS position
            union
            SELECT PATINDEX('%asdadasd%', 'interesting data') AS position
            union
            SELECT PATINDEX('%ensure%', DocumentSummary) AS position
            FROM Production.Document
            WHERE DocumentNode = 0x7B40;

            select DocumentSummary from Production.Document
            ```
    - CHARINDEX: Searches for one character expression inside a second character expression, returning the starting position of the first expression if found.
        - ```sql
            SELECT CHARINDEX('is', 'This is a string is', 4), 'jo' as Eredmeny
            union
            SELECT CHARINDEX('is', 'This is a string is', 7), 'messze'
            union
            SELECT CHARINDEX('at', 'This is a string'), 'rossz'
            union
            SELECT CHARINDEX('ensure', DocumentSummary, 10), 'ez is jo'
            FROM Production.Document
            ```

# Modifying Data

## Lesson 1
- INSERT
    - To add one or more rows into a table, you use the INSERT statement.
    - ```sql
        INSERT INTO table_name (column_list)
        VALUES (value_list);
        ```
    - ```sql
        use AdventureWorks2025

        insert into Sales.SalesReason
        values('Item Closout', 'Other', getdate())
        ```
    - Több sor beszúrása
    - ```sql
        INSERT INTO sales.promotions (
            promotion_name,
            discount,
            start_date,
            expired_date
        )
        VALUES
            (
                '2019 Summer Promotion',
                0.15,
                '20190601',
                '20190901'
            ),
            (
                '2019 Fall Promotion',
                0.20,
                '20191001',
                '20191101'
            ),
            (
                '2019 Winter Promotion',
                0.25,
                '20191201',
                '20200101'
            );
        ```
    - A táblához van default, ezért azt ki is egészíti magától.
    - ```sql
        use AdventureWorks2025

        insert into Sales.SalesReason
        values('Item Closout', 'Other', DEFAULT)
        ```
    - Lehet specifikusan is beszúrni oszlopokba
    - ```sql
        use AdventureWorks2025

        insert into Sales.SalesReason
        (Name,ReasonType)
        values('Item Closout', 'Other')
        ```
    - Lehet beszúrni egyik táblába úgy, hogy egy másik táblából kérdezünk le hozzá. (ez mondjuk menő)
    - ```sql
        use Northwind

        insert into Customers
        select SUBSTRING(lastname,1,5), 'Northwind Traders', FirstName + ' ' + LastName,
            'Employee', Address, City, Region, PostalCode, Country, HomePhone, NULL
        from Employees
        ```
    - OUTPUT 
        - To capture the inserted values, you use the OUTPUT clause.
        - ```sql
            INSERT INTO sales.promotions (
                promotion_name,
                discount,
                start_date,
                expired_date
            ) OUTPUT inserted.promotion_id
            VALUES
                (
                    '2018 Fall Promotion',
                    0.15,
                    '20181001',
                    '20181101'
                );
            ```
    - SELECT INTO: Copies data from one table into a new table.
        - ```sql
            use AdventureWorks2025

            select IDENTITY (int, 1,1) AS EmpID, FirstName, LastName, JobTitle,
                Name as 'Department', GroupName as 'Division'
            into #EmployeeDepartment
            from HumanResources.Employee join Person.Person
            on HumanResources.Employee.BusinessEntityID = Person.Person.BusinessEntityID
            join HumanResources.EmployeeDepartmentHistory
            on HumanResources.Employee.BusinessEntityID = 
                HumanResources.EmployeeDepartmentHistory.BusinessEntityID
            join HumanResources.Department
            on HumanResources.Department.DepartmentId =
                HumanResources.EmployeeDepartmentHistory.DepartmentID
            where HumanResources.EmployeeDepartmentHistory.EndDate is null
            order by Department, Division
            ```
        - ```sql
            INSERT INTO sales.addresses (street, city, state, zip_code) 
            SELECT
                street,
                city,
                state,
                zip_code
            FROM
                sales.customers
            ORDER BY
                first_name,
                last_name; 
        ```

- UPDATE
    - To modify existing data in a table, you use the following UPDATE statement:
    - ```sql
        UPDATE 
            table_name
        SET 
            c1 = v1, 
            c2 = v2, 
            ...,
            cn = vn
        [WHERE condition]
        ```
    - ```sql
        use AdventureWorks2025

        update Sales.CurrencyRate
        set EndOfDayRate = EndOfDayRate + 0.05
        ```
    - Itt is lehet WHERE-t használni.
    - ```sql
        use AdventureWorks2025

        update Sales.SalesReason
        set Name = 'N/A'
        where SalesReasonID = 10
        ```
    - To query data from related tables, you often use the join clauses, either inner join or left join. In SQL Server, you can use these join clauses in the UPDATE statement to perform a cross-table update.
    - ```sql
        UPDATE 
            t1
        SET 
            t1.c1 = t2.c2,
            t1.c2 = expression,
            ...   
        FROM 
            t1
            [INNER | LEFT] JOIN t2 ON join_predicate
        WHERE 
            where_predicate;
        ```
    - ```sql
        use AdventureWorks2025

        update Sales.SalesOrderDetail
        set UnitPrice = UnitPrice * 1.05
        from Sales.SalesOrderDetail join Production.Product
            on Sales.SalesOrderDetail.ProductID = Production.Product.ProductID
        where Production.Product.Color = 'Red'
        ```
- DELETE
    - To remove one or more rows from a table completely, you use the DELETE statement. The following illustrates its syntax:
    - ```sql
        DELETE [ TOP ( expression ) [ PERCENT ] ]  
        FROM table_name
        [WHERE search_condition];
        ```
    - ```sql
        use Northwind

        delete from Employees
        where EmployeeID = 7
        ```    
    - Itt is lehet join alapján törölni
    - ```sql
        use Northwind

        delete from [Order Details]
        from Orders join [Order Details]
            on Orders.OrderID = [Order Details].OrderID
        where OrderDate < '07-10-1996' and ShippedDate is not null
        ```  
- TRUNCATE TABLE
    - Olyan mint a DELETE, csak gyorsabb, nem hagy annyi nyomot maga után.
    - ```sql
        TRUNCATE TABLE <tablaNeve>
        ```
    - Use less transaction log
    - Use fewer locks
    - Identity reset

## Lesson 2
- OUTPUT Clause: Returns information from, or expressions based on, each row affected by an INSERT, UPDATE, DELETE, or MERGE statement. These results can be returned to the processing application for use in such things as confirmation messages, archiving, and other such application requirements. The results can also be inserted into a table or table variable.
    - Pl. logolja, ha beszúrás történik:
    - ```sql
        use Northwind

        insert into Employees
        (LastName, FirstName, Title)
        output GETDATE(), inserted.EmployeeID into Audit
        values ('Ralls', 'Kim', 'Support Rep')
        ``` 
    - Pl. Átrakja az 1997. dec. 1. előtti rekordokat másik táblába:
    - ```sql
        use Northwind

        delete from [Order Details]
        output deleted.* into OrderDetailsArchive
        from Orders join [Order Details]
        on Orders.OrderID = [Order Details].OrderID
        where OrderDate < '12-01-1997'
        ```
- MERGE Statement: Performs insert, update, or delete operations on a target table based on the results of a join with a source table.
    - Syntax:
    - ```sql
        MERGE target_table USING source_table
        ON merge_condition
        WHEN MATCHED
            THEN update_statement
        WHEN NOT MATCHED
            THEN insert_statement
        WHEN NOT MATCHED BY SOURCE
            THEN DELETE;
        ```
- First, you specify the target table and the source table in the MERGE clause.
- Second, the merge_condition determines how the rows from the source table are matched to the rows from the target table. It is similar to the join condition in the join clause. Typically, you use the key columns either primary key or unique key for matching.
- Third, the merge_condition results in three states: MATCHED, NOT MATCHED, and NOT MATCHED BY SOURCE.
    - MATCHED: these are the rows that match the merge condition. In the diagram, they are shown as blue. For the matching rows, you need to update the rows columns in the target table with values from the source table.
    - NOT MATCHED: these are the rows from the source table that does not have any matching rows in the target table. In the diagram, they are shown as orange. In this case, you need to add the rows from the source table to the target table. Note that NOT MATCHED is also known as NOT MATCHED BY TARGET.
    - NOT MATCHED BY SOURCE: these are the rows in the target table that does not match any rows in the source table. They are shown as green in the diagram. If you want to synchronize the target table with the data from the source table, then you will need to use this match condition to delete rows from the target table.
- ![alt text](media/image-21.png)
    - ```sql
        use AdventureWorks2025

        merge into dbo.Sales.SalesOrderDetailHistory as SODH
            using Sales.SalesOrderDetail as SOD
            on SODH.SalesOrderID = SOD.SalesOrderID
            and SODH.SalesOrderDetailID = SOD.SalesOrderDetailID
        when not matched by target then
            insert (LineTotal, SalesOrderID, SalesOrderDetailID, CarrierTrackingNumber
            ,OrderQty, ProductID, SpecialOfferID,UnitPrice,UnitPriceDiscount,rowguid
            ,ModifiedDate, Cancelled)
        values (LineTotal, SalesOrderID, SalesOrderDetailID, CarrierTrackingNumber
            ,OrderQty, ProductID, SpecialOfferID,UnitPrice,UnitPriceDiscount,rowguid
            ,ModifiedDate, GETDATE())
        when not matched by source then
            update set SODH.Cancelled = 'True';
        ```
- PIVOT
    - SQL Server PIVOT operator rotates a table-valued expression. It turns the unique values in one column into multiple columns in the output and performs aggregations on any remaining column values.
    - First, select a base dataset for pivoting.
    - Second, create a temporary result by using a derived table or common table expression (CTE)
    - Third, apply the PIVOT operator.
    - ```SQL
        SELECT * FROM   
        (
            SELECT 
                category_name, 
                product_id
            FROM 
                production.products p
                INNER JOIN production.categories c 
                    ON c.category_id = p.category_id
        ) t 
        PIVOT(
            COUNT(product_id) 
            FOR category_name IN (
                [Children Bicycles], 
                [Comfort Bicycles], 
                [Cruisers Bicycles], 
                [Cyclocross Bicycles], 
                [Electric Bikes], 
                [Mountain Bikes], 
                [Road Bikes])
        ) AS pivot_table;
        ```

## Lesson 3
- https://learn.microsoft.com/en-us/sql/t-sql/language-elements/transactions-transact-sql?view=sql-server-ver17
- Transactions: A transaction is a single unit of work. If a transaction is successful, all of the data modifications made during the transaction are committed and become a permanent part of the database. If a transaction encounters errors and must be canceled or rolled back, then all of the data modifications are erased.
- BEGIN TRANSACTION: Marks the starting point of an explicit, local transaction. Explicit transactions start with the BEGIN TRANSACTION statement and end with the COMMIT or ROLLBACK statement.
- ROLLBACK TRANSACTION: This statement rolls back an explicit or implicit transaction to the beginning of the transaction, or to a savepoint inside the transaction. You use ROLLBACK TRANSACTION to erase all data modifications made from the start of the transaction or from a savepoint. ROLLBACK TRANSACTION also frees the resources held by the transaction.
    - ```sql
        begin transaction
        insert into TestTable
        values (1, 'a', 'b');
            begin transaction
            update TestTable
            set Col2 = 'c' where TedtIID = 1;
            commit transaction;
        rollback;
        ```
    - Itt a ROLLBACK viszza dobja egészen a transaction kezdetéig az első soron. A beszúrt sor nem fog létezni a táblázatban.
    - SAVE TRANSACTION [name]: El lehet menteni a transaction állaotát. A ROLLBACK ide fog visszadobni, ha több is van azonos néven, akkor a legutolsóhoz.
    - Ha több infó kell a transactionokról, akkor meg lehet nézni őket:
    - ```sql
        select * from sys.dm_tran_active_transactions
        order by transaction_begin_time
        ```
- Locking: Each transaction requests locks of different types on the resources, such as rows, pages, or tables, on which the transaction is dependent. The locks block other transactions from modifying the resources in a way that would cause problems for the transaction requesting the lock. Each transaction frees its locks when it no longer has a dependency on the locked resources.

# Tables, Data Types, and Declarative Data Integrity
## Lesson 1
- https://learn.microsoft.com/en-us/sql/t-sql/data-types/data-types-transact-sql?view=sql-server-ver17
- Exact numeric types

    |Data type|	Range|	Storage|
    |-|-|-|
    |bigint|	-9,223,372,036,854,775,808 to 9,223,372,036,854,775,807|    8 bytes
    |int|	-2,147,483,648 to 2,147,483,647|	4 bytes
    |smallint|	-32,768 to 32,767|	2 bytes
    |tinyint|	0 to 255|	1 byte
    |decimal <br>(precision, scale)| -10E38+1 to 10E38-1| 5 to 17 bytes
    |numeric <br>(precision, scale)| Functionally equivalent to the decimal data type.
    - A decimal és a numeric minél pontosabb, annál több helyet foglal.

    |Precision|	Storage bytes|
    |-|-|
    |1 - 9|	5
    10-19|	9
    20-28|	13
    29-38|	17
- Approximate Numeric Type
    - Float is Approximate-number data type, which means that not all values in the data type range can be represented exactly. Decimal/Numeric is Fixed-Precision data type, which means that all the values in the data type range can be represented exactly with precision and scale.

    |Data type|	Range|	Storage|
    |-|-|-|
    |float<br>n<=24|	-3.40E38 to -1.18E-38, 0 and 1.18E38|	4 bytes
    |float<br> 24<n<=53|	- 1.79E+308 to -2.23E-308, 0 and 2.23E-308 to 1.79E+308| 8 bytes
    |real| Functionally equivalent to float(24)
- Date and Time

    | Data type| Description| Storage|
    |-|-|-|
    | datetime        | From January 1, 1753 to December 31, 9999 with an accuracy of 3.33 ms        | 8 bytes    |
    | datetime2       | From January 1, 0001 to December 31, 9999 with an accuracy of 100 ns         | 6–8 bytes  |
    | smalldatetime   | From January 1, 1900 to June 6, 2079 with an accuracy of 1 minute            | 4 bytes    |
    | date            | Store a date only. From January 1, 0001 to December 31, 9999                 | 3 bytes    |
    | time            | Store a time only to an accuracy of 100 nanoseconds                          | 3–5 bytes  |
    | datetimeoffset  | Same as datetime2 with the addition of a time zone offset                    | 8–10 bytes |
- Tables
    - CREATE SCHEMA: Creates a schema in the current database. The CREATE SCHEMA transaction can also create tables and views within the new schema, and set GRANT, DENY, or REVOKE permissions on those objects.<br>
    Olyan mint egy namespace.
    - CREATE TABLE: Creates a new table in the database.
- ```sql
    CREATE TABLE
    { database_name.schema_name.table_name | schema_name.table_name | table_name }
    [ AS FileTable ]
    ( { <column_definition>
        | <computed_column_definition>
        | <column_set_definition>
        | [ <table_constraint> ] [ ,... n ]
        | [ <table_index> ] }
          [ ,... n ]
          [ PERIOD FOR SYSTEM_TIME ( system_start_time_column_name
             , system_end_time_column_name ) ]
      )
    [ ON { partition_scheme_name ( partition_column_name )
           | filegroup
           | "default" } ]
    [ TEXTIMAGE_ON { filegroup | "default" } ]
    [ FILESTREAM_ON { partition_scheme_name
           | filegroup
           | "default" } ]
    [ WITH ( <table_option> [ ,... n ] ) ]
    [ ; ]
    ```
- ```sql
    create schema Sales;
    GO

    create table Sales.Customers (
        CustomerID int not null,
        Name nvarchar(50) not null
    );
    ```
    - Table and Column Names 
        - STANDARD IDENTIFIERS
            - @ defines a variable or parameter
            - '#' defines a temporary object (the object is available only from the current connection)
            - '##' defines a global temporary object
        - DELIMITED IDENTIFIERS
            - Any identifier that does not adhere to the standard identifier neming rules must be delimited using either quotation marks (") or square brackets ([])
            - ```sql
                -- Standard identifiers
                create table HR.Employees (
                    EmloyeeId int not null
                );

                -- Delimited identifiers
                set QUOTED_IDENTIFIER ON;

                create table HR."Organisaton Employees"(
                    "Employee Id" int not null
                );
                -- or
                create table HR.[Organisaton Employees](
                    [Employee Id] int not null
                );
                ```
        - Naming Guidelines
            - PascalCasing
            - Avoid abbreviations (rövidítéseket)
            - Inkább hosszú érthető név
        - Choosing Data Types
            - Lehető legkevesebb helyet foglalja
            - Lehetőleg egyből jót válasszunk, mert nehéz lehet váltani fejlesztés közben
            - Inkább váltzó hosszút használjunk
        - NULL or NOT NULL
            - Akkor engedélyezzük a NULL-t ha az oszlopot opcionális megadni
            - Ne használjunk más értéket helyette (pl -1-et, mert az AVG-ben bezavarhat)
        - IDENTITY 
            - Creates an identity column in a table.
            - ```sql
                IDENTITY [ (seed , increment) ]
                ```
            - seed: The value that is used for the very first row loaded into the table.
            - increment: The incremental value that is added to the identity value of the previous row that was loaded.<br>
            You must specify both the seed and increment or neither. If neither is specified, the default is (1,1).
            - ```sql
                CREATE TABLE new_employees (
                id_num INT IDENTITY(1, 1),
                fname VARCHAR(20),
                minit CHAR(1),
                lname VARCHAR(30)
                );
                ```
        - Compression
            - Be lehet kapcsolni és akkor változó hosszú típusokat használ minden helyett
            - sor vagy oszlopszintű
            - ```sql
                alter table HR.Employees
                rebuild
                with(DATA_COMPRESSION = PAGE);
                ```

## Lesson 2
- Validatring Data
    - Use constrains
- Implementing Declarative Data Integrity
    - There are 5 types of constrains:
        - PRIMARY KEY, AND UNIQUE CONSTRAINTS
            - A table typically has a column or combination of columns that contain values that uniquely identify each row in the table.
            - Both a UNIQUE constraint and a PRIMARY KEY constraint enforce uniqueness, use a UNIQUE constraint instead of a PRIMARY KEY constraint when you want to enforce the uniqueness of a column (or combination of columns) that isn't the primary key.<br>
            Unlike PRIMARY KEY constraints, UNIQUE constraints allow for the value NULL
            - ```sql
                -- Primary key
                CREATE TABLE MyTable
                (  
                MyTableID int NOT NULL,   
                CONSTRAINT PK_MyTableID PRIMARY KEY(MyTableID)   
                ); 

                -- Unique key
                CREATE TABLE MyTbale (  
                MyTableID int NOT NULL,   
                CONSTRAINT U_MyTableID UNIQUE(MyTableID)   
                ); 

                -- Primary key
                alter table MyTable
                    add PRIMARY KEY (MyTableID)

                -- Unique key
                alter table MyTable
                    add UNIQUE(MyTableID)
                ```
        - FOREGIN KEY CONSTRAINTS
            - A foreign key (FK) is a column or combination of columns that is used to establish and enforce a link between the data in two tables to control the data that can be stored in the foreign key table. In a foreign key reference, a link is created between two tables when the column or columns that hold the primary key value for one table are referenced by the column or columns in another table. This column becomes a foreign key in the second table.
            - NO ACTION (the default): The Database Engine raises an error and the delete or update action on the row in the parent table is rolled back.
            - SET NULL: All the values that make up the foreign key are set to NULL when the corresponding row in the parent table is updated or deleted.
            - SET DEFAULT: All the values that make up the foreign key are set to their default values if the corresponding row in the parent table is updated or deleted.
            - CASCADE: Corresponding rows are updated or deleted in the referencing table when that row is updated or deleted in the parent table.   
                - ```sql
                    create table Test.Customers(
                    CustomerID int primary key
                    );
                    create table Test.Orders (
                    OrderID int primary key,
                    CustomerId int null
                        references Test.Customers
                            on delete set null
                            on update cascade
                    );
                    ```
        - CHECK CONSTRAINTS
            - CHECK constraints enforce domain integrity by limiting the values that are accepted by one or more columns. You can create a CHECK constraint with any logical (Boolean) expression that returns TRUE or FALSE based on the logical operators.
            - ```sql
                alter table Products
                add check(Price >= 0.0);
                ```
        - EXTENDING CHECK CONSTRAINTS WITH USER-DEFINED FUNCTIONS
            - UDF = USER-DEFINED FUNCTION
            - Ha jól értem meg lehet hívni sql kódból függvényeket
            - ```cs
                // C#
                [SqlFunction(IsDeterministic = true, DataAccessKind.None)]
                static public SqlBoolean fnIsPhoneNumber(SqlString phoneNumber) 
                {
                    if (phoneNumber.IsNull;) {
                        return SqlBoolean.Null
                    }
                    return System.Text.RegularExperssion.Regex
                    .IsMatch(phoneNumber.Velue, @"^\([1-9]\d{2}\)\s?\d{3}\-\d{4}$");
                }
                ```
            - ```sql
                create table Test.Contacts (
                    ContactID int identity primary key,
                    Name nvarchar(50) not null
                    PhoneNumber varchar(20) null,
                    constraint CKContactsPhoneNumber
                        check(dbo.fnIsPhoneNumber(PhoneNumber) = cast(1 as bit))
                );
                ```

# Using Additional Query Techniques

## Lesson 1
- Building Recursive Queries with  CTEs (CTE = Common Table Expression is a temporary result set in SQL that you can reference within a single query).
- A CTE allows you to define a temporary named result set that available temporarily in the execution scope of a statement such as SELECT, INSERT, UPDATE, DELETE, or MERGE.
- Common Table Expression
    - ```sql
        WITH expression_name[(column_name [,...])]
        AS
            (CTE_definition)
        SQL_statement;
        ```
    - ```sql
        use SampleDatabase

        go

        WITH cte_sales_amounts (staff, sales, year) AS (
            SELECT    
                first_name + ' ' + last_name, 
                SUM(quantity * list_price * (1 - discount)),
                YEAR(order_date)
            FROM    
                sales.orders o
            INNER JOIN sales.order_items i ON i.order_id = o.order_id
            INNER JOIN sales.staffs s ON s.staff_id = o.staff_id
            GROUP BY 
                first_name + ' ' + last_name,
                year(order_date)
        )

        SELECT
            staff, 
            sales
        FROM 
            cte_sales_amounts
        WHERE
            year = 2018;
        ```  
        - First, we defined cte_sales_amounts as the name of the common table expression. the CTE returns a result that that consists of three columns staff, year, and sales derived from the definition query.      
        - Second, we constructed a query that returns the total sales amount by sales staff and year by querying data from the orders, order_items and staffs tables.
        - Third, we referred to the CTE in the outer query and select only the rows whose year are 2018.
    - ```sql
        use AdventureWorks2025

        ;with EmpTitle as
        (select JobTitle, COUNT(*) as numtitles
            from HumanResources.Employee
            group by JobTitle)
        select b.BusinessEntityID, b.JobTitle, a.numtitles
        from EmpTitle as a inner join HumanResources.Employee as b on a.JobTitle = b.JobTitle;
        ```
        - Azoknak a dolgozóknak a számát adja meg, akik az adott munkakőrben dolgoznak.
- Recursive CTE: a recursive CTE is one in which an initial CTE is repeatedly executed to return subsets of data until the complete result set is obtained.
    - In general, a recursive CTE has three parts:
        - An initial query that returns the base result set of the CTE. The initial query is called an anchor member.
        - A recursive query that references the common table expression, therefore, it is called the recursive member. The recursive member is union-ed with the anchor member using the UNION ALL operator.
        - A termination condition specified in the recursive member that terminates the execution of the recursive member.
    - ```sql
            WITH cte_numbers(n, weekday) 
        AS (
            SELECT 
                0, 
                DATENAME(DW, 0)
            UNION ALL
            SELECT    
                n + 1, 
                DATENAME(DW, n + 1)
            FROM    
                cte_numbers
            WHERE n < 6
        )
        SELECT 
            weekday
        FROM 
            cte_numbers;
        ```
        - The anchor member returns the Monday
            - ```sql
                SELECT 
                    0, 
                    DATENAME(DW, 0)
                ```
        - The recursive member returns the next day starting from the Tuesday till Sunday.
            - ```sql
                SELECT    
                    n + 1, 
                    DATENAME(DW, n + 1)
                FROM    
                    cte_numbers
                WHERE n < 6
                ```
        - The condition in the WHERE clause is the termination condition that stops the execution of the recursive member when n is 6
            - ```sql
                n < 6
                ```
    - ```sql
        USE AdventureWorks2025;

        WITH DirectReports (ManagerID, EmployeeID, Title, DeptID, Level)
        AS (
        -- Anchor member definition
        SELECT e.ManagerID,
                e.EmployeeID,
                e.Title,
                edh.DepartmentID,
                0 AS Level
            FROM dbo.MyEmployees AS e
                INNER JOIN HumanResources.EmployeeDepartmentHistory AS edh
                    ON e.EmployeeID = edh.BusinessEntityID
                    AND edh.EndDate IS NULL
            WHERE ManagerID IS NULL
            UNION ALL
        -- Recursive member definition
            SELECT e.ManagerID,
                e.EmployeeID,
                e.Title,
                edh.DepartmentID,
                Level + 1
            FROM dbo.MyEmployees AS e
                INNER JOIN HumanResources.EmployeeDepartmentHistory AS edh
                    ON e.EmployeeID = edh.BusinessEntityID
                    AND edh.EndDate IS NULL
                INNER JOIN DirectReports AS d
                    ON e.ManagerID = d.EmployeeID)
        -- Statement that executes the CTE
        SELECT ManagerID,
            EmployeeID,
            Title,
            DeptID,
            Level
        FROM DirectReports
            INNER JOIN HumanResources.Department AS dp
                ON DirectReports.DeptID = dp.DepartmentID
        WHERE dp.GroupName = N'Sales and Marketing'
            OR Level = 0;
        ```
        - Returning a hierarchical list of employees, starting with the highest ranking employee, in the AdventureWorks2025 database.
        - The recursive CTE, DirectReports, defines one anchor member and one recursive member.
        - The anchor member returns the base result set T0. This is the highest ranking employee in the company. That is, an employee who doesn't report to a manager.
        - The recursive member returns the direct subordinates of the employee in the anchor member result set. This is achieved by a join operation between the Employee table and the DirectReports CTE. It's this reference to the CTE itself that establishes the recursive invocation. 
        - The recursive member is activated repeatedly. The second iteration of the recursive member uses the single-row result set in step 3 (containing an EmployeeID of 273) as the input value.

## Lesson 2: Implementing Subqueries
- Noncorrelated Subqueries
    - A non-correlated subquery is a subquery that can be executed independently of the outer query. The subquery does not depend on the outer query for its results.
    - ```sql
        use AdventureWorks2025

        select a.ProductID, a.Name, a.ListPrice
        from Production.Product as a
        where a.ListPrice > (select avg(b.ListPrice) from Production.Product as b)
        ```
    - ```sql
        SELECT
            order_id,
            order_date,
            customer_id
        FROM
            sales.orders
        WHERE
            customer_id IN (
                SELECT
                    customer_id
                FROM
                    sales.customers
                WHERE
                    city = 'New York'
            )
        ORDER BY
            order_date DESC;
        ```
- Correlated Subqueries
    - A correlated subquery is a subquery that depends on values from the outer query.
    - Vissza adja az összes olyan árunak az id-ját és árát, ami el lett adva.
    - ```sql
        use AdventureWorks2025

        select a.ProductID, a.ListPrice
        from Production.Product as a
        where exists (select 1 from Sales.SalesOrderDetail as b
                        where b.ProductID = a.ProductID)
        ```

## Lesson 3: Applying Ranking Functions
- Ranking functions return a ranking value for each row in a partition.
    - ROW_NUMBER: Numbers the output of a result set.
    - RANK: Returns the rank of each row within the partition of a result set.
    - DENSE_RANK: Returns the rank of each row within a result set partition, with no gaps in the ranking values.
    - NTILE: Distributes the rows in an ordered partition into a specified number of groups.
    - ```sql
        SELECT 
        name, recovery_model_desc
        FROM sys.databases 
        WHERE database_id < 5
        ORDER BY name ASC;
        ```
    - ```sql
        SELECT 
        ROW_NUMBER() OVER(ORDER BY name ASC) AS Row#,
        name, recovery_model_desc
        FROM sys.databases 
        WHERE database_id < 5;
        ```

# Programming Mincrosoft SQL Server with T-SWL User-Defined Stored Procedurs, Functions, Triggers, and Views
## Lesson 1: Stored Procedures
- A stored procedure is a prepared SQL code that you can save, so the code can be reused over and over again.
    - ```sql
        CREATE PROCEDURE SelectAllCustomers
        AS
        SELECT * FROM Customers
        ```
    - ```sql
        EXEC SelectAllCustomers;
        ```
    - Stored Procedure With Parameters
    - ```sql
        CREATE PROCEDURE SelectAllCustomersCity @City nvarchar(30)
        AS
        SELECT * FROM Customers WHERE City = @City
        ```
    - ```sql
        EXEC SelectAllCustomersCity @City = 'London';
        ```

## Lesson 2: User-Defined Functions
- System Functions
    - https://learn.microsoft.com/en-us/sql/relational-databases/system-functions/system-functions-category-transact-sql?view=sql-server-ver17
- User-Defined Functions
    - User-defined functions are routines that accept parameters, perform an action, such as a complex calculation, and return the result of that action as a value. The return value can either be a single scalar value or a result set.

## Lesson 3: Triggers
- DML Triggers
    - The DML trigger is a special type of stored procedure that automatically takes effect when a data manipulation language (DML) event takes place that affects the table or view defined in the trigger. DML events include INSERT, UPDATE, or DELETE statements. 
- DDL Triggers
    - DDL triggers fire in response to various Data Definition Language (DDL) events. These events primarily correspond to Transact-SQL statements that start with the keywords CREATE, ALTER, DROP, GRANT, DENY, REVOKE, or UPDATE STATISTICS.
- Logon Triggers
    - Logon triggers fire stored procedures in response to a LOGON event. This event is raised when a user session is established with an instance of SQL Server. Logon triggers fire after the authentication phase of logging in finishes, but before the user session is established. Therefore, all messages originating inside the trigger that would typically reach the user, such as error messages and messages from the PRINT statement, are diverted to the SQL Server error log. Logon triggers don't fire if authentication fails.

## Lesson 4: Views
- A view is a virtual table whose contents are defined by a query. Like a table, a view consists of a set of named columns and rows of data. Unless indexed, a view does not exist as a stored set of data values in a database. The rows and columns of data come from tables referenced in the query defining the view and are produced dynamically when the view is referenced.
- ```sql
    use AdventureWorks2025

    go

    CREATE VIEW HumanResources.EmployeeHireDate
    AS
    SELECT p.FirstName,
        p.LastName,
        e.HireDate
    FROM HumanResources.Employee AS e
    INNER JOIN Person.Person AS p
        ON e.BusinessEntityID = p.BusinessEntityID;
    GO

    -- Query the view
    SELECT FirstName,
        LastName,
        HireDate
    FROM HumanResources.EmployeeHireDate
    ORDER BY LastName;
    ```
- Modify data through a view
    - You can modify the data of an underlying base table in SQL Server by using SQL Server Management Studio or Transact-SQL.
    - Requires UPDATE, INSERT, or DELETE permissions on the target table, depending on the action being performed.
- Partitioned Views
    - A partitioned view is a technique in SQL Server where data from multiple tables (typically related to the same logical entity but stored across multiple physical tables) is presented as a single unified view. This is especially useful in scenarios where the data can be logically split into segments, such as time-based data, geographical data, or large-scale data sets that are distributed across multiple tables for performance, scalability, or management reasons.
    - In SQL Server, partitioned views use a UNION ALL operation to combine data from multiple tables into a single view, which allows you to query the data as though it were in one large table. Each individual table in the view typically stores a subset of the data, and the partitioned view helps you access this data seamlessly.
- Create indexed views
    - The first index created on a view must be a unique clustered index. After the unique clustered index has been created, you can create more nonclustered indexes. Creating a unique clustered index on a view improves query performance, because the view is stored in the database in the same way a table with a clustered index is stored. The query optimizer can use indexed views to speed up the query execution. The view doesn't have to be referenced in the query for the optimizer to consider that view for a substitution.

# Techniques to Improve Quers Performance

## Lessson 1: Tuning Queries
- Pege Reads
    - Represents tge number of 8KB data pages accessed by the SQL Server storage engine while executing a query.
- Query Execution Time
    - Affected by blocking (locks) as well as resource connection on the server
- Theoretical Query Execution Order
    1. FROM, JOIN, APPLY, ON 
    2. WHERE 
    3. GROUP BY 
    4. HAVING 
    5. SELECT 
    6. ORDER BY
    7. TOP  
    8. FOR XML
- Graphical Execution Plan
    - ![alt text](media/image-1.png)

## Lesson 2: Creating Indexes
- An index is an on-disk structure associated with a table or view that speeds retrieval of rows from the table or view. An index contains keys built from one or more columns in the table or view. These keys are stored in a structure (B-tree) that enables SQL Server to find the row or rows associated with the key values quickly and efficiently.
- Covered index
    - A covering index is a regular index that provides all the data required for a query without having to access the actual table. When a query is executed, the database looks for the required data in the index tree, retrieves it, and returns the result.
- Clustered index
    - Clustered indexes sort and store the data rows in the table or view based on their key values. These key values are the columns included in the index definition. There can be only one clustered index per table, because the data rows themselves can be stored in only one order.
    - The only time the data rows in a table are stored in sorted order is when the table contains a clustered index. When a table has a clustered index, the table is called a clustered table. If a table has no clustered index, its data rows are stored in an unordered structure called a heap.
- Nonclustered index
    - Nonclustered indexes have a structure separate from the data rows. A nonclustered index contains the nonclustered index key values and each key value entry has a pointer to the data row that contains the key value.
    - The pointer from an index row in a nonclustered index to a data row is called a row locator. The structure of the row locator depends on whether the data pages are stored in a heap or a clustered table. For a heap, a row locator is a pointer to the row. For a clustered table, the row locator is the clustered index key.

# Extending Mincrosoft SQL Server Functionality with XML, SQLCLR, and Filestream

## Lesson 1: Working with XML
- Retreving Tabular Data as XML
    - ```sql
        use AdventureWorks2025

        select 
            c.CustomerID as "@Id",
            c.AccountNumber as "@AccountNumber",
            c.ModifiedDate as "@Date",
            (
                select	
                    soh.SalesOrderID as "@Id",
                    soh.OrderDate as "@OrderDate",
                    soh.ShipDate as "@Shipdate"
                from Sales.SalesOrderHeader as soh
                where soh.CustomerID = c.CustomerID
                for xml path('Order'), type
                ) as "Orders"
        from Sales.Customer as c
        where c.CustomerID in (1, 2)
        for xml path('Customer'), root('Customers')
        ```

## Lesson 2: Using SQLCLR and Filestream
- Filestream: FILESTREAM enables SQL Server-based applications to store unstructured data, such as documents and images, on the file system. Applications can use the rich streaming APIs and performance of the file system and at the same time maintain transactional consistency between the unstructured data and corresponding structured data.
- To use CLR within the database, you must perforom the following steps:
 1. SQL Server-ben engedélyezni kell a CLR kód futtatását
 2. Olyan kódot kell írni, ami jó .NET-hez (C#, VB)
 3. Assemblybe kell compileolni a kódot
 4. Be kell tölteni az assemblyt SQL Serverbe
 5. Létre kell hozni egy adatbázis objektumot, aminek az assemblyre ekll mutatnia DDL-t használva

# Extending Mincrosoft SQL Server Functionality with the Spatial, Full-Text Searc, and Service Broker

## Lesson 1: Implementing Spatial Data Types
- A spatial data type-pal geogrphic és geometric adatot is lehet kezelni. Lehet velük tárolni hosszúsági és szélességi információket is.
- A két típus:
    - geometry
    - geogrphy
- Tábla létrhozása
- ```sql
    use Northwind

    create table Museum
    (MuseumID int identity primary key,
    MuseumName nchar(50),
    MuseumAddress nvarchar(200),
    Location geography);
    ```
    - Adatokkal föltöltés
- ```sql
    use Northwind

    insert into Museum
    (MuseumName, MuseumAddress, Location)
    values
    ('COSI Columbus',
        '333 West Broad Street, Columbus, OH 43215',
        geography::Parse('POINT(-83.0086 39.95954)')),
    ('Columbus Art Museum',
        '480 Eastr Board Street, Colombus, OH 1234',
        geography::Parse('POINT(-82.98775 39.963775)'));
    ```
    - Lehet vonalat rajzolni, nagyon menő
- ```sql
    SELECT 
        M1.MuseumName,
        M2.MuseumName,
        geography::STLineFromText(
            'LINESTRING(' +
            CAST(M1.Location.Long AS VARCHAR(20)) + ' ' + CAST(M1.Location.Lat AS VARCHAR(20)) + ', ' +
            CAST(M2.Location.Long AS VARCHAR(20)) + ' ' + CAST(M2.Location.Lat AS VARCHAR(20)) +
            ')', 4326
        ) AS LineBetween
    FROM Museum M1
    JOIN Museum M2 
        ON M1.MuseumID = 1 
    AND M2.MuseumID = 2;
    ```
-    ![alt text](media/image-2.png)

## Lesson 2: Implementiing Full-Text Search
- Full-text search allows to bulid advaned queries that go beyond the capabilities of the traditional SELECT command with the LIKE  argument. By using the CONTAINS and FREETEXT predicates as well as the CINTANSTABLE and FREETEXTTABLE functions, you can write queries that retun the followin:
    - Infectional forms of verb that you input
    - Matched results from data stored as a PDF file in a varbinary(max) column
    - Synonyms of the search term located throught thr thesaurus search
## Lesson 3: Implementing Service Broker Solutions
- A native, in-database messaging and queuing framework within MS SQL Server and Azure SQL Managed Instance, designed for building reliable, asynchronous, distributed applications.

# An Introduction to Mincrosoft SQL Server Manageability Features

## Lesson 1: Integrating Database Mail
- El lehet emailben küldeni a lekérdezések eredményét és más dolgokat emailben a felhasználóknak

## Lession 2: IMplementing Scripts by Using windows PowerShell
- Can automate the process of deplying applications, enumeration of database objects and the object properties.
- ![alt text](media/image-3.png)
- Nagyon sok mindent meg lehet csinálni terminálból

## Lession 3: Tracking Data Changes
- Using change data capture or change tracking in applications to track changes in a database, instead of developing a custom solution, has the following benefits:
    - Schema changes aren't required. You don't have to add columns, add triggers, or create side table in which to track deleted rows or to store change tracking information if columns can't be added to the user tables.
    - There's a built-in cleanup mechanism. Cleanup for change tracking is performed automatically in the background. Custom cleanup for data that is stored in a side table isn't required.
    - Functions are provided to obtain change information.
    - There's low overhead to DML operations. Synchronous change tracking will always have some overhead. However, using change tracking can help minimize the overhead. The overhead will frequently be less than that of using alternative solutions, especially solutions that require the use of triggers.
    - Change tracking is based on committed transactions. The order of the changes is based on transaction commit time. This allows for reliable results to be obtained when there are long-running and overlapping transactions. Custom solutions that use timestamp values must be designed to handle these scenarios.
    - Standard tools are available that you can use to configure and manage. SQL Server provides standard DDL statements, SQL Server Management Studio, catalog views, and security permissions.
- Change data capture (CDC)
    - Provides historical change information for a user table by capturing both the fact that DML changes were made and the actual data that was changed. Changes are captured by using an asynchronous process that reads the transaction log and has a low impact on the system.
    - As shown in the following illustration, the changes that were made to user tables are captured in corresponding change tables. These change tables provide a historical view of the changes over time. 
    - ![alt text](media/image-4.png)
- Change tracking
    - Change tracking captures the fact that rows in a table were changed, but doesn't capture the data that was changed. This enables applications to determine the rows that have changed with the latest row data being obtained directly from the user tables. Therefore, change tracking is more limited in the historical questions it can answer compared to change data capture. However, for those applications that don't require the historical information, there's far less storage overhead because of the changed data not being captured. A synchronous tracking mechanism is used to track the changes. This has been designed to have minimal overhead to the DML operations.
    - The following illustration shows a synchronization scenario that would benefit by using change tracking. In the scenario, an application requires the following information: all the rows in the table that were changed since the last time that the table was synchronized, and only the current row data. Because a synchronous mechanism is used to track the changes, an application can perform two-way synchronization and reliably detect any conflicts that might have occurred.
    - ![alt text](media/image-5.png)

# Hasznos oldalak
- https://www.sqlservertutorial.net/
- https://learn.microsoft.com/en-us/sql/relational-databases/track-changes/track-data-changes-sql-server?view=sql-server-ver17































