# JOINing data in R using data.table
Ronald Stalder  
23-12-2014  

## &nbsp;  
#### Tutorial on how to perform the different types 
#### of SQL-JOINs in R using data.table  

## &nbsp;  
## Synopsis

  
- This tutorial is **based on the following articles**:    
       - [basic overview of SQL JOINs by Pinal Dave][Pinal]  
       - the Wikipedia entry on [Join (SQL)](http://en.wikipedia.org/wiki/Join_(SQL))  
       - more elaborate: [The joy of joining data.tables](http://www.magesblog.com/2014/06/the-joy-of-joining-datatables.html)  
  
- For **joining data.tables, the basics are**:    
       - the `ON` or `USING` clause is defined by setting the keys on the tables with `setkey()`  
       - without anything else, `TABLE_X[TABLE_Y]` returns a **right outer join**; setting `nomatch=0` it returns a **inner join**  
  
- The **source of this tutorial**, with the example datasets, is available [here on GitHub](https://github.com/ronasta/JOINing-Data-with-R-data.table)  


## &nbsp;  
## Summary
  
  
JOIN type | DT syntax | data.table::merge() syntax
------ | ----- | -----
INNER | X[Y, nomatch=0] | merge(X, Y, all=FALSE)
LEFT OUTER | Y[X] | merge(X, Y, all.x=TRUE)
RIGHT OUTER | X[Y] | merge(X, Y, all.y=TRUE)
FULL OUTER | - | merge(X, Y, all=TRUE)
FULL OUTER WHERE NULL (NOT INNER) | - | merge(X, Y, all=TRUE), subset NA
CROSS (cartesian) | - | - (see below)

## &nbsp;  
## Example Data

The example data consists of the following two tables:  



&nbsp;  

<table>
<caption>table Employees</caption>
 <thead>
  <tr>
   <th style="text-align:right;"> Employee </th>
   <th style="text-align:left;"> EmployeeName </th>
   <th style="text-align:right;"> Department </th>
   <th style="text-align:right;"> Salary </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:left;"> Alice </td>
   <td style="text-align:right;"> 11 </td>
   <td style="text-align:right;"> 800 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:left;"> Bob </td>
   <td style="text-align:right;"> 11 </td>
   <td style="text-align:right;"> 600 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:left;"> Carla </td>
   <td style="text-align:right;"> 12 </td>
   <td style="text-align:right;"> 900 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:left;"> Daniel </td>
   <td style="text-align:right;"> 12 </td>
   <td style="text-align:right;"> 1000 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 5 </td>
   <td style="text-align:left;"> Evelyn </td>
   <td style="text-align:right;"> 13 </td>
   <td style="text-align:right;"> 800 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 6 </td>
   <td style="text-align:left;"> Ferdinand </td>
   <td style="text-align:right;"> 21 </td>
   <td style="text-align:right;"> 700 </td>
  </tr>
</tbody>
</table>

&nbsp;  

<table>
<caption>table Departments</caption>
 <thead>
  <tr>
   <th style="text-align:right;"> Department </th>
   <th style="text-align:left;"> DepartmentName </th>
   <th style="text-align:right;"> Manager </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 11 </td>
   <td style="text-align:left;"> Production </td>
   <td style="text-align:right;"> 1 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 12 </td>
   <td style="text-align:left;"> Sales </td>
   <td style="text-align:right;"> 4 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 13 </td>
   <td style="text-align:left;"> Marketing </td>
   <td style="text-align:right;"> 5 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 14 </td>
   <td style="text-align:left;"> Research </td>
   <td style="text-align:right;"> NA </td>
  </tr>
</tbody>
</table>

## &nbsp;  
## Inner Join

*Figure linked from [Pinal Dave][Pinal]*  
![Figure1 from Pinal Dave](http://www.pinaldave.com/bimg/March09UG/inner%20join.jpg)  

**the *INNER JOIN* returns the rows with a match in both tables**

&nbsp;  


```r
# set the ON clause as keys of the tables:
setkey(Employees,Department)
setkey(Departments,Department)

# perform the join, eliminating not matched rows from Right
Result <- Employees[Departments, nomatch=0]
```
&nbsp;  
<table>
<caption>Inner Join</caption>
 <thead>
  <tr>
   <th style="text-align:right;"> Employee </th>
   <th style="text-align:left;"> EmployeeName </th>
   <th style="text-align:right;"> Department </th>
   <th style="text-align:right;"> Salary </th>
   <th style="text-align:left;"> DepartmentName </th>
   <th style="text-align:right;"> Manager </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:left;"> Alice </td>
   <td style="text-align:right;"> 11 </td>
   <td style="text-align:right;"> 800 </td>
   <td style="text-align:left;"> Production </td>
   <td style="text-align:right;"> 1 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:left;"> Bob </td>
   <td style="text-align:right;"> 11 </td>
   <td style="text-align:right;"> 600 </td>
   <td style="text-align:left;"> Production </td>
   <td style="text-align:right;"> 1 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:left;"> Carla </td>
   <td style="text-align:right;"> 12 </td>
   <td style="text-align:right;"> 900 </td>
   <td style="text-align:left;"> Sales </td>
   <td style="text-align:right;"> 4 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:left;"> Daniel </td>
   <td style="text-align:right;"> 12 </td>
   <td style="text-align:right;"> 1000 </td>
   <td style="text-align:left;"> Sales </td>
   <td style="text-align:right;"> 4 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 5 </td>
   <td style="text-align:left;"> Evelyn </td>
   <td style="text-align:right;"> 13 </td>
   <td style="text-align:right;"> 800 </td>
   <td style="text-align:left;"> Marketing </td>
   <td style="text-align:right;"> 5 </td>
  </tr>
</tbody>
</table>

&nbsp;  
**note:**  

> Employee #6, Ferdinand, has not been returned, as he's in a yet to be created department 21.  
> Neither has department #14, Research, as there are no employees.  

## &nbsp;  
## Left Outer Join

*Figure linked from [Pinal Dave][Pinal]*  
![Figure2 from Pinal Dave](http://www.pinaldave.com/bimg/March09UG/left%20join.jpg)  

**the *LEFT OUTER JOIN* returns all the rows from the left table, filling in matched columns (or NA) from the right table**

&nbsp;  


```r
# set the ON clause as keys of the tables:
setkey(Employees,Department)
setkey(Departments,Department)

# perform the join using the merge function
Result <- merge(Employees,Departments, all.x=TRUE)
```
&nbsp;  
<table>
<caption>Left Outer Join</caption>
 <thead>
  <tr>
   <th style="text-align:right;"> Department </th>
   <th style="text-align:right;"> Employee </th>
   <th style="text-align:left;"> EmployeeName </th>
   <th style="text-align:right;"> Salary </th>
   <th style="text-align:left;"> DepartmentName </th>
   <th style="text-align:right;"> Manager </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 11 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:left;"> Alice </td>
   <td style="text-align:right;"> 800 </td>
   <td style="text-align:left;"> Production </td>
   <td style="text-align:right;"> 1 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 11 </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:left;"> Bob </td>
   <td style="text-align:right;"> 600 </td>
   <td style="text-align:left;"> Production </td>
   <td style="text-align:right;"> 1 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 12 </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:left;"> Carla </td>
   <td style="text-align:right;"> 900 </td>
   <td style="text-align:left;"> Sales </td>
   <td style="text-align:right;"> 4 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 12 </td>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:left;"> Daniel </td>
   <td style="text-align:right;"> 1000 </td>
   <td style="text-align:left;"> Sales </td>
   <td style="text-align:right;"> 4 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 13 </td>
   <td style="text-align:right;"> 5 </td>
   <td style="text-align:left;"> Evelyn </td>
   <td style="text-align:right;"> 800 </td>
   <td style="text-align:left;"> Marketing </td>
   <td style="text-align:right;"> 5 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 21 </td>
   <td style="text-align:right;"> 6 </td>
   <td style="text-align:left;"> Ferdinand </td>
   <td style="text-align:right;"> 700 </td>
   <td style="text-align:left;"> NA </td>
   <td style="text-align:right;"> NA </td>
  </tr>
</tbody>
</table>

&nbsp;  
**note:**  

> Employee #6, Ferdinand, has been returned with department name as NA.  
> Department #14, Research, has not been returned.  
> If the column order Left &rarr; Right has to be preserved, we need to elaborate:  

&nbsp;  


```r
# get the columns of the tables:
leftCols <- colnames(Employees)
rightCols <- colnames(Departments)
# remove the match key of the Right table
rightCols <- setdiff(rightCols,key(Departments))
# set the column order
setcolorder(Result,c(leftCols,rightCols))
```
&nbsp;  
<table>
<caption>Left Outer Join, preserved column order</caption>
 <thead>
  <tr>
   <th style="text-align:right;"> Employee </th>
   <th style="text-align:left;"> EmployeeName </th>
   <th style="text-align:right;"> Department </th>
   <th style="text-align:right;"> Salary </th>
   <th style="text-align:left;"> DepartmentName </th>
   <th style="text-align:right;"> Manager </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:left;"> Alice </td>
   <td style="text-align:right;"> 11 </td>
   <td style="text-align:right;"> 800 </td>
   <td style="text-align:left;"> Production </td>
   <td style="text-align:right;"> 1 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:left;"> Bob </td>
   <td style="text-align:right;"> 11 </td>
   <td style="text-align:right;"> 600 </td>
   <td style="text-align:left;"> Production </td>
   <td style="text-align:right;"> 1 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:left;"> Carla </td>
   <td style="text-align:right;"> 12 </td>
   <td style="text-align:right;"> 900 </td>
   <td style="text-align:left;"> Sales </td>
   <td style="text-align:right;"> 4 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:left;"> Daniel </td>
   <td style="text-align:right;"> 12 </td>
   <td style="text-align:right;"> 1000 </td>
   <td style="text-align:left;"> Sales </td>
   <td style="text-align:right;"> 4 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 5 </td>
   <td style="text-align:left;"> Evelyn </td>
   <td style="text-align:right;"> 13 </td>
   <td style="text-align:right;"> 800 </td>
   <td style="text-align:left;"> Marketing </td>
   <td style="text-align:right;"> 5 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 6 </td>
   <td style="text-align:left;"> Ferdinand </td>
   <td style="text-align:right;"> 21 </td>
   <td style="text-align:right;"> 700 </td>
   <td style="text-align:left;"> NA </td>
   <td style="text-align:right;"> NA </td>
  </tr>
</tbody>
</table>

#### &nbsp;  
#### A typical use case is to match in labels, e.g. in our Employees table  
#### substitute the department number by its name:
#### &nbsp;  


```r
# set the ON clause as keys of the tables:
setkey(Employees,Department)
setkey(Departments,Department)

# defining the Result columns, substitute Department by DepartmentName
leftCols <- colnames(Employees)
leftCols <- sub("Department","DepartmentName",leftCols)

# perform the join, inverting the tables, return defined columns
Result <- Departments[Employees][, leftCols, with=FALSE]

# -- or --
# Result <- merge(Employees, Departments, all.x=TRUE)
# Result <- Result[, setCols, with=FALSE]
```
&nbsp;  
<table>
<caption>Left Outer Join - matching in DepartmentName</caption>
 <thead>
  <tr>
   <th style="text-align:right;"> Employee </th>
   <th style="text-align:left;"> EmployeeName </th>
   <th style="text-align:left;"> DepartmentName </th>
   <th style="text-align:right;"> Salary </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:left;"> Alice </td>
   <td style="text-align:left;"> Production </td>
   <td style="text-align:right;"> 800 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:left;"> Bob </td>
   <td style="text-align:left;"> Production </td>
   <td style="text-align:right;"> 600 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:left;"> Carla </td>
   <td style="text-align:left;"> Sales </td>
   <td style="text-align:right;"> 900 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:left;"> Daniel </td>
   <td style="text-align:left;"> Sales </td>
   <td style="text-align:right;"> 1000 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 5 </td>
   <td style="text-align:left;"> Evelyn </td>
   <td style="text-align:left;"> Marketing </td>
   <td style="text-align:right;"> 800 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 6 </td>
   <td style="text-align:left;"> Ferdinand </td>
   <td style="text-align:left;"> NA </td>
   <td style="text-align:right;"> 700 </td>
  </tr>
</tbody>
</table>


## &nbsp;  
## Right Outer Join

*Figure linked from [Pinal Dave][Pinal]*  
![Figure2 from Pinal Dave](http://www.pinaldave.com/bimg/March09UG/right%20join.jpg)  

**the *RIGHT OUTER JOIN* returns all the rows from the right table, filling in matched columns (or NA) from the left table**

&nbsp;  


```r
# set the ON clause as keys of the tables:
setkey(Employees,Department)
setkey(Departments,Department)

# perform the join - this is the basic join for data.table
Result <- Employees[Departments]
# this corresponds to
# Result <- merge(Employees,Departments, all.y=TRUE)
```
&nbsp;  
<table>
<caption>Right Outer Join</caption>
 <thead>
  <tr>
   <th style="text-align:right;"> Employee </th>
   <th style="text-align:left;"> EmployeeName </th>
   <th style="text-align:right;"> Department </th>
   <th style="text-align:right;"> Salary </th>
   <th style="text-align:left;"> DepartmentName </th>
   <th style="text-align:right;"> Manager </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:left;"> Alice </td>
   <td style="text-align:right;"> 11 </td>
   <td style="text-align:right;"> 800 </td>
   <td style="text-align:left;"> Production </td>
   <td style="text-align:right;"> 1 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:left;"> Bob </td>
   <td style="text-align:right;"> 11 </td>
   <td style="text-align:right;"> 600 </td>
   <td style="text-align:left;"> Production </td>
   <td style="text-align:right;"> 1 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:left;"> Carla </td>
   <td style="text-align:right;"> 12 </td>
   <td style="text-align:right;"> 900 </td>
   <td style="text-align:left;"> Sales </td>
   <td style="text-align:right;"> 4 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:left;"> Daniel </td>
   <td style="text-align:right;"> 12 </td>
   <td style="text-align:right;"> 1000 </td>
   <td style="text-align:left;"> Sales </td>
   <td style="text-align:right;"> 4 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 5 </td>
   <td style="text-align:left;"> Evelyn </td>
   <td style="text-align:right;"> 13 </td>
   <td style="text-align:right;"> 800 </td>
   <td style="text-align:left;"> Marketing </td>
   <td style="text-align:right;"> 5 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:left;"> NA </td>
   <td style="text-align:right;"> 14 </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:left;"> Research </td>
   <td style="text-align:right;"> NA </td>
  </tr>
</tbody>
</table>


## &nbsp;  
## Full Outer Join

*Figure linked from [Pinal Dave][Pinal]*  
![Figure2 from Pinal Dave](http://www.pinaldave.com/bimg/March09UG/outer%20join.jpg)  

**the *FULL OUTER JOIN* returns all the rows from both tables, filling in matched columns (or NA)**

&nbsp;  


```r
# set the ON clause as keys of the tables:
setkey(Employees,Department)
setkey(Departments,Department)

# perform the join
Result <- merge(Employees,Departments, all=TRUE)
```
&nbsp;  
<table>
<caption>Full Outer Join</caption>
 <thead>
  <tr>
   <th style="text-align:right;"> Department </th>
   <th style="text-align:right;"> Employee </th>
   <th style="text-align:left;"> EmployeeName </th>
   <th style="text-align:right;"> Salary </th>
   <th style="text-align:left;"> DepartmentName </th>
   <th style="text-align:right;"> Manager </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 11 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:left;"> Alice </td>
   <td style="text-align:right;"> 800 </td>
   <td style="text-align:left;"> Production </td>
   <td style="text-align:right;"> 1 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 11 </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:left;"> Bob </td>
   <td style="text-align:right;"> 600 </td>
   <td style="text-align:left;"> Production </td>
   <td style="text-align:right;"> 1 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 12 </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:left;"> Carla </td>
   <td style="text-align:right;"> 900 </td>
   <td style="text-align:left;"> Sales </td>
   <td style="text-align:right;"> 4 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 12 </td>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:left;"> Daniel </td>
   <td style="text-align:right;"> 1000 </td>
   <td style="text-align:left;"> Sales </td>
   <td style="text-align:right;"> 4 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 13 </td>
   <td style="text-align:right;"> 5 </td>
   <td style="text-align:left;"> Evelyn </td>
   <td style="text-align:right;"> 800 </td>
   <td style="text-align:left;"> Marketing </td>
   <td style="text-align:right;"> 5 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 14 </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:left;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:left;"> Research </td>
   <td style="text-align:right;"> NA </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 21 </td>
   <td style="text-align:right;"> 6 </td>
   <td style="text-align:left;"> Ferdinand </td>
   <td style="text-align:right;"> 700 </td>
   <td style="text-align:left;"> NA </td>
   <td style="text-align:right;"> NA </td>
  </tr>
</tbody>
</table>


## &nbsp;  
## Full Outer Join Where NULL - a.k.a "NOT INNER join"

*Figure linked from [Pinal Dave][Pinal]*  
![Figure2 from Pinal Dave](http://www.pinaldave.com/bimg/March09UG/outer%20join%20null.jpg)  

**the *NOT INNER JOIN* returns all the rows from both tables, where no match was obtained**

&nbsp;  


```r
# set the ON clause as keys of the tables:
setkey(Employees,Department)
setkey(Departments,Department)

# perform the join, retain only NA from matched cols on both side
Result <- merge(Employees,Departments, all=TRUE)
Result <- Result[is.na(EmployeeName) | is.na(DepartmentName)]
```
&nbsp;  
<table>
<caption>Full Outer Join Where Null</caption>
 <thead>
  <tr>
   <th style="text-align:right;"> Department </th>
   <th style="text-align:right;"> Employee </th>
   <th style="text-align:left;"> EmployeeName </th>
   <th style="text-align:right;"> Salary </th>
   <th style="text-align:left;"> DepartmentName </th>
   <th style="text-align:right;"> Manager </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 14 </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:left;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:left;"> Research </td>
   <td style="text-align:right;"> NA </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 21 </td>
   <td style="text-align:right;"> 6 </td>
   <td style="text-align:left;"> Ferdinand </td>
   <td style="text-align:right;"> 700 </td>
   <td style="text-align:left;"> NA </td>
   <td style="text-align:right;"> NA </td>
  </tr>
</tbody>
</table>


## &nbsp;  
## Cross Join

*Figure linked from [Pinal Dave][Pinal]*  
![Figure2 from Pinal Dave](http://www.pinaldave.com/bimg/March09UG/cross%20join%20-%20half.jpg)  

**the *CROSS JOIN* returns all the rows from one table combined with all the rows from the other**  

As the documentation `?data.table` states, this ***"is (deliberately) difficult to achieve in data.table"***. So, I'll leave this as an excersise to the reader :-). In the context of the examples here, I can't see the usefulness of a cross join, anyway.


## &nbsp;
## System and R-Packages Information


```r
if (Sys.which("uname") != "") system("uname -srpi", intern=TRUE)
if (Sys.which("lsb_release") != "") 
    print(paste("Ubuntu:",system("lsb_release -rc", intern=TRUE)[1]))
#print(paste("Rstudio version:", rstudio::versionInfo()$version))  # does not work in Rmd
```

```
## [1] "Linux 3.13.0-43-generic x86_64 x86_64"
## [1] "Ubuntu: Release:\t14.04"
```

```r
sessionInfo()
```

```
## R version 3.1.2 (2014-10-31)
## Platform: x86_64-pc-linux-gnu (64-bit)
## 
## locale:
##  [1] LC_CTYPE=pt_BR.UTF-8       LC_NUMERIC=C              
##  [3] LC_TIME=pt_BR.UTF-8        LC_COLLATE=pt_BR.UTF-8    
##  [5] LC_MONETARY=pt_BR.UTF-8    LC_MESSAGES=pt_BR.UTF-8   
##  [7] LC_PAPER=pt_BR.UTF-8       LC_NAME=C                 
##  [9] LC_ADDRESS=C               LC_TELEPHONE=C            
## [11] LC_MEASUREMENT=pt_BR.UTF-8 LC_IDENTIFICATION=C       
## 
## attached base packages:
## [1] stats     graphics  grDevices utils     datasets  methods   base     
## 
## other attached packages:
## [1] data.table_1.9.4
## 
## loaded via a namespace (and not attached):
##  [1] chron_2.3-45     digest_0.6.4     evaluate_0.5.5   formatR_1.0     
##  [5] highr_0.4        htmltools_0.2.6  knitr_1.8        plyr_1.8.1      
##  [9] Rcpp_0.11.3      reshape2_1.4.1   rmarkdown_0.3.10 stringr_0.6.2   
## [13] tools_3.1.2      yaml_2.1.13
```
  
The report was produced using `RStudio/knittr`  
**on 2014-12-23 at 14:07:00 (BRT, GMT-0300)**  

[Pinal]:http://blog.sqlauthority.com/2009/04/13/sql-server-introduction-to-joins-basic-of-joins/
