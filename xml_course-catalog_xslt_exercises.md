# xml course-catalog xslt exercises 
## core set

In these exercises, you will be working with a small XML data set drawn from the Stanford course catalog. 
There are multiple departments, each with a department chair, some courses, and professors and/or lecturers who teach courses. 
The XML data is [here] (https://prod-c2g.s3.amazonaws.com/db/Winter2013/files/courses-noID.xml). 

* Return a list of department titles. 
```
<?xml version="1.0" encoding="ISO-8859-1"?>
    <xsl:stylesheet version="2.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
        <xsl:template match="Department">
            <Title> <xsl:value-of select="Title" /> </Title>
        </xsl:template>
    </xsl:stylesheet>
```

* Return a list of department elements with no attributes and two subelements each: 
the department title and the entire Chair subelement structure. 
```
<?xml version="1.0" encoding="ISO-8859-1"?>
    <xsl:stylesheet version="2.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
        <xsl:template match="Department">
            <Department>
                <Title> <xsl:value-of select="Title" /> </Title>
                <xsl:copy-of select="./Chair" />
            </Department>
        </xsl:template>
    </xsl:stylesheet>
```

## challenge level exercises

* Create a summarized version of the EE part of the course catalog. 
For each course in EE, return a Course element, with its Number and Title as attributes, its Description as a subelement, 
and the last name of each instructor as an Instructor subelement. 
Discard all information about department titles, chairs, enrollment, and prerequisites, 
as well as all courses in departments other than EE.
```
<?xml version="1.0" encoding="ISO-8859-1"?>
    <xsl:stylesheet version="2.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
        <xsl:template match='Department[@Code="EE"]/Course'>
        <Course
            Number="{@Number}"
            Title="{./Title}">
            <xsl:copy-of select="./Description"/>
            <xsl:for-each select="Instructors//Last_Name">
            <Instructor> <xsl:value-of select="."/></Instructor>
            </xsl:for-each>
        </Course>
        </xsl:template>
     <xsl:template match="text()"/>   
    </xsl:stylesheet>
```

* Create an HTML table with one-pixel border that lists all CS department courses with enrollment greater than 200. 
Each row should contain three cells: the course number in italics, course title in bold, and enrollment. 
Sort the rows alphabetically by course title. No header is needed.
```
<?xml version = "1.0" encoding="ISO-8859-1"?>
<xsl:stylesheet version="2.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
    <xsl:template match="Department[@Code='CS']"> 
    <table border = "1">
        <xsl:for-each select ="Course">
        <xsl:sort select ="Title"/>
            <xsl:if test="200 &lt; @Enrollment">
                <tr>
                <td><i><xsl:value-of select="@Number"/></i></td>
                <td><b><xsl:value-of select="Title"/></b></td>
                <td><xsl:value-of select="@Enrollment"/></td>
                </tr>
            </xsl:if>
            </xsl:for-each>
        </table>        
</xsl:template> 
<xsl:template match="text()"/>

</xsl:stylesheet>
```
