---
title: "Android: Select date with DatePickerDialog"
date: 2017-08-14T23:05:47+02:00
draft: true
---
With androids date picker you can easily choose a date without much code.

# Guide How To
1.Add latest appcompat library to your <code>build.gradle</code>.
~~~java
dependencies {
        ...
	compile 'com.android.support:appcompat-v7:26.+' // set exact version here
}
~~~

2.Make sure, that your activity extends <code>android.support.v7.app.AppCompatActivity</code>.
~~~java
public class MainActivity extends AppCompatActivity { ... }
~~~

3.Create your <code>DatePickerDialog</code> and set a context, a listener and the start year, month and day.
~~~java
DatePickerDialog datePickerDialog = new DatePickerDialog(
                        context, 
                        listener, 
                        startYear, 
                        starthMonth, 
                        startDay);
~~~

4.Optional: Advanced options for your date picker
~~~java
DatePicker datePicker = datePickerDialog.getDatePicker();

//set minimum date to select
datePicker.setMinDate(long minDate);

//set maximum date to select
datePicker.setMaxDate(long maxDate);

//set the first day of a week
datePicker.setFirstDayOfWeek(int firstDayOfWeek)

//update the current date
datePicker.updateDate(int year, int month, int day);
~~~

5.Show your date picker using the <code>show()</code> method.
~~~java
datePickerDialog.show()
~~~

# Code Example
~~~java
//create an datePickerListener to get the selected date. Needs to be done, before creating the date picker
private DatePickerDialog.OnDateSetListener dateListener = new DatePickerDialog.OnDateSetListener() {
	@Override
	public void onDateSet(DatePicker view, int Year, int Month, int Day) {

	    //Year is now the selected year
	    //Month + 1 is the selected month. Note: add 1, because months starts from 0 in android
	    //Day is the selected day

            Toast.makeText(MainYctivity.this, "Selected date: " + Year + "-" + (Month + 1) + "-" + Day, Toast.LENGTH_SHORT).show();
            
	}
};

//init calendar to get the current year, month and day
Calendar cal = Calendar.getInstance();

//creating the DatePickerDialog
DatePickerDialog dialog = new DatePickerDialog(MainActivity.this, 
                        dateListener, 	//set the listener
                        cal.get(Calendar.YEAR), 
                        cal.get(Calendar.MONTH), 
                        cal.get(Calendar.DAY_OF_MONTH));

//optional set starting day (from today)
dialog.getDatePicker().setMinDate(new Date().getTime());

//show picker
dialog.show();
~~~