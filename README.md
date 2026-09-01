
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
Developed by:sreenithi
Registeration Number : 212223220109
*/
```

## EMP.JAVA:

```
package com.example.experiment1;

public class Employee {

    private String id;
    private String name;
    private String department;
    private String salary;
    private String age;

    public Employee() {
    }

    public Employee(String id, String name, String department, String salary, String age) {
        this.id = id;
        this.name = name;
        this.department = department;
        this.salary = salary;
        this.age = age;
    }

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getDepartment() {
        return department;
    }

    public void setDepartment(String department) {
        this.department = department;
    }

    public String getSalary() {
        return salary;
    }

    public void setSalary(String salary) {
        this.salary = salary;
    }

    public String getAge() {
        return age;
    }

    public void setAge(String age) {
        this.age = age;
    }
}
```
## MAINACTIVITY:

```
package com.example.experiment1;

import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

import androidx.annotation.NonNull;
import androidx.appcompat.app.AppCompatActivity;
import androidx.recyclerview.widget.LinearLayoutManager;
import androidx.recyclerview.widget.RecyclerView;

import com.google.firebase.database.DataSnapshot;
import com.google.firebase.database.DatabaseError;
import com.google.firebase.database.DatabaseReference;
import com.google.firebase.database.FirebaseDatabase;
import com.google.firebase.database.ValueEventListener;

import java.util.ArrayList;

public class MainActivity extends AppCompatActivity {

    EditText etName, etDepartment, etSalary, etAge;
    Button btnSave, btnUpdate, btnDelete;
    RecyclerView recyclerView;

    ArrayList<Employee> employeeList;
    EmployeeAdapter adapter;

    DatabaseReference databaseReference;

    String selectedId = "";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        etName = findViewById(R.id.etName);
        etDepartment = findViewById(R.id.etDepartment);
        etSalary = findViewById(R.id.etSalary);
        etAge = findViewById(R.id.etAge);

        btnSave = findViewById(R.id.btnSave);
        btnUpdate = findViewById(R.id.btnUpdate);
        btnDelete = findViewById(R.id.btnDelete);

        recyclerView = findViewById(R.id.recyclerView);
        recyclerView.setLayoutManager(new LinearLayoutManager(this));

        databaseReference = FirebaseDatabase.getInstance().getReference("Employees");

        employeeList = new ArrayList<>();

        adapter = new EmployeeAdapter(employeeList, new EmployeeAdapter.OnEmployeeClickListener() {
            @Override
            public void onEmployeeClick(Employee employee) {

                selectedId = employee.getId();

                etName.setText(employee.getName());
                etDepartment.setText(employee.getDepartment());
                etSalary.setText(employee.getSalary());
                etAge.setText(employee.getAge());
            }
        });

        recyclerView.setAdapter(adapter);

        loadEmployees();

        btnSave.setOnClickListener(v -> saveEmployee());

        btnUpdate.setOnClickListener(v -> updateEmployee());

        btnDelete.setOnClickListener(v -> deleteEmployee());
    }

    private void saveEmployee() {

        String id = databaseReference.push().getKey();

        Employee employee = new Employee(
                id,
                etName.getText().toString().trim(),
                etDepartment.getText().toString().trim(),
                etSalary.getText().toString().trim(),
                etAge.getText().toString().trim()
        );

        databaseReference.child(id).setValue(employee);

        Toast.makeText(this, "Employee Saved", Toast.LENGTH_SHORT).show();

        clearFields();
    }

    private void loadEmployees() {

        databaseReference.addValueEventListener(new ValueEventListener() {
            @Override
            public void onDataChange(@NonNull DataSnapshot snapshot) {

                employeeList.clear();

                for (DataSnapshot ds : snapshot.getChildren()) {

                    Employee employee = ds.getValue(Employee.class);

                    if (employee != null) {
                        employeeList.add(employee);
                    }
                }

                adapter.notifyDataSetChanged();
            }

            @Override
            public void onCancelled(@NonNull DatabaseError error) {

                Toast.makeText(MainActivity.this,
                        error.getMessage(),
                        Toast.LENGTH_SHORT).show();
            }
        });
    }

    private void updateEmployee() {

        if (selectedId.isEmpty()) {

            Toast.makeText(this,
                    "Please select an employee",
                    Toast.LENGTH_SHORT).show();
            return;
        }

        Employee employee = new Employee(
                selectedId,
                etName.getText().toString().trim(),
                etDepartment.getText().toString().trim(),
                etSalary.getText().toString().trim(),
                etAge.getText().toString().trim()
        );

        databaseReference.child(selectedId).setValue(employee);

        Toast.makeText(this,
                "Employee Updated",
                Toast.LENGTH_SHORT).show();

        clearFields();
    }

    private void deleteEmployee() {

        if (selectedId.isEmpty()) {

            Toast.makeText(this,
                    "Please select an employee",
                    Toast.LENGTH_SHORT).show();
            return;
        }

        databaseReference.child(selectedId).removeValue();

        Toast.makeText(this,
                "Employee Deleted",
                Toast.LENGTH_SHORT).show();

        clearFields();
    }

    private void clearFields() {

        etName.setText("");
        etDepartment.setText("");
        etSalary.setText("");
        etAge.setText("");

        selectedId = "";
    }
}
```

## ACTIVITY_MAIN.XML:
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="16dp"
    tools:context=".MainActivity">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Employee Management"
        android:textSize="24sp"
        android:textStyle="bold"
        android:layout_gravity="center"
        android:layout_marginBottom="16dp"/>

    <EditText
        android:id="@+id/etName"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Employee Name"
        android:inputType="textPersonName"/>

    <EditText
        android:id="@+id/etDepartment"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Department"
        android:inputType="text"/>

    <EditText
        android:id="@+id/etSalary"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Salary"
        android:inputType="numberDecimal"/>

    <EditText
        android:id="@+id/etAge"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Age"
        android:inputType="number"/>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:layout_marginTop="12dp">

        <Button
            android:id="@+id/btnSave"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="Save"/>

        <Button
            android:id="@+id/btnUpdate"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="Update"/>

        <Button
            android:id="@+id/btnDelete"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="Delete"/>
    </LinearLayout>

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"
        android:layout_marginTop="16dp"/>

</LinearLayout>
```

## OUTPUT

<img width="1920" height="1080" alt="Screenshot (672)" src="https://github.com/user-attachments/assets/4e513f47-19a9-4bb6-98a1-0846fb42e896" />

<img width="1920" height="1080" alt="Screenshot (681)" src="https://github.com/user-attachments/assets/ce410a43-a0b1-44b2-a930-c26ef4f397fa" />

<img width="1920" height="1080" alt="Screenshot (683)" src="https://github.com/user-attachments/assets/31e8d1a4-72e7-4e8d-9425-f991a58dbc5c" />


## RESULT
Thus a Simple Android Application create a firebase database and to display the employee details using Firbase Real Time Database in Android Studio is developed and executed successfully.
