# SQL Zoo

1. Show the population of 'Germany'
```sql
SELECT 
	population 
FROM 
	world
WHERE 
	name = 'Germany';
```
2. Show the name and the population for 'Sweden', 'Norway' and 'Denmark'
```sql
SELECT 
  name, 
  population 
FROM 
  world 
WHERE 
  name IN ('Sweden', 'Norway', 'Denmark');
```
3. Show the country and the area for countries with an area between 200,000 and 250,000.
```sql
SELECT 
  name, 
  area 
FROM 
  world 
WHERE 
  area BETWEEN 200000 
  AND 250000;
```
4. Show the name for the countries that have a population of at least 200 million. 200 million is 200000000, there are eight zeros.
```sql
SELECT 
  name 
FROM 
  world 
WHERE 
  population >= 200000000;
```

5. Give the name and the **per capita GDP** for those countries with a population of at least 200 million.
```sql
SELECT 
  name, 
  gdp / population AS 'per capita GDP' 
FROM 
  world 
WHERE 
  population >= 200000000;

```

6. Show the `name` and `population` in millions for the countries of the `continent` 'South America'. Divide the population by 1000000 to get population in millions.
```sql
SELECT 
  name, 
  population / 1000000 AS 'population(m)' 
FROM 
  world 
WHERE 
  continent = 'South America';
```
7. Show the `name` and `population` for France, Germany, Italy
```sql
SELECT 
  name, 
  population 
FROM 
  world 
WHERE 
  name IN ('France', 'Germany', 'Italy');
```
8. Show the countries which have a `name` that includes the word 'United'
```sql
SELECT 
  name 
FROM 
  world 
WHERE 
  name like '%United%';
```
9. Show the countries that are big by area or big by population. Show name, population and area.
```sql
SELECT 
  name, 
  population, 
  area 
FROM 
  world 
WHERE 
  area > 3000000 
  OR population > 250000000;
```

10. Show the countries that are big by area (more than 3 million) or big by population (more than 250 million) but not both. Show name, population and area.

- Australia has a big area but a small population, it should be **included**.
- Indonesia has a big population but a small area, it should be **included**.
- China has a big population **and** big area, it should be **excluded**.
- United Kingdom has a small population and a small area, it should be **excluded**.
```sql
SELECT 
  name, 
  population, 
  area 
FROM 
  world 
WHERE 
  (
    area > 3000000 
    OR population > 250000000
  ) 
  AND NOT (
    area > 3000000 
    AND population > 250000000
  );

```
11. Show the name and population in millions and the GDP in billions for the countries of the continent 'South America'. Use the [ROUND]

**For South America show population in millions and GDP in billions both to 2 decimal places.**
```sql
SELECT 
  name, 
  ROUND(population / 1000000, 2) AS 'population(m)', 
  ROUND (gdp / population, 2) AS 'gdp' 
FROM 
  world 
WHERE 
  continent = 'South America';
```

12. Show the name and per-capita GDP for those countries with a GDP of at least one trillion (1000000000000; that is 12 zeros). Round this value to the nearest 1000.
**Show per-capita GDP for the trillion dollar countries to the nearest $1000.**
```sql
SELECT 
  name, 
  ROUND(gdp / population,-3) AS 'gdp' 
FROM 
  world 
WHERE 
  gdp > 1000000000000;
```

13. Greece has capital Athens.
Each of the strings 'Greece', and 'Athens' has 6 characters.
**Show the name and capital where the name and the capital have the same number of characters.**
```sql
SELECT 
  name, 
  capital 
FROM 
  world 
WHERE 
  LEN(name) = LEN(capital);
```
14. The capital of Sweden is Stockholm. Both words start with the letter 'S'.
**Show the name and the capital where the first letters of each match. Don't include countries where the name and the capital are the same word.**
```sql
SELECT 
  name, 
  capital 
FROM 
  world 
WHERE 
  LEFT(name, 1) = LEFT(capital, 1) 
  AND name <> capital;
```

15. **Equatorial Guinea** and **Dominican Republic** have all of the vowels (a e i o u) in the name. They don't count because they have more than one word in the name.
**Find the country that has all the vowels and no spaces in its name.**
```sql
SELECT 
  name 
FROM 
  world 
WHERE 
  name LIKE '%a%' 
  AND name LIKE '%e%' 
  AND name LIKE '%i%' 
  AND name LIKE '%o%' 
  AND name LIKE '%u%' 
  AND name NOT LIKE '% %';
```
16. List each country name where the population is larger than that of 'Russia'.
```sql
SELECT 
  name 
FROM 
  world 
WHERE 
  population > (
    SELECT 
      population 
    FROM 
      world 
    WHERE 
      name = 'Russia'
  );

```
17. Show the countries in Europe with a per capita GDP greater than 'United Kingdom'.
```sql
SELECT 
  name 
FROM 
  world 
WHERE 
  continent = 'Europe' 
  AND gdp / population > (
    SELECT 
      gdp / population 
    FROM 
      world 
    WHERE 
      name = 'United Kingdom'
  );

```
18. List the name and continent of countries in the continents containing either Argentina or Australia. Order by name of the country.
```sql
SELECT 
  name, 
  continent 
FROM 
  world 
WHERE 
  continent IN (
    SELECT 
      continent 
    FROM 
      world 
    WHERE 
      name IN ('Argentina', 'Australia')
  );
```

19. Which country has a population that is more than United Kingdom but less than Germany? Show the name and the population.
```sql
SELECT 
  name, 
  population 
FROM 
  world 
WHERE 
  population > (
    SELECT 
      population 
    FROM 
      world 
    WHERE 
      name = 'United Kingdom'
  ) 
  AND population < (
    SELECT 
      population 
    FROM 
      world 
    WHERE 
      name = 'Germany'
  );
```

20. Which countries have a GDP greater than every country in Europe? [Give the name only.] (Some countries may have NULL gdp values)
```sql
SELECT 
  name 
FROM 
  world 
WHERE 
  gdp > (
    SELECT 
      MAX(gdp) 
    FROM 
      world 
    WHERE 
      continent = 'Europe'
  );
```

21. Find the largest country (by area) in each continent, show the continent, the name and the area:
```sql
SELECT 
  continent, 
  name, 
  area 
FROM 
  world 
WHERE 
  area IN (
    SELECT 
      MAX(area) 
    FROM 
      world 
    GROUP BY 
      continent
  );

```

22. List each continent and the name of the country that comes first alphabetically.
```sql
WITH tbl AS (
  SELECT 
    continent, 
    name, 
    row_number() OVER (
      PARTITION BY continent 
      ORDER BY 
        continent, 
        name
    ) AS rownum 
  FROM 
    world
) 
SELECT 
  continent, 
  name 
FROM 
  tbl 
WHERE 
  rownum = 1;

```
