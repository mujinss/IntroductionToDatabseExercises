# sql movie-rating view modification exercises
## core set

You've started a new movie-rating website, and you've been collecting data on reviewers' ratings of various movies. 
Here's the schema: 

Movie `( mID, title, year, director )` 
English: There is a movie with ID number mID, a title, a release year, and a director. 

Reviewer `( rID, name )` 
English: The reviewer with ID number rID has a certain name. 

Rating `( rID, mID, stars, ratingDate )` 
English: The reviewer rID gave the movie mID a number of stars rating `(1-5)` on a certain ratingDate. 

In addition to the base tables, you've created three views: 
View LateRating contains movie ratings after January 20, 2011. 
The view contains the movie ID, movie title, number of stars, and rating date. 
create view LateRating as 
```
  select distinct R.mID, title, stars, ratingDate 
  from Rating R, Movie M 
  where R.mID = M.mID 
  and ratingDate > '2011-01-20' 
```

View HighlyRated contains movies with at least one rating above 3 stars. The view contains the movie ID and movie title. 
create view HighlyRated as 
```
  select mID, title 
  from Movie 
  where mID in (select mID from Rating where stars > 3) 
```

View NoRating contains movies with no ratings in the database. The view contains the movie ID and movie title. 
create view NoRating as
```
  select mID, title 
  from Movie 
  where mID not in (select mID from Rating) 
```
Your exercises will run over a small data set conforming to the schema, with the views predefined. View the database.

* Write an instead-of trigger that enables updates to the title attribute of view LateRating. 
Policy: Updates to attribute title in LateRating should update Movie.title for the corresponding movie. 
`(You may assume attribute mID is a key for table Movie.)` 
Make sure the mID attribute of view LateRating has not also been updated 
-- if it has been updated, don't make any changes. Don't worry about updates to stars or ratingDate.
Remember you need to use an instead-of trigger for view modifications as supported by SQLite.
```
create trigger LateUpdate
instead of update of title on LateRating
begin 
    update Movie
    set title = new.title
    where mID = new.mID;                               
end;
```

* Write an instead-of trigger that enables updates to the stars attribute of view LateRating. 
Policy: Updates to attribute stars in LateRating should update Rating.stars for the corresponding movie rating. 
`(You may assume attributes `[mID,ratingDate]` together are a key for table Rating.)` 
Make sure the mID and ratingDate attributes of view LateRating have not also been updated -- 
if either one has been updated, don't make any changes. Don't worry about updates to title.
```
create trigger updateStars
instead of update of stars on LateRating
begin
    update Rating
    set stars = new.stars
    where mID = new.mID and ratingDate = new.ratingDate;
end;    
```

* Write an instead-of trigger that enables updates to the mID attribute of view LateRating. 
Policy: Updates to attribute mID in LateRating should update Movie.mID and Rating.mID for the corresponding movie. 
Update all Rating tuples with the old mID, not just the ones contributing to the view. 
Don't worry about updates to title, stars, or ratingDate.
```
create trigger UpdatemID
instead of update of mID on LateRating
begin
    update Movie
    set mID = new.mID
    where mID = old.mID;
    
    
    update Rating
    set mID = new.mID
    where mID = old.mID;
end;    
```

* Write an instead-of trigger that enables deletions from view HighlyRated. 
Policy: Deletions from view HighlyRated should delete all ratings for the corresponding movie that have stars > 3.
```
create trigger deleteRating
instead of delete on HighlyRated
begin
    delete from Rating
    where mID = old.mID and stars > 3;
end; 
```

* Write an instead-of trigger that enables deletions from view HighlyRated. 
Policy: Deletions from view HighlyRated should update all ratings for the corresponding movie that have stars > 3 
so they have stars = 3.
```
create trigger updateRating
instead of delete on HighlyRated
begin
    update Rating
    set stars = 3
    where stars > 3 and mID = old.mID;
end;    
```
## Challenge Level
* Write a single instead-of trigger that enables simultaneous updates to attributes mID, title, 
and/or stars in view LateRating. Combine the view-update policies of the questions 1-3 in the core set, 
with the exception that mID may now be updated. 
Make sure the ratingDate attribute of view LateRating has not also been updated -- 
if it has been updated, don't make any changes.
```
create trigger UpdatemID
instead of update on LateRating
for each row
when old.ratingDate = new.ratingDate
begin
    update Movie
    set mID = new.mID
    where mID = old.mID;
    
    update Movie
    set title = new.title
    where title = old.title;
    
    update Rating
    set stars = new.stars 
    where stars = old.stars and mID = old.mID and ratingDate = old.ratingDate;
    
    update Rating
    set mID = new.mID
    where mID = old.mID;

end;
```

* Write an instead-of trigger that enables insertions into view HighlyRated. 
Policy: An insertion should be accepted only when the `(mID,title)` pair already exists in the Movie table. 
`(Otherwise, do nothing.)` Insertions into view HighlyRated should add a new rating 
for the inserted movie with rID = 201, stars = 5, and NULL ratingDate.
```
create trigger InsertNewRating
instead of insert on HighlyRated
when exists (select * from Movie where mID = new.mID and title = new.title)
begin
    insert into Rating
    values(201, new.mID, 5, NULL);
    
end;    
```

* Write an instead-of trigger that enables insertions into view NoRating. 
Policy: An insertion should be accepted only when the `(mID,title)` pair already exists in the Movie table. 
`(Otherwise, do nothing.)` Insertions into view NoRating should delete all ratings for the corresponding movie.
```
create trigger deleteRating
instead of insert on NoRating
when exists (select * from Movie where mID = new.mID and title = new.title)
begin
    delete from Rating
    where mID = new.mID;
end;  
```


