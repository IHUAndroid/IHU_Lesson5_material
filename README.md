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




Using CursorAdapter
The Plan
 
As mentioned in the previous node, we’re going to be adding a CursorAdapter to Sunshine called ForecastAdapter. Why? Well, our loader will be making the calls on our content provider to get a Cursorand we’ll want to take the data from that cursor and put it into our UI. This is what adapters are meant for, associating data with UI components.
For now, we’re not going to worry about the Loader and we're just going to change our code to use a more appropriate adapter. Right now, you might recall from lesson 1, we’re using an ArrayAdapter. This ArrayAdapter is populated only when we’re syncing with OpenWeatherMapAPI. Basically we get the JSON, put it in the content provider, take it out again, and change it to an array. This is not ideal. Let’s fix all of this.
We’ll talk more about making an adapter in Lesson 5, for now, we’re going to give you the code for one and do a quick overview.
Go ahead and copy ForecastAdapter and Utility.java into the main package (example.android.com) of your code.
What’s going inside ForecastAdapter
ForecastAdapter is a subclass of CursorAdapter. Let’s take a quick look at the functionality we’ve got in here. After the constructor, there are four methods. The first two are formatHighLows and convertCursorRowToUXFormat. These are two formatting methods specific to Sunshine.
•	convertCursorRowToUXFormat takes a row from a cursor and constructs a single string of the format:
Date - Weather -- High/Low
This is the string we’re used to seeing in the listview element. It uses formatHighLow to get the correct string for the temperature.
The other two methods are necessary to override whenever you’re extending a cursor adapter.
•	newView - Remember that adapters work with listviews to populate them. They create duplicates of the same layout to put into the list view. This is where you return what layout is going to be duplicated.
•	View view = LayoutInflater.from(context).inflate(R.layout.list_item_forecast, parent, false);
•	return view;
In our case, we’re inflating our listview layout, list_item_forecast, and then returning it.
•	bindView - This is where the exciting bit occurs. As the name suggests you are binding the values in the cursor to the view.
•	TextView tv = (TextView)view;
•	tv.setText(convertCursorRowToUXFormat(cursor));
The View passed into bindView is the View returned from newView. We know it’s a TextView, so we cast it. Then we take the Cursor, run it through our custom made formatting function, and set the text of the TextView.
Refactoring to use ForecastAdapter with Cursors and from the Fragment
1. Change mForecastAdapter's type
Change mForecastAdapter, to be an instance of ForecastAdapter.
2. Get Data from the Database
Let’s go to where we first need to populate the ForecastFragment with data and do so by getting the data from the database. Go to onCreateView. Use WeatherProvider to query the database the same way you are in FetchWeatherTask:
     String locationSetting = Utility.getPreferredLocation(getActivity());
        // Sort order:  Ascending, by date.
        String sortOrder = WeatherContract.WeatherEntry.COLUMN_DATE + " ASC";
        Uri weatherForLocationUri = WeatherContract.WeatherEntry.buildWeatherLocationWithStartDate(
                locationSetting, System.currentTimeMillis());

        Cursor cur = getActivity().getContentResolver().query(weatherForLocationUri,
                null, null, null, sortOrder);
3. Make a new ForecastAdapter
Still in onCreateView, we have a Cursor cur, so let’s use our new ForecastAdapter. Create a new ForecastAdapter with the new cursor. The list will be empty the first time we run.
mForecastAdapter = new ForecastAdapter(getActivity(), cur, 0);
4. Delete OnItemClickListener
Because we changed the adapter, the OnItemClickListener in ForecastFragment for the ListView won’t work. Specifically this line String forecast = mForecastAdapter.getItem(position); is problematic because getItem with a CursorAdapter doesn’t return a string.
Go ahead and remove or comment out this for now.
We’ll talk more about this and correct this soon enough. Until then, our code will compile and run but not have access to our DetailView.
5. Clean up
Inside of FetchWeatherTask, we’re going to remove the formatting code and anything for updating the adapter. You can remove:
•	Any reference to mForecastAdapter
•	getReadableDateString, formatHighLows, convertContentValuesToUXFormat. These are all formatting functions and we’ve moved them to the ForecastAdapter.
•	The lines in getWeatherDataFromJson where we requery the database after the insert.
•	PostExecute
Note: To keep your tests working, you'll need to modify line 42 of TestFetchWeatherTask to be:
FetchWeatherTask fwt = new FetchWeatherTask(getContext());
Use of Loaders in Forecast Fragment

