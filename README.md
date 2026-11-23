
# Ex.No:1 To create a employee details fields and to display the employee details using Firebase Database in Android Studio.


## AIM:

To create and display the employee details using Firebase Database in Android Studio.

## EQUIPMENTS REQUIRED:

Android Studio(Min.required Artic Fox)

## ALGORITHM:

Step 1: Open Android Stdio and then click on File -> New -> New project.

Step 2: Then type the Application name as HelloWorld and click Next. 

Step 3: Then select the Minimum SDK as shown below and click Next.

Step 4: Then select the Empty Activity and click Next. Finally click Finish.

Step 5: Design layout in activity_main.xml.

Step 6: Display the employee details in MainActivity file.

Step 7: Save and run the application.

## PROGRAM:
```
/*
Program to print the DatabaseTable using the firebasedatabase”.
Developed by: KARTHIK G
Registeration Number : 212223220043

*/
```
## Main Activity 
```
package com.example.firebase;

import android.os.Bundle;
import android.text.TextUtils;
import android.view.View;
import android.widget.TextView;
import android.widget.Toast;

import androidx.activity.EdgeToEdge;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.graphics.Insets;
import androidx.core.view.ViewCompat;
import androidx.core.view.WindowInsetsCompat;
import androidx.recyclerview.widget.LinearLayoutManager;
import androidx.recyclerview.widget.RecyclerView;

import com.google.android.material.button.MaterialButton;
import com.google.android.material.textfield.TextInputEditText;

import java.util.ArrayList;
import java.util.List;

public class MainActivity extends AppCompatActivity {

    private TextInputEditText etName, etAge, etSalary;
    private MaterialButton btnAdd, btnClearAll;
    private RecyclerView rvRecords;
    private TextView tvEmptyState;

    private DatabaseHelper dbHelper;
    private RecordAdapter adapter;
    private List<UserRecord> recordList;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        EdgeToEdge.enable(this);
        setContentView(R.layout.activity_main);

        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main), (v, insets) -> {
            Insets systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars());
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom);
            return insets;
        });

        // Initialize Database Helper
        dbHelper = new DatabaseHelper(this);

        // Bind UI Elements
        etName = findViewById(R.id.etName);
        etAge = findViewById(R.id.etAge);
        etSalary = findViewById(R.id.etSalary);
        btnAdd = findViewById(R.id.btnAdd);
        btnClearAll = findViewById(R.id.btnClearAll);
        rvRecords = findViewById(R.id.rvRecords);
        tvEmptyState = findViewById(R.id.tvEmptyState);

        // Setup RecyclerView & Adapter
        recordList = new ArrayList<>();
        adapter = new RecordAdapter(recordList, record -> {
            boolean deleted = dbHelper.deleteRecord(record.getId());
            if (deleted) {
                Toast.makeText(MainActivity.this, "Record deleted", Toast.LENGTH_SHORT).show();
                loadDatabaseRecords();
            } else {
                Toast.makeText(MainActivity.this, "Failed to delete record", Toast.LENGTH_SHORT).show();
            }
        });

        rvRecords.setLayoutManager(new LinearLayoutManager(this));
        rvRecords.setAdapter(adapter);

        // Add Button Listener
        btnAdd.setOnClickListener(v -> addRecordEntry());

        // Clear All Button Listener
        btnClearAll.setOnClickListener(v -> {
            if (recordList.isEmpty()) {
                Toast.makeText(MainActivity.this, "Database is already empty", Toast.LENGTH_SHORT).show();
                return;
            }
            dbHelper.clearAllRecords();
            Toast.makeText(MainActivity.this, "All records cleared", Toast.LENGTH_SHORT).show();
            loadDatabaseRecords();
        });

        // Load records from DB
        loadDatabaseRecords();
    }

    private void addRecordEntry() {
        String name = etName.getText() != null ? etName.getText().toString().trim() : "";
        String ageStr = etAge.getText() != null ? etAge.getText().toString().trim() : "";
        String salaryStr = etSalary.getText() != null ? etSalary.getText().toString().trim() : "";

        if (TextUtils.isEmpty(name)) {
            etName.setError("Please enter full name");
            etName.requestFocus();
            return;
        }

        if (TextUtils.isEmpty(ageStr)) {
            etAge.setError("Please enter age");
            etAge.requestFocus();
            return;
        }

        int age;
        try {
            age = Integer.parseInt(ageStr);
            if (age <= 0 || age > 120) {
                etAge.setError("Please enter a valid age");
                etAge.requestFocus();
                return;
            }
        } catch (NumberFormatException e) {
            etAge.setError("Invalid age format");
            etAge.requestFocus();
            return;
        }

        if (TextUtils.isEmpty(salaryStr)) {
            etSalary.setError("Please enter salary");
            etSalary.requestFocus();
            return;
        }

        double salary;
        try {
            salary = Double.parseDouble(salaryStr);
            if (salary < 0) {
                etSalary.setError("Salary cannot be negative");
                etSalary.requestFocus();
                return;
            }
        } catch (NumberFormatException e) {
            etSalary.setError("Invalid salary format");
            etSalary.requestFocus();
            return;
        }

        boolean success = dbHelper.addRecord(name, age, salary);
        if (success) {
            Toast.makeText(this, "Record saved to database!", Toast.LENGTH_SHORT).show();
            etName.setText("");
            etAge.setText("");
            etSalary.setText("");
            etName.clearFocus();
            etAge.clearFocus();
            etSalary.clearFocus();
            loadDatabaseRecords();
        } else {
            Toast.makeText(this, "Error saving record", Toast.LENGTH_SHORT).show();
        }
    }

    private void loadDatabaseRecords() {
        recordList = dbHelper.getAllRecords();
        adapter.updateData(recordList);

        if (recordList.isEmpty()) {
            tvEmptyState.setVisibility(View.VISIBLE);
            rvRecords.setVisibility(View.GONE);
        } else {
            tvEmptyState.setVisibility(View.GONE);
            rvRecords.setVisibility(View.VISIBLE);
        }
    }
}
```
## Activity_XML
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="16dp">

    <EditText
        android:id="@+id/etName"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Name" />

    <EditText
        android:id="@+id/etAge"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Age"
        android:inputType="number" />

    <EditText
        android:id="@+id/etSalary"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Salary"
        android:inputType="number" />

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal">

        <Button
            android:id="@+id/btnAdd"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="Add" />

        <Button
            android:id="@+id/btnUpdate"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="Update" />

        <Button
            android:id="@+id/btnDelete"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="Delete" />
    </LinearLayout>

    <ListView
        android:id="@+id/listView"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

</LinearLayout>
```
## OUTPUT

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/963a31a8-3a8f-4897-821a-8f1a0d2c7a83" />


## RESULT
Thus a Simple Android Application create a firebase database and to display the employee details using Firbase Real Time Database in Android Studio is developed and executed successfully.
