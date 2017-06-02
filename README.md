# IHU_Lesson5_material

In this lesson we will analyse the DB functionality of Android, and especially the SQLite.

Our codebase will change a lot, as we will add classes that are related with the DB. 

- At first we'll define the tables of the DB
- Second, we'll write code to create these tables through java
- Next, we'll make test insertions and selections on the DB

We will do all these through Testing files for easier code management


New modules are depicted here:

![](https://github.com/UomMobileDevelopment/Lesson06-material/blob/master/sunshine-db-overview.png)


## Data Storage in Android - Αποθήκευση δεδομένων

Για την αποθήκευση δεδομένων το Android μας παρέχει 2 βασικούς πυλώνες:

1. Το σύστημα αρχείων (File Storage) πχ εκεί αποθηκεύονται τα Shared Preferences 
2. Μια μίνι-έκδοση βάσης δεδομένων που λέγεται SQLite

Φυσικά το 2ο είναι πολύ πιο πολύπλοκο στην υλοποίηση, αλλά παρέχει σαφή και αποδοτική οργάνωση δεδομένων (+γρηγορη αναζήτηση)

![](https://github.com/UomMobileDevelopment/Lesson06-material/blob/master/sql-demo.PNG)


Με τη χρήση του SQLite μέσα απο τον κώδικα μπορούμε εύκολα να δημιουργήσουμε πίνακες, να εισάγουμε και να διαγράψουμε δεδομένα. 

### Αλλαγές Sunshine

Ξεκινάμε την εισαγωγή του WeatherContract [(DBContract)](https://developer.android.com/reference/android/provider/ContactsContract.html)

Πρόκειται για τη διασύνδεση των πινάκων με τον κώδικα. Μια κλάση contract περιλαμβάνει έναν σύνολο static final Strings καθένα απο τα οποία περιγράφει το όνομα ενός πίνακα και της μιας στήλης στη ΒΔ

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

Για να αποφασίσουμε τι δεδομένα πρέπει να κρατήσουμε, μελετούμε το wireframe για την πρόβλεψη μιας ημέρας:

![](https://github.com/UomMobileDevelopment/Lesson06-material/blob/master/sunshine-weather-wireframe.png)

αποφασίζουμε λοιπόν πως το πίνακας ``weather`` πρέπει να έχει τα εξής πεδία:

- Record ID
- Weather Condition ID 
- Min Temp
- Max Temp
- Wind speed
- Humidity
- Date
- Wind direction
- Pressure

### Κατανόηση Foreign Key και έννοιας ακεραιότητας αναφορών

Ο Weather Location θα πρέπει επίσης να περιλαμβάνει και την πληροφορία της τοποθεσίας

![](https://github.com/UomMobileDevelopment/Lesson06-material/blob/master/weather-location-key-foreign-key.PNG)

__Info: Επεξήγηση ακεραιότητας αναφορών__

### Επεξήγηση κλάσεων του πακέτου data (Weather Contract, WeatherDbHelper) 

Τί χρειάζονται αυτές οι κλάσεις; 

### Tests and Unit testing in general
Επειδή η λειτουργικότητα των ΒΔ είναι δύσκολο να δοκιμαστεί σε κινητό λόγω του απαραίτητου User Interface, ο καλύτερος τρόπος είναι να γίνει μέσω Unit Testing.

Με τα Unit Tests εκτελείται δοκιμαστικά ο κώδικας με σκοπό να επαληθευτεί η σωστή του λειτουργία. Αφού εκτελεστεί ένα κομμάτι κώδικα (συνήθως το σπάμε σε μεθόδους) ελέγχεται αν η τιμή μιας μεταβλητής είναι η αναμενόμενη, αν ένας πίνακας είναι γεμάτος ή άδειος κ.ο.κ.

Οι έλεγχοι αυτοί γίνονται με βοήθεια της μεθόδου assert().

Παράδειγμα:

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

Διαβάστε περισσότερα για το Unit Testing στο [Tutorials Point](https://www.tutorialspoint.com/software_testing_dictionary/unit_testing.htm)

Οι βασικές κλάσεις στις οποίες θα υλοποιήσουμε τα δικά μας Unit tests είναι οι κλάσεις: TestPractice και FullTestSuite


### Άσκηση 1. Δημιουργία Sunshine ΒΔ πίνακα Location

Hint:
* Στην κλάση TestDb βγάζουμε απο σχόλια τη μέθοδο: testCreateDb() 
και στην κλάση TestUtilities τις μεθόδους createNorthPoleLocationValues() 
insertNorthPoleLocationValues() *


### Άσκηση 2. Εισαγωγή και ανάγνωση Location και weather data απο τους πίνακες Location και Weather αντιστοιχα

κλάση TestDb μέθοδος testLocationTable 

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

μέθοδος testWeatherTable() --->>> Ασκηση!!!

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
