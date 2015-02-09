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




