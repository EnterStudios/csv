---
layout: layout
title: Examples
---

# Examples

## Parsing a document

A simple example to show you how to parse a CSV document.

~~~.language-php
<?php
use League\Csv;

$csv = new Reader('/path/to/your/csv/file.csv');

//get the first row, usually the CSV header
$headers = $csv->fetchOne();

//get 25 rows starting from the 11th row
$res = $csv->setOffset(10)->setLimit(25)->fetchAll();
~~~
		
## Creating a CSV document

A simple example to show you how to create and download a CSV from a `PDOStatement` object

~~~.language-php
<?php
use League\Csv;

//we fetch the info from a DB using a PDO object
$sth = $dbh->prepare(
	"SELECT firstname, lastname, email FROM users LIMIT 200"
);
//because we don't want to duplicate the data for each row 
// PDO::FETCH_NUM could also have been used
$sth->setFetchMode(PDO::FETCH_ASSOC);
$sth->execute();

//we create the CSV into memory
$csv = new Writer(new SplTempFileObject);

//the library will automatically convert null value into an '' empty string
$csv->setNullHandling(Writer::NULL_AS_EMPTY);

//we insert the CSV header
$csv->insertOne(['firstname', 'lastname', 'email']);

// The PDOStatement Object implements the Traversable Interface
// that's why Writer::insertAll can directly insert
// the data into the CSV
$csv->insertAll($sth);

// Because you are providing the filename you don't have to 
// set the HTTP headers Writer::output can 
// directly set them for you
// The file is downloadable
$csv->output('users.csv');
die;
~~~

## Importing a CSV into a Database

A simple example to show you how to import some CSV data into a database using a `PDOStatement` object

~~~.language-php
<?php
use League\Csv;

//We are going to insert some data into the users table
$sth = $dbh->prepare(
	"INSERT INTO users (firstname, lastname, email) VALUES (:firstname, :lastname, :email)"
);

$csv = new Reader('/path/to/your/csv/file.csv');
$csv->setOffset(1); //because we don't want to insert the header
$nbInsert = $csv->each(function ($row) use (&$sth)) {
	//Do not forget to validate your data before inserting it in your database
	$sth->bindValue(':firstname', $row[0], PDO::PARAM_STR);
	$sth->bindValue(':lastname', $row[1], PDO::PARAM_STR);
	$sth->bindValue(':email', $row[2], PDO::PARAM_STR);

	return $sth->execute(); //if the function return false then the iteration will stop
});
~~~

## More Examples

* [Selecting specific rows in the CSV](https://github.com/thephpleague/csv/blob/master/examples/extract.php)
* [Filtering a CSV](https://github.com/thephpleague/csv/blob/master/examples/filtering.php)
* [Creating a CSV](https://github.com/thephpleague/csv/blob/master/examples/writing.php)
* [Merging 2 CSV documents](https://github.com/thephpleague/csv/blob/master/examples/merge.php)
* [Switching between modes from Writer to Reader mode](https://github.com/thephpleague/csv/blob/master/examples/switchmode.php)
* [Downloading the CSV](https://github.com/thephpleague/csv/blob/master/examples/download.php)
* [Converting the CSV into a Json String](https://github.com/thephpleague/csv/blob/master/examples/json.php)
* [Converting the CSV into a XML file](https://github.com/thephpleague/csv/blob/master/examples/xml.php)
* [Converting the CSV into a HTML Table](https://github.com/thephpleague/csv/blob/master/examples/table.php)

> The CSV data use for the examples are taken from [Paris Opendata](http://opendata.paris.fr/opendata/jsp/site/Portal.jsp?document_id=60&portlet_id=121)

Learn more about how this all works in the [Overview](/overview).