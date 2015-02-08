# sql movie rating query exercises challenge level

You've started a new movie-rating website, and you've been collecting data on reviewers' ratings of various movies. 
There's not much data yet, but you can still try out some interesting queries. Here's the schema: 

Movie `( mID, title, year, director )` 
English: There is a movie with ID number mID, a title, a release year, and a director. 

Reviewer `( rID, name )` 
English: The reviewer with ID number rID has a certain name. 

Rating `( rID, mID, stars, ratingDate )` 
English: The reviewer rID gave the movie mID a number of stars rating `(1-5)` on a certain ratingDate. 

Your queries will run over a small data set conforming to the schema. 
View the [database] (https://class.stanford.edu/c4x/Engineering/db/asset/moviedata.html) 

Important Notes:
Your queries are executed using SQLite, so you must conform to the SQL constructs supported by SQLite.
Unless a specific result ordering is asked for, you can return the result rows in any order.

* For each movie, return the title and the 'rating spread', 
that is, the difference between highest and lowest ratings given to that movie.
Sort by rating spread from highest to lowest, then by movie title. 
```
select title, max(stars)-min(stars) as ratingspread
from Movie as M, Rating as R
where M.mID = R.mID
group by R.mID
order by ratingspread desc, title
```

* Find the difference between the average rating of movies released before 1980 
and the average rating of movies released after 1980. 
`(Make sure to calculate the average rating for each movie, 
then the average of those averages for movies before 1980 and movies after. 
Don't just calculate the overall average rating before and after 1980.)` 
```
select avg(A1) - avg(A2)
from (select avg(stars) as A1 from Movie as M, Rating as R
                        where year < 1980 and M.mID = R.mID group by M.mID),
   (select avg(stars) as A2 from Movie as M, Rating as R
                        where year > 1980 and M.mID = R.mID group by M.mID)
 ```                       
                        
* Some directors directed more than one movie. 
For all such directors, return the titles of all movies directed by them, along with the director name. 
Sort by director name, then movie title.  
```
select title, M.director
from (select director from Movie
       group by director
       having count(*) > 1) as D, Movie as M
where D.director = M.director 
```

* Find the movie`(s)` with the highest average rating. Return the movie title`(s)` and average rating. 
```
select title, avg(stars) as avg_stars
from Movie as M, Rating as Ra
where Ra.mID = M.mID
group by Ra.mID                     
having avg(stars) >= (select max(avg_stars)
                       from (select avg(stars) as avg_stars
                             from Rating as Ra
                              group by Ra.mID))
 ```                     
                      

* Find the movie`(s)` with the lowest average rating. 
Return the movie title`(s)` and average rating. 
```
select title, avg(stars) as avg_stars
from Movie as M, Rating as Ra
where Ra.mID = M.mID
group by Ra.mID                     
having avg(stars) in (select min(avg_stars)
                       from (select avg(stars) as avg_stars
                             from Rating as Ra
                              group by Ra.mID))
```
                      
* For each director, return the director's name together with the title`(s)` of the movie`(s)` 
they directed that received the highest rating among all of their movies, 
and the value of that rating. Ignore movies whose director is NULL. 
``` 
select M.director, M.title, max(Ra.stars)
from Movie as M, Rating as Ra
where M.mID = Ra.mID
and M.director is not NULL
group by M.director
```
