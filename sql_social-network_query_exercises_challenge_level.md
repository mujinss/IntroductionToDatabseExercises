# sql social network query exercises challenge level

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

* Find all students who do not appear in the Likes table `(as a student who likes or is liked)` 
  and return their names and grades. Sort by grade, then by name within each grade.
```
select H.name, H.grade
from Highschooler as H
where H.ID not in (select L.ID1 from Likes as L)
   and H.ID not in (select L.ID2 from Likes as L)
order by H.grade, H.name
```

* Find the difference between the number of students in the school and the number of different first names. 
```
select (n_stu - n_fname)
from (select count(*) as n_stu from Highschooler), 
     (select count(distinct name) as n_fname from Highschooler)
```

* What is the average number of friends per student? `(Your result should be just one number.)` 
```
select avg(total_friends_of_ID1)
from (select ID1, count(ID2) as total_friends_of_ID1
      from Friend
      group by ID1)
```
* Find the number of students who are either friends with Cassandra or are friends of friends of Cassandra.
  Do not count Cassandra, even though technically she is a friend of a friend. 
```
select count (distinct ID1)
from Friend
where ID2 in (select ID 
              from Highschooler 
              where name = 'Cassandra')
   or ID2 in (select F.ID1 
              from Friend as F 
              where F.ID2  = (select ID
                              from Highschooler 
                              where name = 'Cassandra')
              ) 
  and ID1 not in (select ID 
                  from Highschooler
                  where name = 'Cassandra')          
```
* Find the name and grade of the student`(s)` with the greatest number of friends. 
```
select H.name, H.grade 
from Highschooler as H
where H.ID in (select F.ID1 from Friend as F 
               group by F.ID1
               having count(F.ID2) = (select max(c_friend) 
                                      from (select count(F.ID2) as c_friend 
                                            from Friend as F
                                            group by F.ID1))
               )
```
               
               

