# sql_practice
A collection of SQL query from websites below.

- [sql-practice](https://www.sql-practice.com/)

  SQL Practice consists of 2 databases: 
  1. hospital.db 
  2. northwind.db

- [sql-zoo](https://sqlzoo.net/wiki/SQL_Tutorial)

### SQL Practice (Hospital.db)

1. Based on cities where our patient lives in, write a query to display the list of unique city starting with a vowel (a, e, i, o, u). Show the result order in ascending by city.

```sql
SELECT 
  DISTINCT(city) 
FROM 
  patients 
WHERE 
  city LIKE 'a%' 
  OR city LIKE 'e%' 
  OR city LIKE 'i%' 
  OR city LIKE 'o%' 
  OR city LIKE 'u%' 
ORDER BY 
  city;
```
  
2. Show unique birth years from patients and order them by ascending.
```SQL
SELECT 
  DISTINCT(
    YEAR(birth_date)
  ) 
FROM 
  patients 
ORDER BY
  YEAR(birth_date);
```
3. Show unique first names from the patients table which only occurs once in the list.
```SQL
SELECT 
  first_name 
FROM 
  patients 
GROUP BY
  first_name 
HAVING
  COUNT(first_name)= 1;
```
4. Show patient_id and first_name from patients where their first_name start and ends with 's' and is at least 6 characters long.
```SQL
SELECT 
	patient_id, 
	first_name 
FROM 
	patients 
WHERE
	first_name LIKE "s%s" 
	AND LEN(first_name)>= 6;
```
5. Show patient_id, first_name, last_name from patients whos diagnosis is 'Dementia'.
```SQL
 SELECT 
  p.patient_id, 
  p.first_name, 
  p.last_name 
FROM 
  patients p 
  JOIN admissions a ON p.patient_id = a.patient_id 
WHERE 
  a.diagnosis = 'Dementia';
```
6. Show the total amount of male patients and the total amount of female patients in the patients table.
Display the two results in the same row.
```SQL
SELECT 
  COUNT(*) 
FROM
  patients 
WHERE
  gender = 'M' 
UNION
SELECT
   COUNT(*) 
FROM 
  patients 
WHERE
  gender = 'F';
```
7. Show first and last name, allergies from patients which have allergies to either 'Penicillin' or 'Morphine'. Show results ordered ascending by allergies then by first_name then by last_name.
```SQL
 SELECT 
  first_name, 
  last_name, 
  allergies 
FROM 
  patients 
WHERE 
  allergies = 'Penicillin' 
  OR allergies = 'Morphine' 
ORDER BY 
  allergies, 
  first_name, 
  last_name;
```
8. Show patient_id, diagnosis from admissions. Find patients admitted multiple times for the same diagnosis.
```SQL
 SELECT 
  patient_id, 
  diagnosis 
FROM 
  admissions 
GROUP BY 
  patient_id, 
  diagnosis 
HAVING 
  COUNT(diagnosis)> 1;
```
9. Show the city and the total number of patients in the city.
Order from most to least patients and then by city name ascending.
```SQL
SELECT 
  city, 
  COUNT(
    DISTINCT(patient_id)
  ) AS num_patients 
FROM 
  patients 
GROUP BY 
  city 
ORDER BY
  num_patients DESC, 
  city;
```
10. Show first name, last name and role of every person that is either patient or doctor.
The roles are either "Patient" or "Doctor"
```SQL
SELECT
  first_name, 
  last_name, 
  'patient' 
FROM 
  patients 
UNION ALL 
SELECT
  first_name, 
  last_name, 
  'doctor' 
FROM
  doctors;
```

11. Show all allergies ordered by popularity. Remove NULL values from query.
```SQL
SELECT
  allergies, 
  COUNT(allergies) AS popularity 
FROM
  patients 
WHERE 
  allergies IS NOT NULL 
GROUP BY
  allergies 
ORDER BY
  popularity DESC;
```
12. Show all patient's first_name, last_name, and birth_date who were born in the 1970s decade. Sort the list starting from the earliest birth_date.
```SQL
SELECT 
  first_name, 
  last_name, 
  birth_date 
FROM 
  patients 
WHERE 
  YEAR(birth_date) BETWEEN 1970 
  AND 1979 
ORDER BY 
  birth_date;
```
13. We want to display each patient's full name in a single column. Their last_name in all upper letters must appear first, then first_name in all lower case letters. Separate the last_name and first_name with a comma. Order the list by the first_name in decending order
EX: SMITH,jane
```SQL
SELECT 
  CONCAT(
    UPPER(last_name), 
    ",", 
    LOWER(first_name)
  ) AS fullname 
FROM 
  patients 
ORDER BY 
  first_name DESC;
```
14. Show the province_id(s), sum of height; where the total sum of its patient's height is greater than or equal to 7,000.
```SQL
 SELECT 
	 province_id, 
	 SUM(height) 
FROM 
	 patients 
GROUP BY 
	province_id 
HAVING 
	SUM(height) >= 7000;
```
15. Show the difference between the largest weight and smallest weight for patients with the last name 'Maroni'
```SQL
SELECT
	MAX(weight)- MIN(weight) 
FROM 
	patients 
WHERE
	last_name = 'Maroni';
```
16. Show all of the days of the month (1-31) and how many admission_dates occurred on that day. Sort by the day with most admissions to least admissions.
```sql
SELECT 
  DAY(admission_date), 
  COUNT(*) 
FROM 
  admissions 
GROUP BY 
  DAY(admission_date) 
ORDER BY 
  COUNT(*) DESC;
```
17.Show all columns for patient_id 542's most recent admission_date.
```SQL
SELECT
  * 
FROM
  admissions 
WHERE 
  patient_id = 542 
  AND admission_date =(
    SELECT 
      MAX(admission_date) 
    FROM 
      admissions 
    WHERE 
      patient_id = 542
  );
```

```SQL
SELECT 
  * 
FROM 
  admissions 
GROUP BY 
  patient_id 
HAVING 
  patient_id = 542 
  AND MAX(admission_date); 
```
18. Show patient_id, attending_doctor_id, and diagnosis for admissions that match one of the two criteria: patient_id is an odd number and attending_doctor_id is either 1, 5, or 19. attending_doctor_id contains a 2 and the length of patient_id is 3 characters.
```SQL
SELECT 
  patient_id, 
  attending_doctor_id, 
  diagnosis 
FROM 
  admissions 
WHERE 
  (
    patient_id % 2 <> 0 
    AND attending_doctor_id IN (1, 5, 19)
  ) 
  OR (
    attending_doctor_id LIKE '%2%' 
    AND LEN(patient_id)= 3
  );
```
19. Show first_name, last_name, and the total number of admissions attended for each doctor. Every admission has been attended by a doctor.
 ```SQL
SELECT
  d.first_name, 
  d.last_name, 
  COUNT(a.admission_date) 
FROM 
  doctors d 
  JOIN admissions a ON a.attending_doctor_id = d.doctor_id 
GROUP BY 
  d.doctor_id;
```
```SQL
 SELECT 
  first_name, 
  last_name, 
  COUNT(*) 
FROM 
  doctors p, 
  admissions a 
WHERE 
  a.attending_doctor_id = p.doctor_id 
GROUP BY 
  p.doctor_id;
```
20. For each doctor, display their id, full name, and the first and last admission date they attended.
```SQL
SELECT 
  d.doctor_id, 
  CONCAT(d.first_name, " ", d.last_name), 
  MIN(a.admission_date), 
  MAX(a.admission_date) 
FROM 
  doctors d 
  JOIN admissions a ON a.attending_doctor_id = d.doctor_id 
GROUP BY 
  d.doctor_id;
```
21. Display the total amount of patients for each province. Order by descending.
```SQL
 SELECT
	pn.province_name, 
	COUNT(patient_id) AS PatientCount 
FROM 
	province_names pn 
	JOIN patients p ON p.province_id = pn.province_id 
GROUP BY 
	pn.province_id 
ORDER BY 
	PatientCount DESC;
```
```SQL
 SELECT 
	province_name, 
	COUNT(*) AS patient_count 
FROM 
	patients pa 
	JOIN province_names pr ON pr.province_id = pa.province_id 
GROUP BY 
	pr.province_id 
ORDER BY 
	patient_count DESC;
```
22. For every admission, display the patient's full name, their admission diagnosis, and their doctor's full name who diagnosed their problem.
```SQL
SELECT 
  CONCAT(p.first_name, " ", p.last_name) AS patient_name, 
  a.diagnosis, 
  CONCAT(d.first_name, " ", d.last_name) AS doctor_name 
FROM 
  patients p, 
  admissions a, 
  doctors d 
WHERE 
  p.patient_id = a.patient_id 
  AND a.attending_doctor_id = d.doctor_id;
```
```sql
 SELECT 
	 CONCAT(
    patients.first_name, ' ', patients.last_name
  ) AS patient_name, 
  diagnosis, 
  CONCAT(
    doctors.first_name, ' ', doctors.last_name
  ) AS doctor_name 
FROM 
  patients 
  JOIN admissions ON admissions.patient_id = patients.patient_id 
  JOIN doctors ON doctors.doctor_id = admissions.attending_doctor_id;
  ```
23. display the number of duplicate patients based on their first_name and last_name.
  ```sql
SELECT 
	first_name, 
	last_name, 
	COUNT(*) AS num_of_duplicates 
FROM 
	patients 
GROUP BY 
	first_name, 
	last_name 
HAVING 
	num_of_duplicates > 1;
```
24. Display patient's full name,height in the units feet rounded to 1 decimal,
weight in the unit pounds rounded to 0 decimals, birth_date, gender non abbreviated.
```SQL
SELECT 
  CONCAT(first_name, " ", last_name) AS fullname, 
  ROUND(height / 30.48, 1) AS height_ft, 
  ROUND(weight * 2.205, 0) AS weight_pound, 
  birth_date, 
  CASE 
	  WHEN gender = 'M' THEN 'Male' 
	  WHEN gender = 'F' THEN 'Female' 
  END AS gender_type 
FROM 
  patients;
```
25. Show all of the patients grouped into weight groups.
Show the total amount of patients in each weight group.
Order the list by the weight group decending.
```SQL
SELECT 
  COUNT(*) AS patients_in_group, 
  FLOOR(weight / 10) * 10 AS weight_group 
FROM 
  patients 
GROUP BY 
  weight_group 
ORDER BY 
  weight_group DESC;
```
26. Show patient_id, weight, height, isObese from the patients table.
 
 Obese is defined as weight(kg)/(height(m)2) >= 30. 
```SQL
SELECT 
  patient_id, 
  weight, 
  height, 
  (
    CASE WHEN weight /(
      POWER(height / 100.0, 2)
    ) >= 30 THEN 1 ELSE 0 END
  ) AS isObese 
FROM 
  patients;
```
27. Show patient_id, first_name, last_name, and attending doctor's specialty.
Show only the patients who has a diagnosis as 'Epilepsy' and the doctor's first name is 'Lisa'
```SQL
SELECT p.patient_id, p.first_name, p.last_name, d.specialty
FROM patients p,
admissions a,
doctors d
WHERE p.patient_id = a.patient_id 
AND a.attending_doctor_id = d.doctor_id
AND a.diagnosis = 'Epilepsy'
AND d.first_name = 'Lisa';
```
28. All patients who have gone through admissions, can see their medical documents on our site. Those patients are given a temporary password after their first admission. Show the patient_id and temp_password.

The password must be the following, in order:
a. patient_id
b. the numerical length of patient's last_name
c. year of patient's birth_date 
```sql
SELECT 
  p.patient_id, 
  CONCAT(
    p.patient_id, 
    LEN(p.last_name), 
    YEAR(p.birth_date)
  ) AS temp_password 
FROM 
  patients p 
  RIGHT JOIN admissions a ON p.patient_id = a.patient_id 
GROUP BY 
  p.patient_id;
```
29. Each admission costs $50 for patients without insurance, and $10 for patients with insurance. All patients with an even patient_id have insurance.

Give each patient a 'Yes' if they have insurance, and a 'No' if they don't have insurance. Add up the admission_total cost for each has_insurance group.
```SQL
SELECT 
  'Yes', 
  COUNT(*)* 10 AS cost_after_insurance 
FROM 
  admissions 
WHERE 
  patient_id % 2 = 0 
UNION
SELECT
  'No', 
  COUNT(*)* 50 AS cost_after_insurance 
FROM 
  admissions 
WHERE 
  patient_id % 2 <> 0;
  ```
  ```SQL
  SELECT 
  CASE WHEN patient_id % 2 = 0 THEN 'Yes' ELSE 'No' end AS has_insurance, 
  SUM(
    CASE WHEN patient_id % 2 = 0 THEN 10 ELSE 50 END
  ) AS cost_after_insurance 
FROM 
  admissions 
GROUP BY 
  has_insurance;
```
30. Show the provinces that has more patients identified as 'M' than 'F'. Must only show full province_name
```SQL
SELECT 
  province_name 
FROM 
  province_names pn 
  JOIN patients p ON pn.province_id = p.province_id 
GROUP BY 
  pn.province_name 
HAVING 
  SUM(
    CASE WHEN p.gender = 'M' THEN 1 ELSE 0 END
  ) > SUM(
    CASE WHEN p.gender = 'F' THEN 1 ELSE 0 END
  ); 
```
31. We are looking for a specific patient. Pull all columns for the patient who matches the following criteria:
- First_name contains an 'r' after the first two letters.
- Identifies their gender as 'F'
- Born in February, May, or December
- Their weight would be between 60kg and 80kg
- Their patient_id is an odd number
- They are from the city 'Kingston'
```SQL
SELECT 
  * 
FROM 
  patients 
WHERE 
  first_name LIKE '__r%' 
  AND gender = 'F' 
  AND month(birth_date) IN (2, 5, 12) 
  AND weight BETWEEN 60 
  AND 80 
  AND patient_id % 2 <> 0 
  AND city = 'Kingston';
```
32. For each day display the total amount of admissions on that day. Display the amount changed from the previous date.
```SQL
SELECT 
  admission_date, 
  COUNT(*), 
  COUNT(*)- LAG(
    COUNT(*)
  ) OVER (
    ORDER BY 
      admission_date
  ) AS admission_count_change 
FROM 
  admissions 
GROUP BY 
  admission_date
```
33. Sort the province names in ascending order in such a way that the province 'Ontario' is always on top.
```SQL
SELECT
  province_name 
FROM
  province_names 
ORDER BY 
  CASE WHEN province_name = 'Ontario' THEN 1 ELSE 2 END;
```
### SQL Practice (Northwind.db)

1. Show the category_name and description from the categories table sorted by category_name.
```sql
SELECT
  category_name, 
  description 
FROM 
  categories 
ORDER BY
  category_name;
```
2. Show all the contact_name, address, city of all customers which are not from 'Germany', 'Mexico', 'Spain'
```SQL
SELECT
  contact_name, 
  address, 
  city 
FROM 
  customers 
WHERE 
  country NOT IN ('Germany', 'Mexico', 'Spain');
```
3. Show order_date, shipped_date, customer_id, Freight of all orders placed on 2018 Feb 26
```SQL
 SELECT 
  order_date, 
  shipped_date, 
  customer_id, 
  freight 
FROM 
  orders 
WHERE 
  order_date = '2018-02-26';
```
4. Show the employee_id, order_id, customer_id, required_date, shipped_date from all orders shipped later than the required date
```SQL
SELECT 
  employee_id, 
  order_id, 
  customer_id, 
  required_date, 
  shipped_date 
FROM 
  orders 
WHERE
  shipped_date > required_date;
```
5. Show all the even numbered Order_id from the orders table
```SQL
SELECT 
	order_id 
FROM
	orders 
WHERE 
	order_id % 2 = 0;
```
  
6. Show the city, company_name, contact_name of all customers from cities which contains the letter 'L' in the city name, sorted by contact_name
```SQL
SELECT 
  city, 
  company_name, 
  contact_name 
FROM 
  customers 
WHERE 
  city LIKE '%L%' 
ORDER BY 
  contact_name;
```
7. Show the company_name, contact_name, fax number of all customers that has a fax number. (not null)
```SQL
SELECT 
	company_name, 
	contact_name, 
	fax 
FROM 
	customers 
WHERE 
	fax IS NOT NULL;
```
8. Show the first_name, last_name of the most recently hired employee.
```SQL
SELECT 
	first_name, 
	last_name, 
	hire_date 
FROM 
	employees 
ORDER BY 
	hire_date DESC 
LIMIT 1;
```
9. Show the average unit price rounded to 2 decimal places, the total units in stock, total discontinued products from the products table.
  ```SQL
SELECT 
	ROUND(AVG(unit_price),2) AS average_price, 
	SUM(units_in_stock) AS total_stock, 
	SUM(discontinued = 1) AS total_discontinued 
FROM
  products;
```
10. Show the ProductName, CompanyName, CategoryName from the products, suppliers, and categories table
```SQL
SELECT 
  p.product_name, 
  s.company_name, 
  c.category_name 
FROM
  categories c, 
  products p, 
  suppliers s 
WHERE 
  c.category_id = p.category_id 
  AND p.supplier_id = s.supplier_id;
```
11. Show the category_name and the average product unit price for each category rounded to 2 decimal places.
```SQL
SELECT 
  c.category_name, 
  ROUND(
    AVG(p.unit_price), 
    2
  ) 
FROM 
  categories c, 
  products p 
WHERE 
  p.category_id = c.category_id 
GROUP BY 
  c.category_name;
```
12. Show the city, company_name, contact_name from the customers and suppliers table merged together.
Create a column which contains 'customers' or 'suppliers' depending on the table it came from.
```SQL
SELECT 
  city, 
  company_name, 
  contact_name, 
  'Customers' 
FROM
  customers 
UNION
SELECT 
  city, 
  company_name, 
  contact_name, 
  'Suppliers' 
FROM 
  suppliers;
```
13. Show the employee's first_name and last_name, a "num_orders" column with a count of the orders taken, and a column called "Shipped" that displays "On Time" if the order shipped on time and "Late" if the order shipped late.

Order by employee last_name, then by first_name, and then descending by number of orders.
```SQL
SELECT 
  e.first_name, 
  e.last_name, 
  COUNT(o.order_id) as num_orders, 
  CASE WHEN o.shipped_date < o.required_date THEN 'On Time' ELSE 'Late' END AS shipped 
FROM
  employees e, 
  orders o 
WHERE
  e.employee_id = o.employee_id 
GROUP BY 
  shipped, 
  e.first_name, 
  e.last_name 
ORDER BY
  e.last_name, 
  e.first_name, 
  num_orders DESC;
```
