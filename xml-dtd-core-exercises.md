# XML-DTD-Core-Exercises 



1. In this question, you are to create a DTD for a small XML data set about world countries. 
This data is adapted from the [Mondial 3.0 database as hosted by the University of Washington]
(http://www.cs.washington.edu/research/xmldatasets/www/repository.html#mondial), 
and was originally compiled by [the Georg-August University of Goettingen Institute for Informatics]
(http://www.dbis.informatik.uni-goettingen.de/Mondial/). 
Each country has a name, population, and area `(in sq. km)`. Some countries also list languages 
`(with percentages of the population that speaks each language)` and/or cities `(with names and populations)`. 
The XML data is [here] (https://prod-c2g.s3.amazonaws.com/db/Winter2013/files/countries.xml)
    
 ```
 <!ELEMENT countries (country*)>
      <!ELEMENT country (city*, language*)>
        <!ELEMENT city (name, population)>
            <!ELEMENT name (#PCDATA)>
            <!ELEMENT population (#PCDATA)>
        <!ELEMENT language (#PCDATA)>
        <!ATTLIST language percentage CDATA #REQUIRED>            
    <!ATTLIST country name CDATA #REQUIRED>
    <!ATTLIST country population CDATA #REQUIRED>
    <!ATTLIST country area CDATA #REQUIRED> 
  ```
    
2. In this question, you are to create a DTD for a small XML data set drawn from the Stanford course catalog.
   There are multiple departments, each with a department chair, some courses, and professors and/or lecturers 
   who teach courses. The XML data is [here] (https://prod-c2g.s3.amazonaws.com/db/Winter2013/files/courses-noID.xml)
	
 ```
 <!ELEMENT Course_Catalog (Department*)>
    <!ELEMENT Department (Title, Chair, Course+)>
    <!ATTLIST Department Code CDATA #REQUIRED>  
    <!ELEMENT Chair (Professor)>
    <!ELEMENT Course (Title, Description?, Instructors+, Prerequisites*)>
    <!ATTLIST Course Number CDATA #REQUIRED>
    <!ATTLIST Course Enrollment CDATA #IMPLIED>
    <!ELEMENT Title (#PCDATA)>
    <!ELEMENT Description (#PCDATA)>
    <!ELEMENT Instructors (Professor|Lecturer)*>
    <!ELEMENT Prerequisites (Prereq+)>
    <!ELEMENT Lecturer (First_Name, Middle_Initial?, Last_Name)>
    <!ELEMENT Professor (First_Name, Middle_Initial?, Last_Name)>
    <!ELEMENT First_Name (#PCDATA)>
    <!ELEMENT Middle_Initial (#PCDATA)>
    <!ELEMENT Last_Name (#PCDATA)>
    <!ELEMENT Prereq (#PCDATA)>
  ```
    
3. In this question, you are to create a DTD for a different version of the data set 
   drawn from the Stanford course catalog. This version encodes the data using ID and IDREF(S) attributes. 
   The XML data is [here] (https://prod-c2g.s3.amazonaws.com/db/Winter2013/files/courses-ID.xml)
	
  ```
  <!ELEMENT Course_Catalog (Department*)>
    <!ELEMENT Department (Title, Course+, (Professor|Lecturer)*)>
    <!ATTLIST Department Code CDATA #REQUIRED
        Chair IDREF #REQUIRED>
    <!ELEMENT Course (Title, Description?)>
    <!ATTLIST Course 
        Number ID #REQUIRED
        Prerequisites IDREFS #IMPLIED
        Instructors IDREFS #REQUIRED
        Enrollment CDATA #IMPLIED>
    <!ELEMENT Title (#PCDATA)>    
    <!ELEMENT Description ANY>
    <!ELEMENT Courseref EMPTY>
    <!ATTLIST Courseref Number IDREFS #IMPLIED>
    <!ELEMENT Lecturer (First_Name, Middle_Initial?, Last_Name)>
    <!ATTLIST Lecturer InstrID ID #REQUIRED>
    <!ELEMENT Professor (First_Name, Middle_Initial?, Last_Name)>
    <!ATTLIST Professor InstrID ID #REQUIRED>
    <!ELEMENT First_Name (#PCDATA)>
    <!ELEMENT Middle_Initial (#PCDATA)>
    <!ELEMENT Last_Name (#PCDATA)>
  ```

  
