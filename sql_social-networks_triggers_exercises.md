# sql social-networks triggers exercises
## core set

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

Your triggers will run over a small data set conforming to the schema. View the [database] (https://class.stanford.edu/c4x/Engineering/db/asset/socialdata.html).
Your triggers are created in SQLite, so you must conform to the trigger constructs supported by SQLite.

* Write a trigger that makes new students named 'Friendly' automatically like everyone else in their grade. 
That is, after the trigger runs, we should have `('Friendly', A)` in the Likes table for every other Highschooler A in the same grade as 'Friendly'.
```
create trigger T1
after insert on Highschooler
for each row
when New.name = 'Friendly'
begin
insert into Likes 
select New.ID, H.ID from Highschooler as H where H.grade = New.grade and H.ID <> New.ID;
end;  
```

* Write one or more triggers to manage the grade attribute of new Highschoolers. 
If the inserted tuple has a value less than 9 or greater than 12, change the value to NULL. 
On the other hand, if the inserted tuple has a null value for grade, change it to 9. 
To create more than one trigger, separate the triggers with a vertical bar `(|)`.
```
create trigger T1
after insert on Highschooler
for each row
when New.grade < 9 or New.grade > 12
begin
    update Highschooler
    set grade = Null
    where ID = New.ID; 
end;    
|
create trigger T2
after insert on Highschooler
for each row
when New.grade is Null
begin
    update Highschooler
    set grade = 9
    where ID = New.ID;
end;    
```

* Write a trigger that automatically deletes students when they graduate, i.e., when their grade is updated to exceed 12. 
```
create trigger T1
after update on Highschooler
for each row
begin
    delete from Highschooler
    where grade > 12;
end;
```

## Challenge Level
* Write one or more triggers to maintain symmetry in friend relationships. 
Specifically, if `(A,B)` is deleted from Friend, then `(B,A)` should be deleted too. 
If `(A,B)` is inserted into Friend then `(B,A)` should be inserted too. Don't worry about updates to the Friend table. 
```
create trigger T1
after delete on Friend
for each row
begin
delete from Friend
where ID1 = Old.ID2 and ID2 = Old.ID1;
end;
|
create trigger T2
after insert on Friend
for each row
begin
insert into Friend values(New.ID2, New.ID1);
end;
```

* Write a trigger that automatically deletes students when they graduate, i.e., when their grade is updated to exceed 12. 
In addition, write a trigger so when a student is moved ahead one grade, then so are all of his or her friends. 
```
create trigger T1
after update of grade on Highschooler
for each row
when New.grade = Old.grade + 1
begin 
update Highschooler
set grade = grade +1
where ID in (select ID2 from Friend where ID1 = New.ID);
end;
|
create trigger T2
after update of grade on Highschooler
for each row 
when New.grade > 12
begin
delete from Highschooler
where ID = New.ID;
end;
```

* Write a trigger to enforce the following behavior: If A liked B but is updated to A liking C instead, 
and B and C were friends, make B and C no longer friends. Don't forget to delete the friendship in both directions, 
and make sure the trigger only runs when the "liked" `(ID2)` person is changed but the "liking" `(ID1)` person is not changed.
```
create trigger T1
after update on Likes
when exists (select ID1, ID2 from Friend 
             where ID1 = Old.ID2 and ID2 = New.ID2 and Old.ID1 = New.ID1)
begin 
delete from Friend
where ID1 = Old.ID2 and ID2 = New.ID2;
delete from Friend
where ID1 = New.ID2 and ID2 = Old.ID2;
end;
```