public class ForecastFragment extends Fragment implements LoaderManager.LoaderCallbacks<Cursor> {
    private static final int FORECAST_LOADER = 0;
    private ForecastAdapter mForecastAdapter;
    @Override
    public void onActivityCreated(Bundle savedInstanceState) {
        getLoaderManager().initLoader(FORECAST_LOADER, null, this);
        super.onActivityCreated(savedInstanceState);
    }

    private void updateWeather() {
        FetchWeatherTask weatherTask = new FetchWeatherTask(getActivity());
        String location = Utility.getPreferredLocation(getActivity());
        weatherTask.execute(location);
    }

    @Override
    public void onStart() {
        super.onStart();
        updateWeather();
    }

    @Override
    public Loader<Cursor> onCreateLoader(int i, Bundle bundle) {
        String locationSetting = Utility.getPreferredLocation(getActivity());

        // Sort order:  Ascending, by date.
        String sortOrder = WeatherContract.WeatherEntry.COLUMN_DATE + " ASC";
        Uri weatherForLocationUri = WeatherContract.WeatherEntry.buildWeatherLocationWithStartDate(
                locationSetting, System.currentTimeMillis());
        return new CursorLoader(getActivity(),
                weatherForLocationUri,                null,                null,                null,                sortOrder);
    }

    @Override
    public void onLoadFinished(Loader<Cursor> cursorLoader, Cursor cursor) {
        mForecastAdapter.swapCursor(cursor);
    }

    @Override
    public void onLoaderReset(Loader<Cursor> cursorLoader) {
        mForecastAdapter.swapCursor(null);
    }
 
Projections
private static final String[] FORECAST_COLUMNS = {
            // In this case the id needs to be fully qualified with a table name, since
            // the content provider joins the location & weather tables in the background
            // (both have an _id column)
            // On the one hand, that's annoying.  On the other, you can search the weather table
            // using the location set by the user, which is only in the Location table.
            // So the convenience is worth it.
            WeatherContract.WeatherEntry.TABLE_NAME + "." + WeatherContract.WeatherEntry._ID,
            WeatherContract.WeatherEntry.COLUMN_DATE,
            WeatherContract.WeatherEntry.COLUMN_SHORT_DESC,
            WeatherContract.WeatherEntry.COLUMN_MAX_TEMP,
            WeatherContract.WeatherEntry.COLUMN_MIN_TEMP,
            WeatherContract.LocationEntry.COLUMN_LOCATION_SETTING,
            WeatherContract.WeatherEntry.COLUMN_WEATHER_ID,
            WeatherContract.LocationEntry.COLUMN_COORD_LAT,
            WeatherContract.LocationEntry.COLUMN_COORD_LONG
    };

