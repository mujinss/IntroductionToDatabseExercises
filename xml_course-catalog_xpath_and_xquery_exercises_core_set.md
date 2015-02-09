# xml course-catalog xpath and xquery exercises core set

In these exercises, you will be working with a small XML data set drawn from the Stanford course catalog. 
There are multiple departments, each with a department chair, some courses, and professors and/or lecturers who teach courses.
The XML data is [here].(https://prod-c2g.s3.amazonaws.com/db/Winter2013/files/courses-noID.xml)

* Return all Title elements `(of both departments and courses)`. 
```
doc("courses.xml")//Title
```

* Return last names of all department chairs. 
```
doc("courses.xml")/Course_Catalog/Department/Chair/Professor/Last_Name
```

* Return titles of courses with enrollment greater than 500. 
```
doc("courses.xml")/Course_Catalog//Course[@Enrollment > 500]/Title
```

* Return titles of departments that have some course that takes "CS106B" as a prerequisite. 
```
for $d in doc("courses.xml")/Course_Catalog/Department
where $d/Course/Prerequisites/Prereq = "CS106B"
return $d/Title
```

* Return last names of all professors or lecturers who use a middle initial. 
Don't worry about eliminating duplicates. 
```
for $m in doc("courses.xml")//Middle_Initial
return $m/following-sibling::*
```

* Return the count of courses that have a cross-listed course 
'(i.e., that have "Cross-listed" in their description)'. 
```
let $r:= (for $c in doc("courses.xml")//Course
where contains($c/Description, "Cross-listed")
return $c)
return count($r)
```

* Return the average enrollment of all courses in the CS department. 
```
avg(doc("courses.xml")//Department[@Code = "CS"]/Course/@Enrollment)
```

* Return last names of instructors teaching at least one course that has "system" 
in its description and enrollment greater than 100. 
```
for $c in doc("courses.xml")//Course
where contains($c/Description, "system") and $c/@Enrollment > 100
return $c/Instructors//Last_Name
```
### challenge level exercises
* Return the title of the course with the largest enrollment. 
```
for $c in doc("courses.xml")//Course[@Enrollment]
where not (xs:int($c/@Enrollment) <= $c/preceding::Course/xs:int(@Enrollment))
                      and not (xs:int($c/@Enrollment) <= $c/following::Course/xs:int(@Enrollment))
return $c/Title 
```
* Return course numbers of courses that have the same title as some other course. 
```
doc("courses.xml")//Course[Title = preceding::Course/Title or 
                          Title = following::Course/Title] 
                        /data(@Number)
```

* Return the number (count) of courses that have no lecturers as instructors. 
```
let $n := doc("courses.xml")//Course[count(Instructors/Lecturer) = 0]
return count($n)
```

* Return titles of courses taught by the chair of a department. For this question, you may assume that all professors have distinct last names. 
```
doc("courses.xml")//Course[Instructors//Last_Name = preceding::Chair/Professor/Last_Name]/Title
```

* Return titles of courses taught by a professor with the last name "Ng" but not by a professor with the last name "Thrun". 
```
doc("courses.xml")//Course[Instructors/Professor/Last_Name = "Ng" 
                        and count(Instructors/Professor[Last_Name = "Thrun"]) = 0]/Title
```

* Return course numbers of courses that have a course taught by Eric Roberts as a prerequisite. 
```
for $p in doc("courses.xml")//Course/Prerequisites/Prereq 
for $c in doc("courses.xml")//Course
where $p = $c/@Number and $c//Last_Name = "Roberts" and $c//First_Name = "Eric"
return $p/ancestor::Course/data(@Number)        
```

* Create a summary of CS classes: List all CS department courses in order of enrollment. For each course include only its Enrollment `(as an attribute)` and its Title `(as a subelement)`. 
```
<Summary>
{for $c in doc("courses.xml")/Course_Catalog/Department[@Code = "CS"]/Course
 order by number($c/@Enrollment) 
 return
    <Course>{$c/@Enrollment}
            {$c/Title}
    </Course>}
</Summary>    
```

* Return a "Professors" element that contains as subelements a listing of all professors in all departments, sorted by last name with each professor appearing once. The "Professor" subelements should have the same structure as in the original data. For this question, you may assume that all professors have distinct last names. Watch out -- the presence/absence of middle initials may require some special handling.
```
let $prof := doc("courses.xml")//Professor
let $distinct_prof := $prof except(for $ln in $prof/Last_Name
                        where $ln = $ln/following::Last_Name 
                        return $ln/..)
 return 
<Professors> {for $p in $distinct_prof 
  order by $p/Last_Name
  return $p}
</Professors>
```




