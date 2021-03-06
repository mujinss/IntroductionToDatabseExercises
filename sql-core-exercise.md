# SQL core exercise set

You've started a new movie-rating website, and you've been collecting data on reviewers' ratings of various movies. 
There's not much data yet, but you can still try out some interesting queries. Here's the schema: 

Movie `( mID, title, year, director )` 
English: There is a movie with ID number mID, a title, a release year, and a director. 

Reviewer `( rID, name )` 
English: The reviewer with ID number rID has a certain name. 

Rating `( rID, mID, stars, ratingDate )` 
English: The reviewer rID gave the movie mID a number of stars rating `(1-5)` on a certain ratingDate. 

Your queries will run over a small data set conforming to the schema. 
View the [database](https://class.stanford.edu/c4x/Engineering/db/asset/moviedata.html) 
The queries are executed using SQLite, so the answers must conform to the SQL constructs supported by SQLite.

* Find the titles of all movies directed by Steven Spielberg
```
select title
from Movie
where director = "Steven Spielberg"
```

* Find all years that have a movie that received a rating of 4 or 5, and sort them inincreasing order
```
select distinct year
from Movie as M, Rating as Ra
where M.mID = Ra.mID and stars >= 4
order by year
```

* Find the titles of all movies that have no ratings
```
select Movie.title
from Movie
where Movie.mID not in (select Rating.mID 
                        from Rating)
```

* Some reviewers didn't provide a date with their rating. Find the names off all reviewers who have ratings with a NULL value 
for the date
```
select name 
from Reviewer as Re, Rating as Ra
where Re.rID = Ra.rID and rating Date is null;
```

* Write a query to return the ratings data in a more readable format: reviewer name, movie title, stars, and ratingDate. 
Also, sort the data, first by reviewer name, then by movie title, and lastly by number of stars. 
```
select Re.name, title, stars, ratingDate
from Reviewer as Re, Movie as M, Rating as Ra
where Re.rID = Ra.rID and M.mID = Ra.mID
order by Re.name, M.title, stars
```

* For all cases where the same reviewer rated the same movie twice and gave it a higher rating the second time, 
return the reviewer's name and the title of the movie. 
```
select Re.name, M.title
from (select Ra1.rID, Ra1.mID 
      from Rating as Ra1, Rating as Ra2
      where Ra1.rID = Ra2.rID and Ra1.mID = Ra2.mID and Ra1.ratingDate < Ra2.ratingDate 
      and Ra1.stars < Ra2.stars) as Ra, Reviewer as Re, Movie as M
where Re.rID = Ra.rID and M.mID = Ra.mID
```

* For each movie that has at least one rating, find the highest number of stars that movie received. 
Return the movie title and number of stars. Sort by movie title. 
```
select title, Mstars
from (select max(stars)as Mstars, title
      from Rating as Ra, Movie as M
      where Ra.mID = M.mID
      group by title)
order by title   
```

* List movie titles and average ratings, from highest-rated to lowest-rated. 
If two or more movies have the same average rating, list them in alphabetical order. 
```
select M.title, AVGstar
from (select mID, avg(stars) as AVGstar 
      from Rating
      group by mID) as Ra, Movie as M
where M.mID = Ra.mID
order by AVGstar desc, M.title;
```

* Find the names of all reviewers who have contributed three or more ratings. 
```
select name
from Rating natural join Reviewer
group by rID
having count(mID)>=3
```
