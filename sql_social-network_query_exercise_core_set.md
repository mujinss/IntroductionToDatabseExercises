# sql social-network query exercises core set

Students at your hometown high school have decided to organize their social network using databases. 
So far, they have collected information about sixteen students in four grades, 9-12. Here's the schema: 

Highschooler `( ID, name, grade )` 
English: There is a high school student with unique ID and a given first name in a certain grade. 

Friend `( ID1, ID2 )`
English: The student with ID1 is friends with the student with ID2. 
Friendship is mutual, so if `(123, 456)` is in the Friend table, so is `(456, 123)`. 

Likes `( ID1, ID2 )` 
English: The student with ID1 likes the student with ID2. 
Liking someone is not necessarily mutual, so if `(123, 456)` is in the Likes table, 
there is no guarantee that `(456, 123)` is also present. 

Your queries will run over a small data set conforming to the schema.
View the [database](https://class.stanford.edu/c4x/Engineering/db/asset/socialdata.html)

Important Notes:
Your queries are executed using SQLite, so you must conform to the SQL constructs supported by SQLite.
Unless a specific result ordering is asked for, you can return the result rows in any order.

* Find the names of all students who are friends with someone named Gabriel. 
```
select distinct name
from (select ID2
      from Highschooler as H, Friend as F
      where H.ID = F.ID1 and H.name = "Gabriel") as T, Highschooler as H
where T.ID2 = H.ID
```

* For every student who likes someone 2 or more grades younger than themselves, 
  return that student's name and grade, and the name and grade of the student they like. 
```
select H1.name, H1.grade, H2.name, H2.grade
from Likes as L, Highschooler as H1, Highschooler as H2
where L.ID1 = H1.ID and L.ID2 = H2.ID and H1.grade >= H2.grade + 2
```

* For every pair of students who both like each other, return the name and grade of both students. 
  Include each pair only once, with the two names in alphabetical order. 
```
select H1.name, H1.grade, H2.name, H2.grade
from (select L1.ID1, L1.ID2
      from Likes as L1 join Likes as L2 on L1.ID2 = L2.ID1
      where L1.ID1 = L2.ID2), Highschooler as H1, Highschooler as H2
where ID1 = H1.ID and ID2 = H2.ID and H1.name < H2.name
```

* Find names and grades of students who only have friends in the same grade. 
  Return the result sorted by grade, then by name within each grade. 
```
select name, grade
from Highschooler
where ID in 
(select F.ID1
 from Friend as F, Highschooler as H1, Highschooler as H2
 where F.ID1 = H1.ID and F.ID2 = H2.ID and H1.grade = H2.grade
EXCEPT 
select F.ID1
 from Friend as F, Highschooler as H1, Highschooler as H2
 where F.ID1 = H1.ID and F.ID2 = H2.ID and H2.grade <> H1.grade)
order by grade, name
```

* Find the name and grade of all students who are liked by more than one other student. 
```
select H.name, H.grade
from (select ID2 as ID 
      from Likes
      group by ID2
      having count (ID1) > 1) natural join Highschooler as H
```

