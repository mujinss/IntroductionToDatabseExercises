# sql social network modification exercises

Students at your hometown high school have decided to organize their social network using databases. So far, they have collected information about sixteen students in four grades, 9-12. Here's the schema: 

Highschooler `( ID, name, grade )` 
English: There is a high school student with unique ID and a given first name in a certain grade. 

Friend `( ID1, ID2 )` 
English: The student with ID1 is friends with the student with ID2. 
Friendship is mutual, so if `(123, 456)` is in the Friend table, so is `(456, 123)`. 

Likes `( ID1, ID2 )` 
English: The student with ID1 likes the student with ID2. 
Liking someone is not necessarily mutual, so if `(123, 456)`
is in the Likes table, there is no guarantee that `(456, 123)` is also present. 

Your modifications will run over a small data set conforming to the schema.
View the [database] (https://class.stanford.edu/c4x/Engineering/db/asset/socialdata.html)

* It's time for the seniors to graduate. Remove all 12th graders from Highschooler.
```
delete from Highschooler
where grade = 12
```

* If two students A and B are friends, and A likes B but not vice-versa, remove the Likes tuple. 
```
delete from Likes
where ID1 in (select L.ID1
              from Friend as F, Likes as L
              where F.ID1 = L.ID1 and F.ID2 = L.ID2 
                     and F.ID2 not in (select ID1 from Likes where ID2 = L.ID1))
  and ID2 in (select L.ID2
              from Friend as F, Likes as L
              where F.ID1 = L.ID1 and F.ID2 = L.ID2 
                     and F.ID2 not in (select ID1 from Likes where ID2 = L.ID1))
```

* For all cases where A is friends with B, and B is friends with C, add a new friendship for the pair A and C. 
Do not add duplicate friendships, friendships that already exist, or friendships with oneself. 
```
insert into Friend
    select distinct F1.ID1, F2.ID2
    from Friend as F1, Friend as F2
    where F1.ID2 = F2.ID1 and F1.ID1 <> F2.ID2
    and F2.ID2 NOT IN (select ID2 
                       from Friend
                       where ID1 = F1.ID1)
```
