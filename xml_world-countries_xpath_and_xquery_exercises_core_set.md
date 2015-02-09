# xml world-countries xpath and xquery exercises core set
In these exercises, you will be working with a small XML data set about world countries. 
This data is adapted from the Mondial 3.0 database as hosted by the University of Washington, 
and was originally compiled by [the Georg-August University of Goettingen Institute for Informatics](http://www.dbis.informatik.uni-goettingen.de/Mondial/). 
Each country has a name, population, and area `(in sq. km)`. Some countries also list languages 
`(with percentages of the population that speaks each language)` and/or cities `(with names and populations)`. 
The XML data is [here] (https://prod-c2g.s3.amazonaws.com/db/Winter2013/files/countries.xml). 

* Return the area of Mongolia. 
```
for $c in doc("countries.xml")/countries/country[@name = "Mongolia"]
return $c/data(@area)
```

* Return the names of all cities that have the same name as the country in which they are located. 
```
for $c in doc("countries.xml")/countries/country
where $c/city/name = $c/@name
return <name> {data($c/@name)}</name>
```

* Return the average population of Russian-speaking countries. 
```
let $p := (for $c in doc("countries.xml")//country
where $c/language = "Russian"
return $c/@population)
return avg($p)
```

* Return the names of all countries where over 50% of the population speaks German.
```
for $l in doc("countries.xml")/countries/country/language
where $l/. = "German" and $l/@percentage>50
return $l/parent::*/data(@name)
```

* Return the name of the country with the highest population.
```
let $m:= max(doc("countries.xml")/countries//data(@population))
for $c in doc("countries.xml")/countries/country
where $c/@population = $m
return $c/data(@name)
```

### challenge level exercises
* Return the names of all countries that have at least three cities with population greater than 3 million. 
```
doc("countries.xml")//country[count(city[xs:int(population) > 3000000])>=3 ]/data(@name)
```

* Create a list of French-speaking and German-speaking countries. The result should take the form:
```
<result>
  <French>
    <country>country-name</country>
    <country>country-name</country>
    ...
  </French>
  <German>
    <country>country-name</country>
    <country>country-name</country>
    ...
  </German>
</result>
```
```
<result>
<French>
{for $c1 in doc("countries.xml")//country[language = "French"]
 return
    <country>{$c1/data(@name)}</country>}
    </French>
<German>
{for $c2 in doc("countries.xml")//country[language = "German"]
 return 
     <country>{$c2/data(@name)}</country>}
 </German>
 </result>
 ```
 
* Return the names of all countries containing a city such that some other country has a city of the same name. 
```
for $c1 in doc("countries.xml")//country
for $c2 in doc("countries.xml")//country
where $c1/city/name = $c2/city/name and $c1/@name != $c2/@name
return $c1/data(@name)
```

* Return the average number of languages spoken in countries where Russian is spoken. 
```
let $c:= doc("countries.xml")//country[language = "Russian"]
return (count($c/language) div count($c))
```

* Return all country-language pairs where the language is spoken in the country 
and the name of the country textually contains the language name.
Return each pair as a country element with language attribute, e.g.,
```
<country language="French">French Guiana</country>
```
```
for $c in doc("countries.xml")//country
for $l in $c/language
where contains ($c/@name, $l)
return <country language = "{$l}"> {data($c/@name)} </country>
```

* Return all countries that have at least one city with population greater than 7 million. 
For each one, return the country name along with the cities greater than 7 million, in the format:
```
<country name="country-name">
  <big>city-name</big>
  <big>city-name</big>
  ...
</country>
```
```
for $c1 in doc("countries.xml")//country
where count($c1/city[population > 7000000])>=1 
return <country name = "{data($c1/@name)}">
      {for $c2 in $c1/city[population > 7000000]
            return <big>{data($c2/name)}</big>}
</country>
```

* Return all countries where at least one language is listed, but the total percentage for all listed languages is less than 90%. 
Return the country element with its name attribute and its language subelements, but no other attributes or subelements. 
```
for $c in doc("countries.xml")//country[language]
where sum(data($c/language/@percentage))< 90
return <country name = "{data($c/@name)}">
       {$c/language}
       </country>
```

* Return all countries where at least one language is listed, 
and every listed language is spoken by less than 20% of the population. 
Return the country element with its name attribute and its language subelements, but no other attributes or subelements. 
```
for $c in doc("countries.xml")//country[language]
where every $l in $c/language satisfies data($l/@percentage)<20
return <country name = "{data($c/@name)}">
        {$c/language}
       </country>
```