    // These indices are tied to FORECAST_COLUMNS.  If FORECAST_COLUMNS changes, these
    // must change.
    static final int COL_WEATHER_ID = 0;
    static final int COL_WEATHER_DATE = 1;
    static final int COL_WEATHER_DESC = 2;
    static final int COL_WEATHER_MAX_TEMP = 3;
    static final int COL_WEATHER_MIN_TEMP = 4;
    static final int COL_LOCATION_SETTING = 5;
    static final int COL_WEATHER_CONDITION_ID = 6;
    static final int COL_COORD_LAT = 7;
    static final int COL_COORD_LONG = 8;



private String convertCursorRowToUXFormat(Cursor cursor) {
        String highAndLow = formatHighLows(
                cursor.getDouble(ForecastFragment.COL_WEATHER_MAX_TEMP),
                cursor.getDouble(ForecastFragment.COL_WEATHER_MIN_TEMP));

        return Utility.formatDate(cursor.getLong(ForecastFragment.COL_WEATHER_DATE)) +
                " - " + cursor.getString(ForecastFragment.COL_WEATHER_DESC) +
                " - " + highAndLow;
    }
 
Make Details View Functional
One of the things that we decided to temporarily break is the details view. It’s time to fix this and hook things up.
The major change we will make here is that we will start our DetailsActivity by passing it the URI it needs to pass to the content provider to get the correct data.
1. Add OnItemClickListener to ListView
In ForecastFragment, in the onCreateView method, go ahead and add an onItemClickListener, except this time, it’s going to pass a URI for the data needed for the detail view.
        listView.setOnItemClickListener(new AdapterView.OnItemClickListener() {

            @Override
            public void onItemClick(AdapterView adapterView, View view, int position, long l) {
                // CursorAdapter returns a cursor at the correct position for getItem(), or null
                // if it cannot seek to that position.
                Cursor cursor = (Cursor) adapterView.getItemAtPosition(position);
                if (cursor != null) {
                    String locationSetting = Utility.getPreferredLocation(getActivity());
                    Intent intent = new Intent(getActivity(), DetailActivity.class)
                            .setData(WeatherContract.WeatherEntry.buildWeatherLocationWithDate(
                                    locationSetting, cursor.getLong(COL_WEATHER_DATE)
                            ));
                    startActivity(intent);
                }
            }
        });
2. Print the URI in the ListView
On the DetailActivity side, we'll want to change the code, which is referring to an intent extra that you're no longer setting. Instead we used setData so we need to grab this data using getDataString. The full code you'll need to put in DetailActivity is:

       if (intent != null) {
           mForecastStr = intent.getDataString();
       }

This causes the detail view to show the URI.
But wait, that's not what we want! So obviously we’re not done at this point, we need to actually use the URI to display the correct data in the detail view. You’ll be doing that in the next node.
Check out the full diff here.
 
Implement Details View as Cursor Loader (+use Projections)

public static class DetailFragment extends Fragment implements LoaderCallbacks<Cursor> {
        private static final String LOG_TAG = DetailFragment.class.getSimpleName();
        private static final String FORECAST_SHARE_HASHTAG = " #SunshineApp";
        private ShareActionProvider mShareActionProvider;
        private String mForecast;
        private static final int DETAIL_LOADER = 0;


        private static final String[] FORECAST_COLUMNS = {
                WeatherEntry.TABLE_NAME + "." + WeatherEntry._ID,
                WeatherEntry.COLUMN_DATE,
                WeatherEntry.COLUMN_SHORT_DESC,
                WeatherEntry.COLUMN_MAX_TEMP,
                WeatherEntry.COLUMN_MIN_TEMP,
        };
        // these constants correspond to the projection defined above, and must change if the
        // projection changes
        private static final int COL_WEATHER_ID = 0;
        private static final int COL_WEATHER_DATE = 1;
        private static final int COL_WEATHER_DESC = 2;
        private static final int COL_WEATHER_MAX_TEMP = 3;
        private static final int COL_WEATHER_MIN_TEMP = 4;


