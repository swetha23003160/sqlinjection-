NAME: swetha.M

REG NO: 212223040223

# sqlinjection
Exploiting SQL Injection vulnerability

# AIM:
To exploit SQL Injection vulnerability using Multidae web application in Metasploitable2

## DESIGN STEPS:

### Step 1:

Install kali linux either in partition or virtual box or in live mode


### Step 2:

Investigate on the various categories of tools as follows:

### Step 3:

Open terminal and try execute some kali linux commands

## EXECUTION STEPS AND ITS OUTPUT:

SQL Injection is a sort of infusion assault that makes it conceivable to execute malicious SQL statements. These statements control a database server behind a web application. Assailants can utilize SQL Injection vulnerabilities to sidestep application safety efforts. They can circumvent authentication and authorization of a page or web application and recover the content of the whole SQL database. 
Identify IP address using ifconfig in Metasploitable2
# OUTPUT

<img width="825" height="411" alt="image" src="https://github.com/user-attachments/assets/92a06030-17e4-4b89-b5d0-d054ee908d41" />


Use the above ip address to access the apache webserver of Metasploitable2 from kali/parrot linux. In Kali Linux use the ip address in a web browser.
##  OUTPUT

<img width="955" height="484" alt="image" src="https://github.com/user-attachments/assets/b3c3bc22-87e0-4eb1-b197-4ffa7636799c" />


Select Multidae from the menu listed as shown above. The page is displayed as below:
##  OUTPUT

<img width="1016" height="477" alt="image" src="https://github.com/user-attachments/assets/90edd81b-222e-4ffc-9da9-e4c41f6bac56" />


Click on the menu Login/Register and register for an account
##  OUTPUT

<img width="1456" height="668" alt="image" src="https://github.com/user-attachments/assets/7e616b68-c5fc-4b9a-b0c5-c87f151ade04" />


Click on “Create Account” to display the following page:
##  OUTPUT

<img width="1462" height="605" alt="image" src="https://github.com/user-attachments/assets/fcea6774-29c2-425a-9266-60e5bf50f5e9" />


The login structure we will use in our examples is straightforward. It contains two input fields (username and password), which are both vulnerable. The back-end content creates a query to approve the username and secret key given by the client. Here is an outline of the page rationale:


($query = “SELECT * FROM users WHERE username=’$_POST[username]’ AND password=’$_POST[password]’“;).
 For the username put “ganesh” or “anything” and for the password put (anything’ or ‘1’=’1) or (admin’ or ‘1’=’1) then try to log in, and you’ll be presented with an admin login page.
From this point, all our attack vectors will be performed in the URL section of the page using the Union-Based technique.There are two different ways to discover how many columns are selected by the original query. The first is to infuse an “ORDER BY” statement indicating a column number. Given the column number specified is higher than the number of columns in the “SELECT” statement, an error will be returned.
##  OUTPUT

<img width="1161" height="580" alt="image" src="https://github.com/user-attachments/assets/72e03831-d83a-4ced-a538-70327d8efd97" />


Since we do not know the number of columns, we start at 1. To find the exact amount of columns, the number is incremented until an error related to the “ORDER BY” clause is returned. In this example, we incremented it to 6 and received an error message, so it means that the number of columns is lower than 6.

The browser url of this info page need to be modified with the url as below:
##  OUTPUT

<img width="1155" height="586" alt="image" src="https://github.com/user-attachments/assets/f6ffb134-bf3c-4bc6-b5a6-ef4440ea1d0a" />



After adding the order by 6 into the existing url , the following error statement will be obtained:
##  OUTPUT


<img width="1162" height="576" alt="image" src="https://github.com/user-attachments/assets/ecae5a98-a902-4331-a7fd-4334d34108f6" />


When we ordered by 5, it worked and displayed some information. It means there are five columns that we can work with. Following screenshot shows that the url modified to have statement added with ordered by 5 replacing 6.
# OUTPUT

<img width="1165" height="579" alt="image" src="https://github.com/user-attachments/assets/10ee043a-337a-4ff4-89e9-485f905103b8" />



 As it is having 5 columns the query worked fine and it provides the correct result
##  OUTPUT

<img width="1157" height="593" alt="image" src="https://github.com/user-attachments/assets/31baad8a-d15b-4ab9-bf11-d281ffbe1761" />



Instead of using the "order by" option, let’s use the "union select" option and provide all five columns. Ex: (union select 1,2,3,4,5).
##  OUTPUT

<img width="1355" height="714" alt="image" src="https://github.com/user-attachments/assets/db2cf6d3-4376-452d-aaa0-cd0cc0af3ea1" />


As given in the screenshot below columns 2,3,4 are usable in which we can substitute any sql commands to extract necessary information.
##  OUTPUT



<img width="961" height="487" alt="image" src="https://github.com/user-attachments/assets/e02ff7bf-770e-46d5-8a80-9dc71a5bcb5d" />



Now we will substitute some few commands like database(), user(), version() to obtain the information regarding the database name, username and version of the database.
##  OUTPUT

<img width="824" height="420" alt="image" src="https://github.com/user-attachments/assets/ba2f2da2-885e-4da2-b377-825dc5eb466c" />


The url when executed, we obtain the necessary information about the database name owasp10, username as root@localhost and version as 5.0.51a-3ubuntu5.
In MySQL, the table “information_schema.tables” contains all the metadata identified with table items. Below is listed the most useful information on this table.

Replace the query in the url with the following one:
union select 1,table_name,null,null,5 from information_schema.tables where table_schema = ‘owasp10’
##  OUTPUT


<img width="824" height="432" alt="image" src="https://github.com/user-attachments/assets/ea2b3aec-72f6-4126-8a8f-016a03e06291" />


The url once executed will  retrieve table names from the “owasp 10” database.
##Extracting sensitive data such as passwords 

When the attacker knows table names, he needs to discover what the column names are to extract data.

In MySQL, the table “information_schema.columns” gives data about columns in tables. One of the most useful columns to extract is called “column_name.”

Ex: (union select 1,colunm_name,null,null,5 from information_schema.columns where table_name = ‘accounts’).

Here we are trying to extract column names from the “accounts” table.
##  OUTPUT

<img width="823" height="410" alt="image" src="https://github.com/user-attachments/assets/3bde7117-5175-4c8e-9a55-f3ea1f435ed2" />


The column names of the accounts is displayed below for the following url:


Once we discovered all available column names, we can extract information from them by just adding those column names in our query sentence.

Ex: (union select 1,username,password,is_admin,5 from accounts).
##  OUTPUT

<img width="823" height="409" alt="image" src="https://github.com/user-attachments/assets/db43ffe2-d7f1-4249-a8b2-c53a1849cc65" />


## Reading and writing files on the web-server
We can use the “LOAD_FILE()” operator to peruse the contents of any file contained within the web-server. We will typically check for the “/etc/password” file to see if we get lucky and scoop usernames and passwords to possible use in brute force attacks later.

Ex: (union select null,load_file(‘/etc/passwd’),null,null,null).


##  OUTPUT

<img width="821" height="436" alt="image" src="https://github.com/user-attachments/assets/6a40a49f-de57-41b8-9f91-caff3a1d4797" />


## RESULT:
The SQL Injection vulnerability is successfully exploited using the Multidae web application in Metasploitable2.
