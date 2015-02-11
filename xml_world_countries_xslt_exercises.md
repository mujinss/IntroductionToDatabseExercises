# xml world-countries xslt exercises

## Core set

In these exercises, you will be working with a small XML data set about world countries. 
This data is adapted from the Mondial 3.0 database as hosted by the University of Washington, 
and was originally compiled by [the Georg-August University of Goettingen Institute for Informatics](http://www.dbis.informatik.uni-goettingen.de/Mondial/). 
Each country has a name, population, and area `(in sq. km)`. Some countries also list languages 
`(with percentages of the population that speaks each language)` and/or cities `(with names and populations)`. 
The XML data is [here](https://prod-c2g.s3.amazonaws.com/db/Winter2013/files/countries.xml).

* Return all countries with population between 9 and 10 million. Retain the structure of country elements from the original data. 
Your solution should fill in the following stylesheet: 
```
<?xml version="1.0" encoding="ISO-8859-1"?>
    <xsl:stylesheet version="2.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
        <xsl:template match=...>
            ... template body ...
        </xsl:template>
        ... more templates as needed ...
    </xsl:stylesheet>
```
```
<?xml version="1.0" encoding="ISO-8859-1"?>
    <xsl:stylesheet version="2.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
        <xsl:template match="country[9000000 &lt; @population]">
            <xsl:copy-of select="." /> 
        </xsl:template>
        <xsl:template match="country[10000000 &lt; @population]" />
        <xsl:template match="text()" />
    </xsl:stylesheet>
```

Find all country names containing the string "stan"; return each one within a "Stan" element. 
`(Note: To specify quotes within an already-quoted XPath expression, use quot;.)` 
```
<?xml version="1.0" encoding="ISO-8859-1"?>
    <xsl:stylesheet version="2.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
        <xsl:template match="country[contains(./@name, &quot;stan&quot;)]">
            <Stan> <xsl:value-of select="./data(@name)" /> </Stan>
        </xsl:template>
        <xsl:template match="text()" />
    </xsl:stylesheet>
```

## Challenge Level
* Create a table using HTML constructs that lists all countries that have more than 3 languages. 
Each row should contain the country name in bold, population, area, and number of languages. 
Sort the rows in descending order of number of languages. 
No header is needed for the table, but use <table border="1"> to make it format nicely, 
should you choose to check your result in a browser. 
```
 <?xml version="1.0" encoding="ISO-8859-1"?>
    <xsl:stylesheet version="2.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
        <xsl:template match='/'>
            <html>
                <table border='1'>
                    <xsl:for-each select='//country[count(language)>3]'>
                    <xsl:sort order='descending' select='count(language)' data-type='number'/>
                   <tr>
                    <td><b><xsl:value-of select='data(@name)'/></b></td>
                    <td><xsl:value-of select='data(@population)'/></td>
                    <td><xsl:value-of select='data(@area)'/></td>
                    <td><xsl:value-of select='count(language)'/></td>
                   </tr>
                    </xsl:for-each>    
                   </table>
                </html>      
            </xsl:template> 
            
            <xsl:template match='text()'/>
       </xsl:stylesheet>
```

* Create an alternate version of the countries database: for each country, include its name and population as sublements, 
and the number of languages and number of cities as attributes `(called "languages" and "cities" respectively)`. 
```
<?xml version="1.0" encoding="ISO-8859-1"?>
    <xsl:stylesheet version="2.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
    <xsl:template match = '/'>
    <countries><xsl:apply-templates /></countries>
    </xsl:template>
        <xsl:template match='//country'>
            <country languages="{count(.//language)}" cities="{count(./city)}">
                <name><xsl:value-of select="data(@name)"/></name>
                <population><xsl:value-of select="data(@population)"/></population>
            </country>
        </xsl:template>
    </xsl:stylesheet>
```