        public DetailFragment() {
            setHasOptionsMenu(true);
        }
        @Override
        public View onCreateView(LayoutInflater inflater, ViewGroup container,
                                 Bundle savedInstanceState) {
            return inflater.inflate(R.layout.fragment_detail, container, false);
        }
        @Override
        public void onCreateOptionsMenu(Menu menu, MenuInflater inflater) {
            // Inflate the menu; this adds items to the action bar if it is present.
            inflater.inflate(R.menu.detailfragment, menu);
            // Retrieve the share menu item
            MenuItem menuItem = menu.findItem(R.id.action_share);


            // Get the provider and hold onto it to set/change the share intent.
            mShareActionProvider = (ShareActionProvider) MenuItemCompat.getActionProvider(menuItem);
            // If onLoadFinished happens before this, we can go ahead and set the share intent now.
            if (mForecast != null) {
                mShareActionProvider.setShareIntent(createShareForecastIntent());
            }
        }
        private Intent createShareForecastIntent() {
            Intent shareIntent = new Intent(Intent.ACTION_SEND);
            shareIntent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_WHEN_TASK_RESET);
            shareIntent.setType("text/plain");
            shareIntent.putExtra(Intent.EXTRA_TEXT, mForecast + FORECAST_SHARE_HASHTAG);
            return shareIntent;
        }
        @Override
        public void onActivityCreated(Bundle savedInstanceState) {
            getLoaderManager().initLoader(DETAIL_LOADER, null, this);
            super.onActivityCreated(savedInstanceState);
        }
        @Override
        public Loader<Cursor> onCreateLoader(int id, Bundle args) {
            Log.v(LOG_TAG, "In onCreateLoader");
            Intent intent = getActivity().getIntent();
            if (intent == null) {
                return null;
            }
            // Now create and return a CursorLoader that will take care of
            // creating a Cursor for the data being displayed.
            return new CursorLoader(
                    getActivity(),
                    intent.getData(),
                    FORECAST_COLUMNS, null,   null,      null);

            );
        }
        @Override
        public void onLoadFinished(Loader<Cursor> loader, Cursor data) {
            Log.v(LOG_TAG, "In onLoadFinished");
            if (!data.moveToFirst()) { return; }
            String dateString = Utility.formatDate(
                    data.getLong(COL_WEATHER_DATE));
            String weatherDescription =
                    data.getString(COL_WEATHER_DESC);
            boolean isMetric = Utility.isMetric(getActivity());
            String high = Utility.formatTemperature(
                    data.getDouble(COL_WEATHER_MAX_TEMP), isMetric);
            String low = Utility.formatTemperature(
                    data.getDouble(COL_WEATHER_MIN_TEMP), isMetric);
            mForecast = String.format("%s - %s - %s/%s", dateString, weatherDescription, high, low);
            TextView detailTextView = (TextView)getView().findViewById(R.id.detail_text);
            detailTextView.setText(mForecast);
            // If onCreateOptionsMenu has already happened, we need to update the share intent now.
            if (mShareActionProvider != null) {
                mShareActionProvider.setShareIntent(createShareForecastIntent());
            }
        }
        @Override
        public void onLoaderReset(Loader<Cursor> loader) { }
    }



 
Handle the Setting Change
One more thing. Right now we're no longer responding correctly when a setting is changed. For example, if you were to update the location setting from Mountain View California, to say, Rochester New York, Sunshine won't work. Why? Well, it would look for the data in the database, but you don't have that data for a new location. Let's fix this.
1. Create mLocation
In MainActivity create a mLocation variable to store our current known location.
Initialize this variable in onCreate to be whatever is currently stored in settings.
2. Add a Fragment Tag
Add a constant for a fragment tag called FORECASTFRAGMENT_TAG. A fragment tag is a constant String we can use to tag a fragment within the fragment manager so we can easily look it up later.
When we add the fragment, we add the parameter for the FORECASTFRAGMENT_TAG. Fragments can only be tagged during the fragment transaction. This means that the same fragment code can be reused multiple times with different tags.
     if (savedInstanceState == null) {
         getSupportFragmentManager().beginTransaction()
                 .add(R.id.container, new ForecastFragment(), FORECASTFRAGMENT_TAG)
                 .commit();
     }
3. Create onLocationChanged method
In our ForecastFragment, create onLocationChanged. This method will do two things, it will first call updateWeather and then it will restart the loader.
4. Check for Location Change
Now in onResume in MainActivity, check whether the location has changed by comparing whatever is stored in the settings (ie Utility.getPreferredLocation(this);) with mLocation. If it has changed:
1.	Get the ForecastFragment using the tag FORECASTFRAGMENT_TAG by using:
ForecastFragment ff = 
(ForecastFragment)getSupportFragmentManager().findFragmentByTag(FORECASTFRAGMENT_TAG);
2.	Call onLocationChanged
3.	Update mLocation
5. Remove Excessive Weather Fetching
Now that we have a database, we don’t have to constantly talk to the network and fetch the weather. But if you look at onStart from ForecastFragment, you'll see every time it's called, it downloads data from Open Weather Map. This means every time you rotate the device, you'll be attempting to connect to Open Weather Map. In Lesson 6 we’ll show you how to schedule updates in the background, but for now let’s save on network bandwidth and battery by deleting onStart. You can use the “refresh” menu item to get new weather data.



