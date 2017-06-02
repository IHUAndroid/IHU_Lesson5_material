# IHU_Lesson5_material

In this lesson we will analyse the DB functionality of Android, and especially the SQLite.

Our codebase will change a lot, as we will add classes that are related with the DB. 

- At first we'll define the tables of the DB
- Second, we'll write code to create these tables through java
- Next, we'll make test insertions and selections on the DB

We will do all these through Testing files for easier code management


New modules are depicted here:

![](https://github.com/UomMobileDevelopment/Lesson06-material/blob/master/sunshine-db-overview.png)


## Data Storage in Android

For data storage, Android gives us 2 ways:

1. **File Storage**  
2. **SQLite** which is a mini DB

Of course the second solution (DB) is more complicated, but it provides better data management and faster retrieval

![](https://github.com/UomMobileDevelopment/Lesson06-material/blob/master/sql-demo.PNG)


We can use SQLite by writing simple java and create tables, read and insert values. 

### Changes in Sunshine

We start by inserting the  WeatherContract 

[(DBContract)](https://developer.android.com/reference/android/provider/ContactsContract.html)


WeatherContract is actually the connection of our code base with the DB Tables. It contains a set of static final Strings, each of them describing the name of a Table and a column of the DB

![](https://github.com/UomMobileDevelopment/Lesson06-material/blob/master/db-contract.PNG)

```
public class WeatherContract {

    /*
        Inner class that defines the table contents of the location table
        Students: This is where you will add the strings.  (Similar to what has been
        done for WeatherEntry)
     */
    public static final class LocationEntry implements BaseColumns {
        public static final String TABLE_NAME = "location";

        public static final String COLUMN_LOCATION_SETTING = "location_setting";

        public static final String COLUMN_CITY_NAME = "city_name";

        public static final String COLUMN_COORD_LAT = "coord_lat";

        public static final String COLUMN_COORD_LONG = "coord_long";

    }
 }
```

![](https://github.com/UomMobileDevelopment/Lesson06-material/blob/master/sunshine-weather-wireframe.png)

We decide that the ``weather`` table should contain the following fields:

- Record ID
- Weather Condition ID 
- Min Temp
- Max Temp
- Humidity
- Date


### Understanding the Foreign Key and Referential integrity

The weather condition should also contain info about the respective location

![](https://github.com/UomMobileDevelopment/Lesson06-material/blob/master/weather-location-key-foreign-key.PNG)

__Info: Understanding Referential  Integrity__

### Explaining classes of data package (Weather Contract, WeatherDbHelper) 

Why do we need these classes? 

### Tests and Unit testing in general

Because it is difficult to test the DB functionality in an Android App mobile interface, it is considered better practice to do this testing through Unit Testing Classes

With Unit Tests we run test code in order to validate the expected app behaviour. 

The validation happens by using the method assert().

For example:

```
public void testThatDemonstratesAssertions() throws Throwable {

        int a = 5;
        int b = 3;
        int c = 5;
        int d = 10;

        assertEquals("X should be equal", a, c);
        assertTrue("Y should be true", d > a);
        assertFalse("Z should be false", a == b);

        if (b > d) {
            fail("XX should never happen");
        }
    }

```

Read more at [Tutorials Point](https://www.tutorialspoint.com/software_testing_dictionary/unit_testing.htm)

We will implement 2 Test classes: TestPractice και FullTestSuite


### Exersice 1. Create Sunshine DB table  ```Location```

Hint:
* At class ```TestDb``` remove comments from the method: testCreateDb() 
and in class ```TestUtilities``` methods ```createNorthPoleLocationValues() and insertNorthPoleLocationValues()``` *


### Exersice 2. Insert and read the Location and weather data from the Tables Location and Weather respectively

class TestDb method testLocationTable 

```
public void testLocationTable() {

        // First step: Get reference to writable database
        // If there's an error in those massive SQL table creation Strings,
        // errors will be thrown here when you try to get a writable database.
        WeatherDbHelper dbHelper = new WeatherDbHelper(mContext);
        SQLiteDatabase db = dbHelper.getWritableDatabase();

        // Second Step: Create ContentValues of what you want to insert
        // (you can use the createNorthPoleLocationValues if you wish)
        ContentValues testValues = TestUtilities.createNorthPoleLocationValues();

        // Third Step: Insert ContentValues into database and get a row ID back
        long locationRowId;
        locationRowId = db.insert(WeatherContract.LocationEntry.TABLE_NAME, null, testValues);

        // Verify we got a row back.
        assertTrue(locationRowId != -1);

        // Data's inserted.  IN THEORY.  Now pull some out to stare at it and verify it made
        // the round trip.

        // Fourth Step: Query the database and receive a Cursor back
        // A cursor is your primary interface to the query results.
        Cursor cursor = db.query(
                WeatherContract.LocationEntry.TABLE_NAME,  // Table to Query
                null, // all columns
                null, // Columns for the "where" clause
                null, // Values for the "where" clause
                null, // columns to group by
                null, // columns to filter by row groups
                null // sort order
        );

        // Move the cursor to a valid database row and check to see if we got any records back
        // from the query
        assertTrue( "Error: No Records returned from location query", cursor.moveToFirst() );

        // Fifth Step: Validate data in resulting Cursor with the original ContentValues
        // (you can use the validateCurrentRecord function in TestUtilities to validate the
        // query if you like)
        TestUtilities.validateCurrentRecord("Error: Location Query Validation Failed",
                cursor, testValues);

        // Move the cursor to demonstrate that there is only one record in the database
        assertFalse( "Error: More than one record returned from location query",
                cursor.moveToNext() );

        // Sixth Step: Close Cursor and Database
        cursor.close();
        db.close();
    }
```

method testWeatherTable() --->>> Homework !!!

```
public void testWeatherTable() {
        // First insert the location, and then use the locationRowId to insert
        // the weather. Make sure to cover as many failure cases as you can.

        // Instead of rewriting all of the code we've already written in testLocationTable
        // we can move this code to insertLocation and then call insertLocation from both
        // tests. Why move it? We need the code to return the ID of the inserted location
        // and our testLocationTable can only return void because it's a test.

        // First step: Get reference to writable database

        // Create ContentValues of what you want to insert
        // (you can use the createWeatherValues TestUtilities function if you wish)

        // Insert ContentValues into database and get a row ID back

        // Query the database and receive a Cursor back

        // Move the cursor to a valid database row

        // Validate data in resulting Cursor with the original ContentValues
        // (you can use the validateCurrentRecord function in TestUtilities to validate the
        // query if you like)

        // Finally, close the cursor and database
    }
```
