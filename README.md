# SQL Lab: SQL for Security Analysts

## Description
In this lab I will demonstrate how a security analyst working to secure a large organization’s system would use SQL queries with filters inside the organization’s database to identify potential security issues the organization is facing in regards to failed login attempts taken on their system after normal working hours, on specific dates, or outside a specific region.

I will also demonstrate how the analyst can use SQL to identify updates needed on employee computers by running operators that allow for filtering through the organization’s database in a much more efficient fashion across numerous departments.

As you work through this lab with me, it is important to know where the data is coming from. For this lab we will be examining the organization’s data in their employees and log_in_attempts tables. We will need to use SQL filters to retrieve records from different datasets and investigate the potential security issues, then note our findings and summarize our work.

## Bonus Material - Blog Post

Follow the link to read more on the strategy and various tools involved in executing SQL commands. There are also 5 helpful **SQL** searh queries given along with an explanation of how to use them and sample code to correspond:

[SQL Lab for Security Analysts: A Walkthrough](https://hispanictitanic.medium.com/sql-lab-for-security-analysts-63c59720b777)

## Table of Contents

   * [Table Formats](#Table-Formats)
   * [log_in_attempts](#log_in_attempts)
   * [Resource Links](#Resource-Links)
   * [Overview](#Overview)
   * [Continue Set Up: Download Windows 10 ISO file](#Download-Windows-10-ISO-file)
   * [Part 1: Download and Install Nessus Essentials](#Part-1-Download-and-Install-Nessus-Essentials)
   * [Part 2: Initialize Nessus Installation](#Part-2-Initialize-Nessus-Installation)
   * [Part 3: Setup Target Virtual Machine](#Part-3-Setup-Target-Virtual-Machine)
   * [Part 4: Configure Target Virtual Machine Firewall](#Part-4-Configure-Target-Virtual-Machine-Firewall)
   * [Part 5: Run Basic Network Scan (Non-Credentialed)](#Part-5-Run-Basic-Network-Scan-Non-Credentialed)
   * [Part 6: Re-Configure Target Virtual Machine](#Part-6-Re-Configure-Target-Virtual-Machine)
   * [Part 7: Run Second Scan (Credentialed)](#Part-7-Run-Second-Scan-Credentialed)
   * [Part 8: Run Third Scan (Deprecated Software - Firefox)](#Part-8-Run-Third-Scan-Deprecated-Software-Firefox)
   * [Part 9: Remediation through Updates and Patching](#Part-9-Remediation-through-Updates-and-Patching)
   * [Part 10: Run Final Scan](#Part-10-Run-Final-Scan)
   * [Results and Takeaways](#Results-and-Takeaways)

### Table Formats
The organization database contains the following two tables:

* ```log_in_attempts```
* ```employees```

Here is a breakdown of each table and the columns they contain:

### log_in_attempts

The log_in_attempts table has the following columns:

* ```event_id```: The identification number assigned to each login event
* ```username```: The username of the employee
* ```login_date```: The date the login attempt was recorded
* ```login_time```: The time the login attempt was recorded
* ```country```: The country where the login attempt occurred
*```ip_address```: The IP address of that employee’s machine
* ```success```: The success of the login attempt; FALSE indicates a failed attempt

In the MariaDB shell, these columns are returned as:
![image](https://github.com/resii-tech/SQL-Lab/assets/129999089/eb80e493-5648-4553-adb3-9efa951162f5)

employees
The employees table has the following columns:

employee_id: The identification number assigned to each employee
device_id: The identification number assigned to each device used by the employee
username: The username of the employee
department: The department the employee is in
office: The office the employee is located in
In the MariaDB shell, these columns are returned as:

employees columns as listed in MariaDB shell
employees columns as listed in MariaDB shell
Failed Login Attempts — After Hours
The security analyst discovers a potential security incident that occurred after business hours (after 18:00). All after hours login attempts that fail need to be investigated. In order to investigate we will need to query the log_in_attempts table and review after hours login activity.

Using what we know, we identify two criteria for our search. The login attempt must have happened after 18:00 hours and the login attempt must have failed .

Screenshot


After hours SQL command
Command

SELECT * FROM log_in_attempts WHERE login_time > '18:00' AND success = 0;
Breakdown

SELECT * — * is a wildcard which indicates you will select all
FROM log_in_atttempts — specifies which dataset we will be pulling from
WHERE login_time > ‘18:00’ — using WHERE clause, specifying first condition as time of login to be greater than 18:00
AND success = 0; — using AND operator to create a multi-condition query that specifies the second condition, which pulls only unsuccessful attempts. FALSE = 0 and TRUE = 1.

Query in three parts and the output it gives
Failed Login Attempts — Specific Dates
The security analyst identified a suspicious event that occurred on May 9, 2022. Any login attempts that happened on that date or on the day before need to be investigated. In order to investigate we will need to query the log_in_attempts table and review all attempts made on 5.8.22 or 5.9.22 (the date of the login attempt is found in the login_date column).

Screenshot


Specific dates SQL command
Command

SELECT * FROM log_in_attempts WHERE login_date = '2022–05–08' OR login_date = '2022–05–09';
Breakdown

SELECT * — * is a wildcard which indicates you will select all
FROM log_in_atttempts — specifies which dataset we will be pulling from
WHERE — clause used with OR operator for multi condition query
login_date = ‘2022–05–08’ — first condition as date of login to be May 8, 2022
OR login_date = ‘2022–05–09’; — second condition as date of login to be May 9, 2022

Query in three parts and the output it gives
Failed Login Attempts — Outside of Mexico
The security analyst has determined that the suspicious activity did not originate in Mexico and needs to find out what other regions the login attempts came from. In order to investigate we will need to query the log_in_attempts table and review all attempts made outside of Mexico (the country column contains values of both MEX and MEXICO, and you need to use the LIKE keyword with % to make sure your query reflects this)

Screenshot


Region specific SQL command
Command

SELECT * FROM log_in_attempts WHERE NOT country LIKE 'MEX%';
Breakdown

SELECT * — * is a wildcard which indicates you will select all
FROM log_in_atttempts — specifies which dataset we will be pulling from
WHERE NOT — clause (WHERE) used with filter (NOT) for countries other than Mexico
country — column we are pulling from
LIKE — Operator used with WHERE to search for a pattern in a column
‘MEX%’; — pattern using the percentage sign (%) which represents any number of unspecified characters when used with LIKE

Query in three parts and the output it gives
Employee Updates — Marketing Department
The security analyst wants to perform security updates on specific employee machines in the Marketing department located in the East Building. We will need to query the employees table using filters in SQL to create a filter that identifies all employees in the Marketing department for all offices in the East building.

We will need to pull from two columns — department column and office column. The office column lists examples of buildings like this — East-170, East-320, and North-434. We will need to use the LIKE keyword with % to filter for the East building.

Screenshot


Employee updates — LIKE and % command
Command

SELECT * FROM employees WHERE department = 'Marketing' AND office LIKE 'East%';
Breakdown

SELECT * — * is a wildcard which indicates you will select all
FROM employees — specifies which dataset we will be pulling from
WHERE department = — clause (WHERE) used with AND to filter for employees who meet both criteria (marketing and east building) from department column
‘Marketing’ AND- first condition, specific department with operator AND
office LIKE ‘EAST%’;- second condition, specific department (office) pattern matching filters (LIKE, EAST%) used together to identify employees in the East building

Query in three parts and the output it gives
Employee Updates — Sales and Finance Departments
The security analyst needs to perform a different security update on machines for employees in the Sales and Finance departments. We will use filters in SQL to create a query that identifies all employees in the Sales or Finance departments. (The department of the employee is found in the department column, which contains values that include Sales and Finance.)

Screenshot


Employee updates — OR operator command
Command

SELECT * FROM employees WHERE department = 'Finance' OR department = 'Sales';
Breakdown

SELECT * — * is a wildcard which indicates you will select all
FROM employees — specifies which dataset we will be pulling from
WHERE — clause (WHERE) used with OR to filter for employees who meet either criteria (sales or finance) from department column
department = ‘Finance’ first condition, specific department (finance)
OR department = ‘Sales’;- OR operator and second condition, specific department (sales)

Query in three parts and the output it gives
Employee Updates — Not the Info Tech Department
The security analyst needs to make one more update to employee machines. The employees who are in the Information Technology department already had this update, but employees in all other departments need it.

We will use filters in SQL to create a query which identifies all employees not in the IT department. (The department of the employee is found in the department column, which contains values that include Information Technology.)

Screenshot


Employee updates — NOT command
Command

SELECT * FROM employees WHERE NOT department = 'Information Technology';
Breakdown

SELECT * — * is a wildcard which indicates you will select all
FROM employees — specifies which dataset we will be pulling from
WHERE NOT — clause (WHERE) used with NOT to filter for employees who are not in a specific department (information technology)
department = ‘Information Technology’; specifies department in query being used to be Information Technology

Query in three parts and the output it gives
Summary
In this lab we were able to demonstrate how a security analyst would use SQL filters and operators to form queries that pulled from two tables in order to access records from different datasets and investigate potential security risks or update employee computers.

The two tables used were log_in_attempts and employees. The operators used to filter for specific information were AND, OR and NOT. The filters used to form patterns were LIKE and the % wildcard.

SQL can be used for precise data extraction making it a powerful cybersecurity tool. Data analysis plays a vital role in adding context to logs and databases full of information that need to be parsed and interpreted. This lab demonstrated the relevance and versatility of SQL.



# SQL Lab


## <span style="text-decoration:underline;">Project Description</span>

In this lab I will demonstrate how a security analyst working to secure a large organization’s system would use SQL queries with filters inside the organization’s database to identify potential security issues the organization is facing in regards to failed login attempts taken on their system after normal working hours, on specific dates, or outside a specific region. 

I will also demonstrate how the analyst can use SQL to identify updates needed on employee computers by running operators that allow for filtering through the organization’s database in a much more efficient fashion across numerous departments. 

As you work through this lab with me, it is important to know where the data is coming from. For this lab we will be examining the organization’s data in their **employees** and **log_in_attempts** tables. We will need to use SQL filters to retrieve records from different datasets and investigate the potential security issues, then note our findings and summarize our work.


---

<span style="text-decoration:underline;">Table Formats</span>

The **organization **database contains the following two tables: 



* **log_in_attempts**
* **employees**

Here is a breakdown of each table and the columns they contain:


## **log_in_attempts**

The **log_in_attempts **table has the following columns:



* **event_id**: The identification number assigned to each login event
* **username**: The username of the employee
* **login_date**: The date the login attempt was recorded
* **login_time**: The time the login attempt was recorded
* **country**: The country where the login attempt occurred
* **ip_address**: The IP address of that employee’s machine
* **success**: The success of the login attempt; **FALSE **indicates a failed attempt

In the MariaDB shell, these columns are returned as:



<p id="gdcalert1" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image1.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert2">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/image1.png "image_tooltip")



## **employees**

The **employees **table has the following columns:



* **employee_id**: The identification number assigned to each employee
* **device_id**: The identification number assigned to each device used by the employee
* **username**: The username of the employee
* **department**: The department the employee is in
* **office**: The office the employee is located in

In the MariaDB shell, these columns are returned as:



<p id="gdcalert2" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image2.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert3">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/image2.png "image_tooltip")



---

<span style="text-decoration:underline;">Failed Login Attempts - After Hours</span>

The security analyst discovers a potential security incident that occurred after business hours (after 18:00). All after hours login attempts that fail need to be investigated. In order to investigate we will need to query the **log_in_attempts** table and review after hours login activity. 

Using what we know, we identify two criteria for our search. The login attempt must have happened after 18:00 hours and the login attempt must have failed . The following screenshot will show the query used:



<p id="gdcalert3" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image3.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert4">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/image3.png "image_tooltip")


Command: ** <code>>SELECT * FROM log_in_attempts WHERE login_time > '18:00' AND success = 0;</code></strong>

For our initial query I will walk through the different components and what they mean.

SELECT * (select all data) FROM log_in_attempts (from log_in_attempts table) WHERE login_time (use the WHERE clause with the AND operator) > ‘18:00’ (first condition) AND success = 0; (second condition)

Breakdown:

**<code>SELECT * </code></strong>- * is a wildcard which indicates you will select all 

**<code>FROM log_in_atttempts </code></strong>- specifies which dataset we will be pulling from

**<code>WHERE login_time > '18:00'</code></strong> - using WHERE clause, specifying first condition as time of login to be greater than 18:00

**<code>AND success = 0;</code></strong> - using AND operator to create a multi-condition query that specifies the second condition, which pulls only unsuccessful attempts. FALSE = 0 and TRUE = 1. 

Here is a screenshot of the query written in three parts and the output it gives:



<p id="gdcalert4" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image4.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert5">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/image4.png "image_tooltip")



---

<span style="text-decoration:underline;">Failed Login Attempts - Specific Dates</span>

The security analyst identified a suspicious event that occurred on May 9, 2022. Any login attempts that happened on that date or on the day before need to be investigated. In order to investigate we will need to query the **log_in_attempts** table and review all attempts made on 5.8.22 or 5.9.22 (the date of the login attempt is found in the **login_date** column).

The following screenshots will show the query used:



<p id="gdcalert5" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image5.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert6">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/image5.png "image_tooltip")


Command: **<code>>SELECT * FROM log_in_attempts WHERE login_date = '2022-05-08' OR login_date = '2022-05-09';</code></strong>

Breakdown:

**<code>SELECT * </code></strong>- * is a wildcard which indicates you will select all 

**<code>FROM log_in_atttempts </code></strong>- specifies which dataset we will be pulling from

**<code>WHERE </code></strong>- clause used with OR operator for multi condition query

**<code>login_date = '2022-05-08'</code></strong> - first condition as date of login to be May 8, 2022

**<code>OR login_date = '2022-05-09';</code></strong> - second condition as date of login to be May 9, 2022

Here is a screenshot of the query written in three parts and the output it gives:



<p id="gdcalert6" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image6.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert7">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/image6.png "image_tooltip")



---

<span style="text-decoration:underline;">Failed Login Attempts - Outside of Mexico</span>

The security analyst has determined that the suspicious activity did not originate in Mexico and needs to find out what other regions the login attempts came from. In order to investigate we will need to query the **log_in_attempts** table and review all attempts made outside of Mexico (the **country** column contains values of both **MEX** and **MEXICO**, and you need to use the **LIKE** keyword with **%** to make sure your query reflects this)

The following screenshots will show the query used:



<p id="gdcalert7" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image7.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert8">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/image7.png "image_tooltip")


Command:**<code> > SELECT * FROM log_in_attempts WHERE NOT country LIKE 'MEX%';</code></strong>

Breakdown: 

**<code>SELECT * </code></strong>- * is a wildcard which indicates you will select all 

**<code>FROM log_in_atttempts </code></strong>- specifies which dataset we will be pulling from

**<code>WHERE NOT </code></strong>- clause (WHERE) used with filter (NOT) for countries other than Mexico

**<code>country </code></strong>- column we are pulling from

**<code>LIKE - </code></strong>Operator used with WHERE to search for a pattern in a column

**<code>'MEX%';</code></strong> - pattern using the percentage sign (%) which represents any number of unspecified characters when used with LIKE

Here is a screenshot of the query written in three parts and the output it gives:



<p id="gdcalert8" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image8.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert9">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/image8.png "image_tooltip")



---

<span style="text-decoration:underline;">Employee Updates - Marketing Department</span>

The security analyst wants to perform security updates on specific employee machines in the Marketing department located in the East Building. We will need to query the **employees** table using filters in SQL to create a filter that identifies all employees in the Marketing department for all offices in the **East **building. 

We will need to pull from two columns - **department** column and **office **column. The office column lists examples of buildings like this - **East-170**, **East-320**, and **North-434**. We will need to use the **LIKE** keyword with **%** to filter for the East building.

The following screenshots will show the query used:



<p id="gdcalert9" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image9.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert10">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/image9.png "image_tooltip")


Command: **<code>>SELECT * FROM employees WHERE department = 'Marketing' AND office LIKE 'East%';</code></strong>

Breakdown:

**<code>SELECT * </code></strong>- * is a wildcard which indicates you will select all 

**<code>FROM employees </code></strong>- specifies which dataset we will be pulling from

**<code>WHERE department = </code></strong>- clause (WHERE) used with AND to filter for employees who meet both criteria (marketing and east building) from department column

**<code>'Marketing' AND-</code></strong> first condition, specific department with operator AND

**<code>office LIKE 'EAST%';-</code></strong> second condition, specific department (office) pattern matching filters (LIKE, EAST%) used together to identify employees in the East building

Here is a screenshot of the query written in three parts and the output it gives:



<p id="gdcalert10" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image10.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert11">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/image10.png "image_tooltip")



---


## <span style="text-decoration:underline;">Employee Updates - Sales and Finance Departments</span>

The security analyst needs to perform a different security update on machines for employees in the Sales and Finance departments. We will use filters in SQL to create a query that identifies all employees in the Sales or Finance departments. (The department of the employee is found in the **department** column, which contains values that include **Sales** and **Finance**.)

The following screenshots will show the query used:



<p id="gdcalert11" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image11.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert12">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/image11.png "image_tooltip")


Command:** <code>>SELECT * FROM employees WHERE department = 'Finance' OR department = 'Sales';</code></strong>

Breakdown:

**<code>SELECT * </code></strong>- * is a wildcard which indicates you will select all 

**<code>FROM employees </code></strong>- specifies which dataset we will be pulling from

**<code>WHERE </code></strong>- clause (WHERE) used with OR to filter for employees who meet either criteria (sales or finance) from department column

**<code>department = 'Finance'</code></strong> first condition, specific department (finance)

**<code>OR department = 'Sales';-</code></strong> OR operator and second condition, specific department (sales) 

Here is a screenshot of the query written in three parts and the output it gives:



<p id="gdcalert12" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image12.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert13">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/image12.png "image_tooltip")



---


## <span style="text-decoration:underline;">Employee Updates - Not the Information Technology Department</span>

The security analyst needs to make one more update to employee machines. The employees who are in the Information Technology department already had this update, but employees in all other departments need it. 

We will use filters in SQL to create a query which identifies all employees not in the IT department. (The department of the employee is found in the **department** column, which contains values that include **Information Technology**.)

The following screenshots will show the query used:



<p id="gdcalert13" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image13.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert14">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/image13.png "image_tooltip")


Command: **<code>>SELECT * FROM employees WHERE NOT department = 'Information Technology';</code></strong>

Breakdown:

**<code>SELECT * </code></strong>- * is a wildcard which indicates you will select all 

**<code>FROM employees </code></strong>- specifies which dataset we will be pulling from

**<code>WHERE NOT </code></strong>- clause (WHERE) used with NOT to filter for employees who are not in a specific department (information technology)

**<code>department = 'Information Technology';</code></strong> specifies department in query being used to be Information Technology

Here is a screenshot of the query written in three parts and the output it gives:



<p id="gdcalert14" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image14.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert15">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/image14.png "image_tooltip")



---


## <span style="text-decoration:underline;">Summary</span>

In this lab we were able to demonstrate how a security analyst would use SQL filters and operators to form queries that pulled from two tables in order to access records from different datasets and investigate potential security risks or update employee computers. 

The two tables used were** log_in_attempts** and **employees**. The operators used to filter for specific information were **AND**, **OR **and **NOT**. The filters used to form patterns were **LIKE **and the **% **wildcard. 

There were some fundamental usages of the SQL language that weren’t explained but that could be inferred or researched in order to gain a basic working knowledge of how to use SQL as a security analyst. This lab focused on specific use cases taken from a security analyst’s daily responsibilities to showcase practical use cases. 
