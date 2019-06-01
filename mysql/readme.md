## Basics

```sql
# -- Destroying the set (table)

DROP TABLE student;
```

```sql
# -- Creating a set (table) and it's schema

CREATE TABLE student (
    student_id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    major VARCHAR(30) DEFAULT "undecided"
);
```

```sql
# -- Showing the set (table) schema

DESCRIBE student;
```

```sql
# -- Adding a new attribute (column) to the set (table)

ALTER TABLE student ADD gpa DECIMAL(3,2);
```

```sql
# -- Removing an attribute (column)  from the set (table)

ALTER TABLE student DROP COLUMN gpa;
```

## Inserting and changing data

```sql
# -- Inserting data into the set (table)

INSERT INTO student(name, major) VALUE("Peter", "Computer Science");
INSERT INTO student(name, major) VALUE("Jack", "Computer Science");
INSERT INTO student(name, major) VALUE("Jon", "Sociology");
```

```sql
# -- Showing all data in the student set (table)

SELECT * FROM student;
```

```sql
# -- Chaning attributes on simple condition

UPDATE student
SET major = "comp sci"
WHERE major = "Computer Science";
```

```sql
# -- Chaning attributes using OR

UPDATE student
SET major = "EVERYTHING"
WHERE major = "comp sci" OR major = "Sociology";
```

```sql
# -- Changing multiple attributes

UPDATE student
SET name = "Russel", major = "undecided"
WHERE student_id = 2;
```

```sql
# -- Changing every single tuple (row)

UPDATE student
SET major = 'undecided';
```

```sql
# -- Removing a tuple (row)

DELETE FROM student
WHERE student_id = 1;
```


```sql
# -- Removing all tuples (rows)

DELETE FROM student;
```


## Basic Queries

```sql
# -- Get values from a set

SELECT student.name, student.major
FROM student
ORDER BY name ASC
;
```

```sql
# -- Get all values and order by multiple attributes

SELECT *
FROM student
ORDER BY major, student_id ASC
;
```


```sql
# -- Get all values and limit number of resutls

SELECT *
FROM student
ORDER BY major ASC
LIMIT 1
;
```

```sql
# -- Get values with conditional statements
# -- <, >, <=, >=, =, <>, AND, OR

SELECT *
FROM student
WHERE major <> "Sociology"
;
```
