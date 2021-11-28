# GRIP21 #andrioid developement #NOV21
plugins {
    id 'com.android.application'
}

android {
    compileSdkVersion 30
    buildToolsVersion "30.0.3"

    defaultConfig {
        applicationId "com.example.basicbankingapp"
        minSdkVersion 23
        targetSdkVersion 30
        versionCode 1
        versionName "1.0"

        testInstrumentationRunner "androidx.test.runner.AndroidJUnitRunner"
    }

    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
    }
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

dependencies {

    implementation 'androidx.appcompat:appcompat:1.2.0'
    implementation 'com.google.android.material:material:1.3.0'
    implementation 'androidx.constraintlayout:constraintlayout:2.0.4'
    testImplementation 'junit:junit:4.+'
    androidTestImplementation 'androidx.test.ext:junit:1.1.2'
    androidTestImplementation 'androidx.test.espresso:espresso-core:3.3.0'
}

package com.example.basicbankingapp;

import android.content.Context;

import androidx.test.platform.app.InstrumentationRegistry;
import androidx.test.ext.junit.runners.AndroidJUnit4;

import org.junit.Test;
import org.junit.runner.RunWith;

import static org.junit.Assert.*;

/**
 * Instrumented test, which will execute on an Android device.
 *
 * @see <a href="http://d.android.com/tools/testing">Testing documentation</a>
 */
@RunWith(AndroidJUnit4.class)
public class ExampleInstrumentedTest {
    @Test
    public void useAppContext() {
        // Context of the app under test.
        Context appContext = InstrumentationRegistry.getInstrumentation().getTargetContext();
        assertEquals("com.example.basicbankingapp", appContext.getPackageName());
    }
}
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.basicbankingapp">

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.BasicBankingApp">
        <activity android:name=".UI.SendToUserList"></activity>
        <activity android:name=".UI.UsersList" />
        <activity android:name=".UI.TransactionHistory" />
        <activity android:name=".UI.UserData" />
        <activity android:name=".UI.HomeScreen">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

        <meta-data
            android:name="preloaded_fonts"
            android:resource="@array/preloaded_fonts" />
    </application>

</manifest>
package com.example.basicbankingapp.DB;

import android.provider.BaseColumns;

public final class TransactionContract {
    private TransactionContract() {}

    public static final class TransactionEntry implements BaseColumns {
        /**Name of database table for pets*/
        public final static String TABLE_NAME = "Transaction_table";

        /**Table Fields*/
        public final static String _ID = BaseColumns._ID;
        public final static String COLUMN_FROM_NAME = "from_name";
        public final static String COLUMN_TO_NAME = "to_name";
        public final static String COLUMN_AMOUNT = "amount";
        public final static String COLUMN_STATUS = "status";
    }
}
package com.example.basicbankingapp.DB;

import android.content.ContentValues;
import android.content.Context;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteOpenHelper;
import com.example.basicbankingapp.DB.TransactionContract.TransactionEntry;
import com.example.basicbankingapp.Data.Transaction;

public class TransactionHelper extends SQLiteOpenHelper {
    /** Name of the database file */
    private static final String DATABASE_NAME = "transaction.db";

    /**
     * Database version. If you change the database schema, you must increment the database version.*/
    private static final int DATABASE_VERSION = 1;

    public TransactionHelper(Context context) {
        super(context, DATABASE_NAME, null, DATABASE_VERSION);
    }

    @Override
    public void onCreate(SQLiteDatabase db) {
        // Create a String that contains the SQL statement to create the pets table
        String SQL_CREATE_TRANSACTION_TABLE =  "CREATE TABLE " + TransactionEntry.TABLE_NAME + " ("
                + TransactionEntry.COLUMN_FROM_NAME + " VARCHAR, "
                + TransactionEntry.COLUMN_TO_NAME + " VARCHAR, "
                + TransactionEntry.COLUMN_AMOUNT + " INTEGER, "
                + TransactionEntry.COLUMN_STATUS + " INTEGER);";

        // Execute the SQL statement
        db.execSQL(SQL_CREATE_TRANSACTION_TABLE);
    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        if (oldVersion != newVersion) {
            // Simplest implementation is to drop all old tables and recreate them
            db.execSQL("DROP TABLE IF EXISTS " + TransactionEntry.TABLE_NAME);
            onCreate(db);
        }
    }

    public boolean insertTransferData (String fromName, String toName, String amount, int status) {
        SQLiteDatabase db = this.getWritableDatabase();
        ContentValues contentValues = new ContentValues();

        contentValues.put(TransactionEntry.COLUMN_FROM_NAME, fromName);
        contentValues.put(TransactionEntry.COLUMN_TO_NAME, toName);
        contentValues.put(TransactionEntry.COLUMN_AMOUNT, amount);
        contentValues.put(TransactionEntry.COLUMN_STATUS, status);
        Long result = db.insert(TransactionEntry.TABLE_NAME, null, contentValues);

        if (result == -1) {
            return false;
        }
        else {
            return true;
        }
    }
}
package com.example.basicbankingapp.DB;

import android.provider.BaseColumns;

public final class UserContract {
    private UserContract() {}

    public static final class UserEntry implements BaseColumns {
        /** Name of database table for pets */
        public final static String TABLE_NAME = "user";

        /** Table Fields */
        public final static String _ID = BaseColumns._ID;
        public final static String COLUMN_USER_NAME ="name";
        public final static String COLUMN_USER_ACCOUNT_NUMBER ="accountNo";
        public final static String COLUMN_USER_EMAIL ="email";
        public final static String COLUMN_USER_IFSC_CODE ="ifscCode";
        public final static String COLUMN_USER_PHONE_NO ="phoneNo";
        public final static String COLUMN_USER_ACCOUNT_BALANCE ="balance";
    }
}
package com.example.basicbankingapp.DB;

import android.content.ContentValues;
import android.content.Context;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteOpenHelper;
import android.util.Log;

import com.example.basicbankingapp.DB.UserContract.UserEntry;
import com.example.basicbankingapp.Data.User;

public class UserHelper extends SQLiteOpenHelper {

    String TABLE_NAME = UserEntry.TABLE_NAME;

    /** Name of the database file */
    private static final String DATABASE_NAME = "User.db";

    /**
     * Database version. If you change the database schema, you must increment the database version.*/
    private static final int DATABASE_VERSION = 1;

    public UserHelper(Context context) {
        super(context, DATABASE_NAME, null, DATABASE_VERSION);
    }

    @Override
    public void onCreate(SQLiteDatabase db) {
        // Create a String that contains the SQL statement to create the pets table
        String SQL_CREATE_USER_TABLE =  "CREATE TABLE " + UserEntry.TABLE_NAME + " ("
                + UserEntry.COLUMN_USER_ACCOUNT_NUMBER + " INTEGER, "
                + UserEntry.COLUMN_USER_NAME + " VARCHAR, "
                + UserEntry.COLUMN_USER_EMAIL + " VARCHAR, "
                + UserEntry.COLUMN_USER_IFSC_CODE + " VARCHAR, "
                + UserEntry.COLUMN_USER_PHONE_NO + " VARCHAR, "
                + UserEntry.COLUMN_USER_ACCOUNT_BALANCE + " INTEGER NOT NULL);";

        // Execute the SQL statement
        db.execSQL(SQL_CREATE_USER_TABLE);

        // Insert Into Table
        db.execSQL("insert into " + TABLE_NAME + " values(7860,'Tanishq Saini', 'tanishq@gmail.com','7584','7895641238', 15000)");
        db.execSQL("insert into " + TABLE_NAME + " values(5862,'Gagan Tripathi', 'gagan@gmail.com','1258','8995641238', 5000)");
        db.execSQL("insert into " + TABLE_NAME + " values(7895,'Surya Pratap', 'surya@gmail.com','8896','7595645896', 1000)");
        db.execSQL("insert into " + TABLE_NAME + " values(1258,'Vikram Garasiya', 'vikram@gmail.com','7752','9995640038', 8000)");
        db.execSQL("insert into " + TABLE_NAME + " values(7410,'Shivani Kumari', 'shivani@gmail.com','3669','9095648962', 7500)");
        db.execSQL("insert into " + TABLE_NAME + " values(8529,'Piyush Joshi', 'piyush@gmail.com','9985','8855640238', 6500)");
        db.execSQL("insert into " + TABLE_NAME + " values(3698,'Yash Pratap', 'yash@gmail.com','1207','8895640215', 4500)");
        db.execSQL("insert into " + TABLE_NAME + " values(7853,'Khushi Jain', 'khushi@gmail.com','4522','9985021539', 2500)");
        db.execSQL("insert into " + TABLE_NAME + " values(4562,'Ritik Sharma', 'ritik@gmail.com','6582','9309565238', 10500)");
        db.execSQL("insert into " + TABLE_NAME + " values(2365,'Rohit Patidar', 'rohit@gmail.com','5450','8292591201', 9900)");
        db.execSQL("insert into " + TABLE_NAME + " values(7854,'Anurag Sharma', 'anurag@gmail.com','2656','9015641200', 9800)");
        db.execSQL("insert into " + TABLE_NAME + " values(3621,'Hitish Kumar', 'hitish@gmail.com','1203','9995641999', 11000)");
        db.execSQL("insert into " + TABLE_NAME + " values(1122,'Naveen Chaturvedi', 'naveen@gmail.com','5566','9119541001', 5800)");
        db.execSQL("insert into " + TABLE_NAME + " values(9512,'Gauri Parashar', 'gauri@gmail.com','2236','6254642205', 3500)");
        db.execSQL("insert into " + TABLE_NAME + " values(7530,'Farhan Khan', 'farhan@gmail.com','6692','6893641266', 1010)");
    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        if (oldVersion != newVersion) {
            // Simplest implementation is to drop all old tables and recreate them
            db.execSQL("DROP TABLE IF EXISTS " + UserEntry.TABLE_NAME);
            onCreate(db);
        }
    }

    public Cursor readAllData() {
        SQLiteDatabase db = this.getWritableDatabase();
        Cursor cursor = db.rawQuery("select * from " + UserEntry.TABLE_NAME, null);
        return cursor;
    }

    public Cursor readParticularData (int accountNo) {
        SQLiteDatabase db = this.getWritableDatabase();
        Cursor cursor = db.rawQuery("select * from " + UserEntry.TABLE_NAME + " where " +
                                        UserEntry.COLUMN_USER_ACCOUNT_NUMBER + " = " + accountNo, null);
        return cursor;
    }

    public void updateAmount(int accountNo, int amount) {
        Log.d ("TAG", "update Amount");
        SQLiteDatabase db = this.getWritableDatabase();
        db.execSQL("update " + UserEntry.TABLE_NAME + " set " + UserEntry.COLUMN_USER_ACCOUNT_BALANCE + " = " + amount + " where " +
                UserEntry.COLUMN_USER_ACCOUNT_NUMBER + " = " + accountNo);
    }
}

package com.example.basicbankingapp.Data;

public class Transaction {
    private String fromUser;
    private String ToUser;
    private int amountTransferred;
    private int status;

    public Transaction(String fromUser, String toUser, int amountTransferred, int status) {
        this.fromUser = fromUser;
        ToUser = toUser;
        this.amountTransferred = amountTransferred;
        this.status = status;
    }

    public String getFromUser() {
        return fromUser;
    }

    public void setFromUser(String fromUser) {
        this.fromUser = fromUser;
    }

    public String getToUser() {
        return ToUser;
    }

    public void setToUser(String toUser) {
        ToUser = toUser;
    }

    public int getAmountTransferred() {
        return amountTransferred;
    }

    public void setAmountTransferred(int amountTransferred) {
        this.amountTransferred = amountTransferred;
    }

    public int getStatus() {
        return status;
    }

    public void setStatus(int status) {
        this.status = status;
    }
}

package com.example.basicbankingapp.Data;

public class User {
    private String name;
    private int accountNumber;
    private String phoneNumber;
    private String ifscCode;
    private int balance;
    private String email;

    public User(String name, int accountNumber, String phoneNumber, String ifscCode, int balance, String email) {
        this.name = name;
        this.accountNumber = accountNumber;
        this.phoneNumber = phoneNumber;
        this.ifscCode = ifscCode;
        this.balance = balance;
        this.email = email;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAccountNumber() {
        return accountNumber;
    }

    public void setAccountNumber(int accountNumber) {
        this.accountNumber = accountNumber;
    }

    public String getPhoneNumber() {
        return phoneNumber;
    }

    public void setPhoneNumber(String phoneNumber) {
        this.phoneNumber = phoneNumber;
    }

    public String getIfscCode() {
        return ifscCode;
    }

    public void setIfscCode(String ifscCode) {
        this.ifscCode = ifscCode;
    }

    public int getBalance() {
        return balance;
    }

    public void setBalance(int balance) {
        this.balance = balance;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }
}

package com.example.basicbankingapp.ListAdapters;

import android.content.Context;
import android.content.Intent;
import android.view.LayoutInflater;
import android.view.View;
import android.view.View.OnClickListener;
import android.view.ViewGroup;
import android.widget.TextView;

import androidx.annotation.NonNull;
import androidx.recyclerview.widget.RecyclerView;

import com.example.basicbankingapp.Data.Transaction;
import com.example.basicbankingapp.Data.User;
import com.example.basicbankingapp.R;
import com.example.basicbankingapp.UI.SendToUserList;
import com.example.basicbankingapp.UI.UserData;

import java.util.ArrayList;

public class SendToUserAdapter extends RecyclerView.Adapter<SendToUserAdapter.ViewHolder> {
    private ArrayList<User> userArrayList;
    private OnUserListener onUserListener;

    public SendToUserAdapter(ArrayList<User> userArrayList, OnUserListener onUserListener) {
        this.userArrayList = userArrayList;
        this.onUserListener = onUserListener;
    }

    @NonNull
    @Override
    public SendToUserAdapter.ViewHolder onCreateViewHolder(@NonNull ViewGroup viewGroup, int viewType) {
        View view = LayoutInflater.from (viewGroup.getContext()).inflate(R.layout.user_list_item, viewGroup, false);
        return new SendToUserAdapter.ViewHolder(view, onUserListener);
    }

    @Override
    public void onBindViewHolder(@NonNull SendToUserAdapter.ViewHolder viewHolder, int position) {
        viewHolder.itemView.setTag(userArrayList.get(position));
        viewHolder.userName.setText(userArrayList.get(position).getName());
        viewHolder.userAccountBalance.setText(String.format("%d", userArrayList.get(position).getBalance()));
    }

    @Override
    public int getItemCount() {
        return userArrayList.size();
    }

    public class ViewHolder extends RecyclerView.ViewHolder implements View.OnClickListener {
        TextView userName, userAccountBalance;
        OnUserListener onUserListener;

        public ViewHolder(@NonNull View itemView, OnUserListener onUserListener) {
            super(itemView);
            userName = itemView.findViewById(R.id.username);
            userAccountBalance = itemView.findViewById(R.id.amount);
            this.onUserListener = onUserListener;
            itemView.setOnClickListener(this);
        }

        @Override
        public void onClick(View v) {
            onUserListener.onUserClick(getAdapterPosition());
        }
    }

    public interface OnUserListener {
        void onUserClick(int position);
    }
}

package com.example.basicbankingapp.UI;

import androidx.appcompat.app.AppCompatActivity;

import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.view.animation.Animation;
import android.view.animation.AnimationUtils;
import android.widget.Button;
import android.widget.ImageView;
import android.widget.TextView;

import com.example.basicbankingapp.R;

public class HomeScreen extends AppCompatActivity {

    Animation anim;
    ImageView bankLogo;
    TextView tvBankTitle;
    Button btnAllUsers, btnAllTransactions;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_home_screen);

        anim = AnimationUtils.loadAnimation(this, R.anim.animation);
        bankLogo = findViewById(R.id.bank_logo);
        btnAllUsers = findViewById(R.id.all_users);
        btnAllTransactions = findViewById(R.id.all_transactions);
        tvBankTitle = findViewById(R.id.bank_title);

        bankLogo.setAnimation(anim);
        tvBankTitle.setAnimation(anim);
        btnAllUsers.setAnimation(anim);
        btnAllTransactions.setAnimation(anim);
    }

   public void showAllUsers(View view) {
        Intent intent = new Intent(this, UsersList.class);
        startActivity(intent);
    }

    public void showAllTransactions(View view) {
        Intent intent = new Intent(this, TransactionHistory.class);
        startActivity(intent);
    }
}

package com.example.basicbankingapp.UI;

import androidx.appcompat.app.AlertDialog;
import androidx.appcompat.app.AppCompatActivity;
import androidx.recyclerview.widget.LinearLayoutManager;
import androidx.recyclerview.widget.RecyclerView;

import android.content.ContentValues;
import android.content.DialogInterface;
import android.content.Intent;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
import android.graphics.Color;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.TextView;
import android.widget.Toast;

import com.example.basicbankingapp.DB.TransactionContract;
import com.example.basicbankingapp.DB.TransactionHelper;
import com.example.basicbankingapp.DB.UserContract;
import com.example.basicbankingapp.DB.UserHelper;
import com.example.basicbankingapp.Data.Transaction;
import com.example.basicbankingapp.Data.User;
import com.example.basicbankingapp.ListAdapters.SendToUserAdapter;
import com.example.basicbankingapp.ListAdapters.UserListAdapter;
import com.example.basicbankingapp.R;
import com.example.basicbankingapp.DB.TransactionContract.TransactionEntry;
import com.example.basicbankingapp.DB.UserContract.UserEntry;

import java.text.SimpleDateFormat;
import java.util.ArrayList;
import java.util.Calendar;

public class SendToUserList extends AppCompatActivity implements SendToUserAdapter.OnUserListener {
    // RecyclerView
    RecyclerView recyclerView;
    RecyclerView.Adapter myAdapter;
    RecyclerView.LayoutManager layoutManager;
    ArrayList<User> userArrayList;

    // Database
    private UserHelper dbHelper;

    String date=null, time=null;
    int fromUserAccountNo, toUserAccountNo, toUserAccountBalance;
    String fromUserAccountName, fromUserAccountBalance, transferAmount, toUserAccountName;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_send_to_user);

        // Get time instance
        Calendar calendar = Calendar.getInstance();
        SimpleDateFormat simpleDateFormat = new SimpleDateFormat("dd-MMM-yyyy, hh:mm a");
        String date_and_time = simpleDateFormat.format(calendar.getTime());

        // Get Intent
        Bundle bundle = getIntent().getExtras();
        if (bundle != null) {
            fromUserAccountName = bundle.getString("FROM_USER_NAME");
            fromUserAccountNo = bundle.getInt("FROM_USER_ACCOUNT_NO");
            fromUserAccountBalance = bundle.getString("FROM_USER_ACCOUNT_BALANCE");
            transferAmount = bundle.getString("TRANSFER_AMOUNT");
        }

        // Create ArrayList of Users
        userArrayList = new ArrayList<User>();

        // Create Table in the Database
        dbHelper = new UserHelper(this);

        // Show list of items
        recyclerView = findViewById(R.id.send_to_user_list);
        recyclerView.setHasFixedSize(true);

        layoutManager = new LinearLayoutManager(this);
        recyclerView.setLayoutManager(layoutManager);

        myAdapter = new SendToUserAdapter(userArrayList, this);
        recyclerView.setAdapter(myAdapter);
    }

    @Override
    protected void onStart() {
        super.onStart();
        displayDatabaseInfo();
    }

    @Override
    public void onUserClick(int position) {
        // Insert data into transactions table
        toUserAccountNo = userArrayList.get(position).getAccountNumber();
        toUserAccountName = userArrayList.get(position).getName();
        toUserAccountBalance = userArrayList.get(position).getBalance();

        calculateAmount();

        new TransactionHelper(this).insertTransferData(fromUserAccountName, toUserAccountName, transferAmount, 1);
        Toast.makeText(this, "Transaction Successful!!", Toast.LENGTH_LONG).show();

        startActivity(new Intent(SendToUserList.this, HomeScreen.class));
        finish();
    }

    private void calculateAmount() {
        Integer currentAmount = Integer.parseInt(fromUserAccountBalance);
        Integer transferAmountInt = Integer.parseInt(transferAmount);
        Integer remainingAmount = currentAmount - transferAmountInt;
        Integer increasedAmount = transferAmountInt + toUserAccountBalance;

        // Update amount in the dataBase
        new UserHelper(this).updateAmount(fromUserAccountNo, remainingAmount);
        new UserHelper(this).updateAmount(toUserAccountNo, increasedAmount);
    }

    @Override
    public void onBackPressed() {
        AlertDialog.Builder builder_exitButton = new AlertDialog.Builder(SendToUserList.this);
        builder_exitButton.setTitle("Do you want to cancel the transaction?").setCancelable(false)
                .setPositiveButton ("yes", new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick (DialogInterface dialogInterface, int i) {
                        // Transactions Cancelled
                        TransactionHelper dbHelper = new TransactionHelper(getApplicationContext());
                        SQLiteDatabase db = dbHelper.getWritableDatabase();
                        ContentValues values = new ContentValues();

                        values.put(TransactionEntry.COLUMN_FROM_NAME, fromUserAccountName);
                        values.put(TransactionEntry.COLUMN_TO_NAME, toUserAccountName);
                        values.put(TransactionEntry.COLUMN_STATUS, 0);
                        values.put(TransactionEntry.COLUMN_AMOUNT, transferAmount);

                        db.insert(TransactionEntry.TABLE_NAME, null, values);

                        Toast.makeText(SendToUserList.this, "Transaction Cancelled!", Toast.LENGTH_LONG).show();
                        startActivity(new Intent(SendToUserList.this, UsersList.class));
                        finish();
                    }
                }).setNegativeButton("No", null);
        AlertDialog alertExit = builder_exitButton.create();
        alertExit.show();
    }

    private void displayDatabaseInfo() {
        // Create and/or open a database to read from it
        SQLiteDatabase db = dbHelper.getReadableDatabase();

        String[] projection = {
                UserEntry.COLUMN_USER_NAME,
                UserEntry.COLUMN_USER_ACCOUNT_BALANCE,
                UserEntry.COLUMN_USER_ACCOUNT_NUMBER,
                UserEntry.COLUMN_USER_PHONE_NO,
                UserEntry.COLUMN_USER_EMAIL,
                UserEntry.COLUMN_USER_IFSC_CODE,
        };

        Cursor cursor = db.query(
                UserEntry.TABLE_NAME,   // The table to query
                projection,                          // The columns to return
                null,                        // The columns for the WHERE clause
                null,                     // The values for the WHERE clause
                null,                        // Don't group the rows
                null,                         // Don't filter by row groups
                null);                       // The sort order

        try {
            // Figure out the index of each column
            int phoneNoColumnIndex = cursor.getColumnIndex(UserEntry.COLUMN_USER_PHONE_NO);
            int emailColumnIndex = cursor.getColumnIndex(UserEntry.COLUMN_USER_EMAIL);
            int ifscCodeColumnIndex = cursor.getColumnIndex(UserEntry.COLUMN_USER_IFSC_CODE);
            int accountNumberColumnIndex = cursor.getColumnIndex(UserEntry.COLUMN_USER_ACCOUNT_NUMBER);
            int nameColumnIndex = cursor.getColumnIndex(UserEntry.COLUMN_USER_NAME);
            int accountBalanceColumnIndex = cursor.getColumnIndex(UserEntry.COLUMN_USER_ACCOUNT_BALANCE);

            // Iterate through all the returned rows in the cursor
            while (cursor.moveToNext()) {
                // Use that index to extract the String or Int value of the word
                // at the current row the cursor is on.
                String currentName = cursor.getString(nameColumnIndex);
                int accountNumber = cursor.getInt(accountNumberColumnIndex);
                String email = cursor.getString(emailColumnIndex);
                String phoneNumber = cursor.getString(phoneNoColumnIndex);
                String ifscCode = cursor.getString(ifscCodeColumnIndex);
                int accountBalance = cursor.getInt(accountBalanceColumnIndex);

                // Display the values from each column of the current row in the cursor in the TextView
                userArrayList.add(new User(currentName, accountNumber, phoneNumber, ifscCode, accountBalance, email));
            }
        } finally {
            // Always close the cursor when you're done reading from it. This releases all its
            // resources and makes it invalid.
            cursor.close();
        }
    }
}

package com.example.basicbankingapp.UI;

import androidx.appcompat.app.AppCompatActivity;
import androidx.cardview.widget.CardView;
import androidx.recyclerview.widget.LinearLayoutManager;
import androidx.recyclerview.widget.RecyclerView;

import android.content.ContentValues;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.TextView;

import com.example.basicbankingapp.DB.TransactionContract;
import com.example.basicbankingapp.DB.TransactionHelper;
import com.example.basicbankingapp.DB.UserContract;
import com.example.basicbankingapp.DB.UserHelper;
import com.example.basicbankingapp.Data.Transaction;
import com.example.basicbankingapp.Data.User;
import com.example.basicbankingapp.ListAdapters.TransactionHistoryAdapter;
import com.example.basicbankingapp.R;

import java.text.NumberFormat;
import java.util.ArrayList;

public class TransactionHistory extends AppCompatActivity {
    RecyclerView recyclerView;
    RecyclerView.Adapter myAdapter;
    RecyclerView.LayoutManager layoutManager;
    ArrayList<Transaction> transactionArrayList;

    // Database
    private TransactionHelper dbHelper;

    TextView emptyList;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_transaction_history);

        // Get TextView
        emptyList = findViewById(R.id.empty_text);

        // Create Transaction History List
        transactionArrayList = new ArrayList<Transaction>();

        // Create Table in the Database
        dbHelper = new TransactionHelper(this);

        // Display database info
        displayDatabaseInfo();

        recyclerView = findViewById(R.id.transactions_list);
        recyclerView.setHasFixedSize(true);

        layoutManager = new LinearLayoutManager(this);
        recyclerView.setLayoutManager(layoutManager);

        myAdapter = new TransactionHistoryAdapter(this, transactionArrayList);
        recyclerView.setAdapter(myAdapter);
    }

    private void displayDatabaseInfo() {
        Log.d("TAG", "displayDataBaseInfo()");

        // Create and/or open a database to read from it
        SQLiteDatabase db = dbHelper.getReadableDatabase();

        Log.d("TAG", "displayDataBaseInfo()1");

        String[] projection = {
                TransactionContract.TransactionEntry.COLUMN_FROM_NAME,
                TransactionContract.TransactionEntry.COLUMN_TO_NAME,
                TransactionContract.TransactionEntry.COLUMN_AMOUNT,
                TransactionContract.TransactionEntry.COLUMN_STATUS
        };

        Log.d("TAG", "displayDataBaseInfo()2");

        Cursor cursor = db.query(
                TransactionContract.TransactionEntry.TABLE_NAME,   // The table to query
                projection,                          // The columns to return
                null,                        // The columns for the WHERE clause
                null,                     // The values for the WHERE clause
                null,                        // Don't group the rows
                null,                         // Don't filter by row groups
                null);                       // The sort order

        try {
            // Figure out the index of each column
            int fromNameColumnIndex = cursor.getColumnIndex(TransactionContract.TransactionEntry.COLUMN_FROM_NAME);
            int ToNameColumnIndex = cursor.getColumnIndex(TransactionContract.TransactionEntry.COLUMN_TO_NAME);
            int amountColumnIndex = cursor.getColumnIndex(TransactionContract.TransactionEntry.COLUMN_AMOUNT);
            int statusColumnIndex = cursor.getColumnIndex(TransactionContract.TransactionEntry.COLUMN_STATUS);

            Log.d("TAG", "displayDataBaseInfo()3");

            // Iterate through all the returned rows in the cursor
            while (cursor.moveToNext()) {
                // Use that index to extract the String or Int value of the word
                // at the current row the cursor is on.
                String fromName = cursor.getString(fromNameColumnIndex);
                String ToName = cursor.getString(ToNameColumnIndex);
                int accountBalance = cursor.getInt(amountColumnIndex);
                int status = cursor.getInt(statusColumnIndex);


                //Log.d("TAG", "displayDataBaseInfo()4");

                // Display the values from each column of the current row in the cursor in the TextView
                transactionArrayList.add(new Transaction(fromName, ToName, accountBalance, status));
            }
        } finally {
            // Always close the cursor when you're done reading from it. This releases all its
            // resources and makes it invalid.
            cursor.close();
        }

        if (transactionArrayList.isEmpty()) {
            emptyList.setVisibility(View.VISIBLE);
        } else {
            emptyList.setVisibility(View.GONE);
        }
    }
}


package com.example.basicbankingapp.UI;

import androidx.appcompat.app.AlertDialog;
import androidx.appcompat.app.AppCompatActivity;

import android.content.ContentValues;
import android.content.DialogInterface;
import android.content.Intent;
import android.database.sqlite.SQLiteDatabase;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.TextView;
import android.widget.Toast;

import com.example.basicbankingapp.DB.TransactionContract;
import com.example.basicbankingapp.DB.TransactionHelper;
import com.example.basicbankingapp.R;

public class UserData extends AppCompatActivity {

    TextView name, email, accountNo, balance, ifscCode, phoneNo;
    Button transferMoney;
    AlertDialog dialog;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_user_data);

        name = findViewById(R.id.name);
        email = findViewById(R.id.email_id);
        accountNo = findViewById(R.id.account_no);
        balance = findViewById(R.id.avail_balance);
        ifscCode = findViewById(R.id.ifsc_id);
        phoneNo = findViewById(R.id.phone_no);
        transferMoney = findViewById(R.id.transfer_money);

        // Getting the intent
        Intent intent = getIntent();
        Bundle extras = intent.getExtras();

        // Extracting the data
        if (extras != null){
            name.setText(extras.getString("NAME"));
            accountNo.setText(String.valueOf(extras.getInt("ACCOUNT_NO")));
            email.setText(extras.getString("EMAIL"));
            phoneNo.setText(extras.getString("PHONE_NO"));
            ifscCode.setText(extras.getString("IFSC_CODE"));
            balance.setText(extras.getString("BALANCE"));
        }
        else {
            Log.d("TAG", "Empty Intent");
        }

        transferMoney.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                enterAmount();
            }
        });
    }

    private void enterAmount() {
        final AlertDialog.Builder mBuilder = new AlertDialog.Builder(UserData.this);
        View mView = getLayoutInflater().inflate(R.layout.dialog_box, null);
        mBuilder.setTitle("Enter Amount").setView(mView).setCancelable(false);

        final EditText mAmount = (EditText) mView.findViewById(R.id.enter_money);
        mBuilder.setPositiveButton("SEND", new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialogInterface, int i) { }
                                    }).setNegativeButton("CANCEL", new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {
                dialog.dismiss();
                transactionCancel();
            }
        });

        dialog = mBuilder.create();
        dialog.show();
        dialog.getButton(AlertDialog.BUTTON_POSITIVE).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                // Checking whether amount entered is correct or not
                int currentBalance = Integer.parseInt(String.valueOf(balance.getText()));

                if (mAmount.getText().toString().isEmpty()) {
                    mAmount.setError("Amount can't be empty");
                } else if (Integer.parseInt(mAmount.getText().toString()) > currentBalance){
                    mAmount.setError("Your account don't have enough balance");
                } else {
                    Intent intent = new Intent(UserData.this, SendToUserList.class);
                    intent.putExtra("FROM_USER_ACCOUNT_NO", Integer.parseInt(accountNo.getText().toString()));    // PRIMARY_KEY
                    intent.putExtra("FROM_USER_NAME", name.getText());
                    intent.putExtra("FROM_USER_ACCOUNT_BALANCE", balance.getText());
                    intent.putExtra("TRANSFER_AMOUNT", mAmount.getText().toString());
                    startActivity(intent);
                    finish();
                }
            }
        });
    }

    private void transactionCancel() {
        AlertDialog.Builder builder_exitbutton = new AlertDialog.Builder(UserData.this);
        builder_exitbutton.setTitle("Do you want to cancel the transaction?").setCancelable(false)
                .setPositiveButton("yes", new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialogInterface, int i) {
                        Toast.makeText(UserData.this, "Transaction Cancelled!", Toast.LENGTH_LONG).show();
                    }
                }).setNegativeButton("No", new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {
                dialog.dismiss();
                enterAmount();
            }
        });
        AlertDialog alertexit = builder_exitbutton.create();
        alertexit.show();
    }
}

package com.example.basicbankingapp.UI;

import androidx.appcompat.app.AppCompatActivity;
import androidx.recyclerview.widget.LinearLayoutManager;
import androidx.recyclerview.widget.RecyclerView;

import android.content.ContentValues;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteException;
import android.os.Bundle;
import android.util.Log;
import android.widget.TextView;
import android.widget.Toast;

import com.example.basicbankingapp.DB.TransactionContract;
import com.example.basicbankingapp.DB.UserContract;
import com.example.basicbankingapp.DB.UserHelper;
import com.example.basicbankingapp.Data.User;
import com.example.basicbankingapp.ListAdapters.UserListAdapter;
import com.example.basicbankingapp.R;
import com.example.basicbankingapp.DB.UserContract.UserEntry;

import java.text.NumberFormat;
import java.util.ArrayList;

public class UsersList extends AppCompatActivity {
    // RecyclerView
    RecyclerView recyclerView;
    RecyclerView.Adapter myAdapter;
    RecyclerView.LayoutManager layoutManager;
    ArrayList<User> userArrayList;

    // Database
    private UserHelper dbHelper;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_users_list);

        // Create ArrayList of Users
        userArrayList = new ArrayList<User>();

        // Create Table in the Database
        dbHelper = new UserHelper(this);

        // Read Data from DataBase
        displayDatabaseInfo();

        // Show list of items
        recyclerView = findViewById(R.id.all_users_list);
        recyclerView.setHasFixedSize(true);

        layoutManager = new LinearLayoutManager(this);
        recyclerView.setLayoutManager(layoutManager);

        myAdapter = new UserListAdapter(this, userArrayList);
        recyclerView.setAdapter(myAdapter);
    }

    private void displayDatabaseInfo() {
        userArrayList.clear();

        Cursor cursor = new UserHelper(this).readAllData();

        int phoneNoColumnIndex = cursor.getColumnIndex(UserEntry.COLUMN_USER_PHONE_NO);
        int emailColumnIndex = cursor.getColumnIndex(UserEntry.COLUMN_USER_EMAIL);
        int ifscCodeColumnIndex = cursor.getColumnIndex(UserEntry.COLUMN_USER_IFSC_CODE);
        int accountNumberColumnIndex = cursor.getColumnIndex(UserEntry.COLUMN_USER_ACCOUNT_NUMBER);
        int nameColumnIndex = cursor.getColumnIndex(UserEntry.COLUMN_USER_NAME);
        int accountBalanceColumnIndex = cursor.getColumnIndex(UserEntry.COLUMN_USER_ACCOUNT_BALANCE);

        while (cursor.moveToNext()){
            String currentName = cursor.getString(nameColumnIndex);
            int accountNumber = cursor.getInt(accountNumberColumnIndex);
            String email = cursor.getString(emailColumnIndex);
            String phoneNumber = cursor.getString(phoneNoColumnIndex);
            String ifscCode = cursor.getString(ifscCodeColumnIndex);
            int accountBalance = cursor.getInt(accountBalanceColumnIndex);

            // Display the values from each column of the current row in the cursor in the TextView
            userArrayList.add(new User(currentName, accountNumber, phoneNumber, ifscCode, accountBalance, email));
        }
    }
}

<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android">
    <translate
        android:duration="1500"
        android:fromYDelta="-100%p"
        android:toYDelta="0%p" />
    <alpha
        android:fromAlpha="0"
        android:toAlpha="1"
        android:duration="2000" >
    </alpha>
</set>

<vector xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:aapt="http://schemas.android.com/aapt"
    android:width="108dp"
    android:height="108dp"
    android:viewportWidth="108"
    android:viewportHeight="108">
    <path android:pathData="M31,63.928c0,0 6.4,-11 12.1,-13.1c7.2,-2.6 26,-1.4 26,-1.4l38.1,38.1L107,108.928l-32,-1L31,63.928z">
        <aapt:attr name="android:fillColor">
            <gradient
                android:endX="85.84757"
                android:endY="92.4963"
                android:startX="42.9492"
                android:startY="49.59793"
                android:type="linear">
                <item
                    android:color="#44000000"
                    android:offset="0.0" />
                <item
                    android:color="#00000000"
                    android:offset="1.0" />
            </gradient>
        </aapt:attr>
    </path>
    <path
        android:fillColor="#FFFFFF"
        android:fillType="nonZero"
        android:pathData="M65.3,45.828l3.8,-6.6c0.2,-0.4 0.1,-0.9 -0.3,-1.1c-0.4,-0.2 -0.9,-0.1 -1.1,0.3l-3.9,6.7c-6.3,-2.8 -13.4,-2.8 -19.7,0l-3.9,-6.7c-0.2,-0.4 -0.7,-0.5 -1.1,-0.3C38.8,38.328 38.7,38.828 38.9,39.228l3.8,6.6C36.2,49.428 31.7,56.028 31,63.928h46C76.3,56.028 71.8,49.428 65.3,45.828zM43.4,57.328c-0.8,0 -1.5,-0.5 -1.8,-1.2c-0.3,-0.7 -0.1,-1.5 0.4,-2.1c0.5,-0.5 1.4,-0.7 2.1,-0.4c0.7,0.3 1.2,1 1.2,1.8C45.3,56.528 44.5,57.328 43.4,57.328L43.4,57.328zM64.6,57.328c-0.8,0 -1.5,-0.5 -1.8,-1.2s-0.1,-1.5 0.4,-2.1c0.5,-0.5 1.4,-0.7 2.1,-0.4c0.7,0.3 1.2,1 1.2,1.8C66.5,56.528 65.6,57.328 64.6,57.328L64.6,57.328z"
        android:strokeWidth="1"
        android:strokeColor="#00000000" />
</vector>

<vector android:height="24dp" android:tint="#0099ff"
    android:viewportHeight="24" android:viewportWidth="24"
    android:width="24dp" xmlns:android="http://schemas.android.com/apk/res/android">
    <path android:fillColor="@android:color/white" android:pathData="M12,12c2.21,0 4,-1.79 4,-4s-1.79,-4 -4,-4 -4,1.79 -4,4 1.79,4 4,4zM12,14c-2.67,0 -8,1.34 -8,4v2h16v-2c0,-2.66 -5.33,-4 -8,-4z"/>
</vector>

<?xml version="1.0" encoding="utf-8"?>
<vector
    android:height="108dp"
    android:width="108dp"
    android:viewportHeight="108"
    android:viewportWidth="108"
    xmlns:android="http://schemas.android.com/apk/res/android">
    <path android:fillColor="#3DDC84"
          android:pathData="M0,0h108v108h-108z"/>
    <path android:fillColor="#00000000" android:pathData="M9,0L9,108"
          android:strokeColor="#33FFFFFF" android:strokeWidth="0.8"/>
    <path android:fillColor="#00000000" android:pathData="M19,0L19,108"
          android:strokeColor="#33FFFFFF" android:strokeWidth="0.8"/>
    <path android:fillColor="#00000000" android:pathData="M29,0L29,108"
          android:strokeColor="#33FFFFFF" android:strokeWidth="0.8"/>
    <path android:fillColor="#00000000" android:pathData="M39,0L39,108"
          android:strokeColor="#33FFFFFF" android:strokeWidth="0.8"/>
    <path android:fillColor="#00000000" android:pathData="M49,0L49,108"
          android:strokeColor="#33FFFFFF" android:strokeWidth="0.8"/>
    <path android:fillColor="#00000000" android:pathData="M59,0L59,108"
          android:strokeColor="#33FFFFFF" android:strokeWidth="0.8"/>
    <path android:fillColor="#00000000" android:pathData="M69,0L69,108"
          android:strokeColor="#33FFFFFF" android:strokeWidth="0.8"/>
    <path android:fillColor="#00000000" android:pathData="M79,0L79,108"
          android:strokeColor="#33FFFFFF" android:strokeWidth="0.8"/>
    <path android:fillColor="#00000000" android:pathData="M89,0L89,108"
          android:strokeColor="#33FFFFFF" android:strokeWidth="0.8"/>
    <path android:fillColor="#00000000" android:pathData="M99,0L99,108"
          android:strokeColor="#33FFFFFF" android:strokeWidth="0.8"/>
    <path android:fillColor="#00000000" android:pathData="M0,9L108,9"
          android:strokeColor="#33FFFFFF" android:strokeWidth="0.8"/>
    <path android:fillColor="#00000000" android:pathData="M0,19L108,19"
          android:strokeColor="#33FFFFFF" android:strokeWidth="0.8"/>
    <path android:fillColor="#00000000" android:pathData="M0,29L108,29"
          android:strokeColor="#33FFFFFF" android:strokeWidth="0.8"/>
    <path android:fillColor="#00000000" android:pathData="M0,39L108,39"
          android:strokeColor="#33FFFFFF" android:strokeWidth="0.8"/>
    <path android:fillColor="#00000000" android:pathData="M0,49L108,49"
          android:strokeColor="#33FFFFFF" android:strokeWidth="0.8"/>
    <path android:fillColor="#00000000" android:pathData="M0,59L108,59"
          android:strokeColor="#33FFFFFF" android:strokeWidth="0.8"/>
    <path android:fillColor="#00000000" android:pathData="M0,69L108,69"
          android:strokeColor="#33FFFFFF" android:strokeWidth="0.8"/>
    <path android:fillColor="#00000000" android:pathData="M0,79L108,79"
          android:strokeColor="#33FFFFFF" android:strokeWidth="0.8"/>
    <path android:fillColor="#00000000" android:pathData="M0,89L108,89"
          android:strokeColor="#33FFFFFF" android:strokeWidth="0.8"/>
    <path android:fillColor="#00000000" android:pathData="M0,99L108,99"
          android:strokeColor="#33FFFFFF" android:strokeWidth="0.8"/>
    <path android:fillColor="#00000000" android:pathData="M19,29L89,29"
          android:strokeColor="#33FFFFFF" android:strokeWidth="0.8"/>
    <path android:fillColor="#00000000" android:pathData="M19,39L89,39"
          android:strokeColor="#33FFFFFF" android:strokeWidth="0.8"/>
    <path android:fillColor="#00000000" android:pathData="M19,49L89,49"
          android:strokeColor="#33FFFFFF" android:strokeWidth="0.8"/>
    <path android:fillColor="#00000000" android:pathData="M19,59L89,59"
          android:strokeColor="#33FFFFFF" android:strokeWidth="0.8"/>
    <path android:fillColor="#00000000" android:pathData="M19,69L89,69"
          android:strokeColor="#33FFFFFF" android:strokeWidth="0.8"/>
    <path android:fillColor="#00000000" android:pathData="M19,79L89,79"
          android:strokeColor="#33FFFFFF" android:strokeWidth="0.8"/>
    <path android:fillColor="#00000000" android:pathData="M29,19L29,89"
          android:strokeColor="#33FFFFFF" android:strokeWidth="0.8"/>
    <path android:fillColor="#00000000" android:pathData="M39,19L39,89"
          android:strokeColor="#33FFFFFF" android:strokeWidth="0.8"/>
    <path android:fillColor="#00000000" android:pathData="M49,19L49,89"
          android:strokeColor="#33FFFFFF" android:strokeWidth="0.8"/>
    <path android:fillColor="#00000000" android:pathData="M59,19L59,89"
          android:strokeColor="#33FFFFFF" android:strokeWidth="0.8"/>
    <path android:fillColor="#00000000" android:pathData="M69,19L69,89"
          android:strokeColor="#33FFFFFF" android:strokeWidth="0.8"/>
    <path android:fillColor="#00000000" android:pathData="M79,19L79,89"
          android:strokeColor="#33FFFFFF" android:strokeWidth="0.8"/>
</vector>

<vector xmlns:android="http://schemas.android.com/apk/res/android"
    android:width="24dp"
    android:height="24dp"
    android:viewportWidth="24"
    android:viewportHeight="24"
    android:tint="?attr/colorControlNormal">
  <path
      android:fillColor="@android:color/white"
      android:pathData="M12,5.9c1.16,0 2.1,0.94 2.1,2.1s-0.94,2.1 -2.1,2.1S9.9,9.16 9.9,8s0.94,-2.1 2.1,-2.1m0,9c2.97,0 6.1,1.46 6.1,2.1v1.1L5.9,18.1L5.9,17c0,-0.64 3.13,-2.1 6.1,-2.1M12,4C9.79,4 8,5.79 8,8s1.79,4 4,4 4,-1.79 4,-4 -1.79,-4 -4,-4zM12,13c-2.67,0 -8,1.34 -8,4v3h16v-3c0,-2.66 -5.33,-4 -8,-4z"/>
</vector>

<?xml version="1.0" encoding="utf-8"?>
<font-family xmlns:app="http://schemas.android.com/apk/res-auto"
        app:fontProviderAuthority="com.google.android.gms.fonts"
        app:fontProviderPackage="com.google.android.gms"
        app:fontProviderQuery="Armata"
        app:fontProviderCerts="@array/com_google_android_gms_fonts_certs">
</font-family>

<?xml version="1.0" encoding="utf-8"?>
<font-family xmlns:app="http://schemas.android.com/apk/res-auto"
        app:fontProviderAuthority="com.google.android.gms.fonts"
        app:fontProviderPackage="com.google.android.gms"
        app:fontProviderQuery="name=Averia Serif Libre&amp;weight=300"
        app:fontProviderCerts="@array/com_google_android_gms_fonts_certs">
</font-family>

<?xml version="1.0" encoding="utf-8"?>
<font-family xmlns:app="http://schemas.android.com/apk/res-auto"
        app:fontProviderAuthority="com.google.android.gms.fonts"
        app:fontProviderPackage="com.google.android.gms"
        app:fontProviderQuery="Ubuntu"
        app:fontProviderCerts="@array/com_google_android_gms_fonts_certs">
</font-family>
<?xml version="1.0" encoding="utf-8"?>
<font-family xmlns:app="http://schemas.android.com/apk/res-auto"
        app:fontProviderAuthority="com.google.android.gms.fonts"
        app:fontProviderPackage="com.google.android.gms"
        app:fontProviderQuery="name=Ubuntu&amp;weight=300"
        app:fontProviderCerts="@array/com_google_android_gms_fonts_certs">
</font-family>

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/home_screen"
    android:background="@color/white"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".UI.HomeScreen">

    <androidx.appcompat.widget.AppCompatImageView
        android:id="@+id/bank_logo"
        android:layout_width="match_parent"
        android:layout_marginTop="50dp"
        android:layout_height="250dp"
        android:src="@drawable/bank_logo"/>

    <TextView
        android:id="@+id/bank_title"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center_horizontal"
        android:layout_marginTop="30dp"
        android:fontFamily="@font/averia_serif_libre_light"
        android:text="@string/banking_system_app"
        android:textColor="#DD433C55"
        android:textSize="32sp"
        android:textStyle="bold" />

    <Button
        android:id="@+id/all_users"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_gravity="center_horizontal"
        android:layout_marginLeft="80dp"
        android:layout_marginTop="150dp"
        android:layout_marginRight="80dp"
        android:backgroundTint="#0099ff"
        android:onClick="showAllUsers"
        android:textSize="16sp"
        android:fontFamily="@font/averia_serif_libre_light"
        android:text="@string/all_users"
        android:textStyle="bold" />

    <Button
        android:id="@+id/all_transactions"
        android:layout_marginLeft="80dp"
        android:layout_marginRight="80dp"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="20dp"
        android:backgroundTint="#0099ff"
        android:text="@string/all_transactions"
        android:textStyle="bold"
        android:onClick="showAllTransactions"
        android:textSize="16sp"
        android:fontFamily="@font/averia_serif_libre_light"
        android:layout_gravity="center_horizontal" />
</LinearLayout>

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:orientation="vertical"
    android:layout_height="match_parent"
    tools:context=".UI.SendToUserList">

    <TextView
        android:id="@+id/header"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_margin ="10dp"
        android:fontFamily="@font/averia_serif_libre_light"
        android:gravity="center_horizontal|center_vertical"
        android:text="Select a user to transfer money"
        android:textColor="#0099ff"
        android:textSize="20sp"
        android:textStyle="bold"/>

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/send_to_user_list"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:listitem="@layout/user_list_item" />
</LinearLayout>

<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".UI.TransactionHistory">

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/transactions_list"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:padding="5dp"
        android:scrollbars="vertical"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.0"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintVertical_bias="0.0"
        tools:listitem="@layout/transaction_history_item" />

    <TextView
        android:id="@+id/empty_text"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerInParent="true"
        android:text="No Transactions Yet"
        android:textSize="20sp"
        android:textColor="@color/black"
        android:fontFamily="@font/averia_serif_libre_light"
        android:visibility="gone"
        app:layout_constraintBottom_toBottomOf="@+id/transactions_list"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:orientation="vertical"
    android:padding="10dp"
    android:layout_height="match_parent"
    tools:context=".UI.UserData">

    <ImageView
        android:padding="5dp"
        android:src="@drawable/ic_baseline_person_24"
        android:requiresFadingEdge="horizontal"
        android:fadingEdge="vertical"
        android:layout_width="match_parent"
        android:layout_height="200dp"
        android:layout_gravity="center_horizontal"/>

    <LinearLayout
        android:padding="5dp"
        android:layout_marginTop="40dp"
        android:layout_gravity="center_horizontal"
        android:orientation="horizontal"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_weight="0.1"
            android:fontFamily="serif"
            android:text="Name : "
            android:textColor="#0099ff"
            android:textSize="18sp"
            android:textStyle="bold" />

        <TextView
            android:id="@+id/name"
            android:textAlignment="textEnd"
            android:fontFamily="serif"
            android:layout_weight="1"
            android:layout_width="wrap_content"
            android:textColor="@color/black"
            android:layout_height="wrap_content"
            android:textSize="18sp" />
    </LinearLayout>

    <LinearLayout
        android:layout_marginTop="10dp"
        android:padding="5dp"
        android:layout_gravity="center_horizontal"
        android:orientation="horizontal"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_weight="0.1"
            android:textSize="18sp"
            android:fontFamily="serif"
            android:textStyle="bold"
            android:textColor="#0099ff"
            android:text="Account No : "/>

        <TextView
            android:id="@+id/account_no"
            android:textAlignment="textEnd"
            android:fontFamily="serif"
            android:textSize="18sp"
            android:textColor="@color/black"
            android:layout_weight="1"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"/>
    </LinearLayout>

    <LinearLayout
        android:padding="5dp"
        android:layout_marginTop="10dp"
        android:layout_gravity="center_horizontal"
        android:orientation="horizontal"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_weight="0.1"
            android:textSize="18sp"
            android:fontFamily="serif"
            android:textStyle="bold"
            android:textColor="#0099ff"
            android:text="Email : "/>

        <TextView
            android:id="@+id/email_id"
            android:textAlignment="textEnd"
            android:fontFamily="serif"
            android:textSize="18sp"
            android:textColor="@color/black"
            android:layout_weight="1"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"/>
    </LinearLayout>

    <LinearLayout
        android:padding="5dp"
        android:layout_marginTop="10dp"
        android:layout_gravity="center_horizontal"
        android:orientation="horizontal"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_weight="0.1"
            android:textSize="18sp"
            android:fontFamily="serif"
            android:textStyle="bold"
            android:textColor="#0099ff"
            android:text="IFSC Code : "/>

        <TextView
            android:id="@+id/ifsc_id"
            android:layout_weight="1"
            android:textAlignment="textEnd"
            android:fontFamily="serif"
            android:textSize="18sp"
            android:textColor="@color/black"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content" />
    </LinearLayout>

    <LinearLayout
        android:padding="5dp"
        android:layout_marginTop="10dp"
        android:layout_gravity="center_horizontal"
        android:orientation="horizontal"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_weight="0.1"
            android:textSize="18sp"
            android:fontFamily="serif"
            android:textStyle="bold"
            android:textColor="#0099ff"
            android:text="Mobile No : "/>

        <TextView
            android:id="@+id/phone_no"
            android:textColor="@color/black"
            android:layout_weight="1"
            android:textAlignment="textEnd"
            android:fontFamily="serif"
            android:textSize="18sp"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content" />
    </LinearLayout>

    <LinearLayout
        android:layout_gravity="center_horizontal"
        android:padding="5dp"
        android:layout_marginTop="10dp"
        android:orientation="horizontal"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_weight="0.1"
            android:textSize="18sp"
            android:fontFamily="serif"
            android:textStyle="bold"
            android:textColor="#0099ff"
            android:text="Current Balance : "/>

        <TextView
            android:id="@+id/avail_balance"
            android:layout_weight="1"
            android:textAlignment="textEnd"
            android:fontFamily="serif"
            android:textSize="18sp"
            android:textColor="@color/black"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
             />
    </LinearLayout>

    <Button
        android:id="@+id/transfer_money"
        android:text="Transfer Money"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="5dp"
        android:textStyle="bold"
        android:backgroundTint="#0099ff"
        android:fontFamily="@font/averia_serif_libre_light"
        android:layout_marginTop="100dp"
        android:layout_marginLeft="100dp"
        android:layout_marginRight="100dp"/>

</LinearLayout>

<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:id="@+id/all_users_list_layout"
    tools:context=".UI.UsersList">

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/all_users_list"
        android:layout_width="0dp"
        android:layout_height="0dp"
        android:padding="10dp"
        tools:listitem="@layout/user_list_item"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"/>
</androidx.constraintlayout.widget.ConstraintLayout>

<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout
    xmlns:android="http://schemas.android.com/apk/res/android" android:layout_width="match_parent"
    android:layout_height="match_parent">

    <EditText
        android:id="@+id/enter_money"
        android:layout_margin="15dp"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:inputType="number"/>

</RelativeLayout>
© 2021 GitHub, Inc.
Terms


<?xml version="1.0" encoding="utf-8"?>
<androidx.cardview.widget.CardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:id="@+id/transaction_card_view"
    android:layout_margin="10dp"
    android:fadingEdge="horizontal"
    android:layout_height="wrap_content">

    <LinearLayout
        android:layout_width="match_parent"
        android:orientation="vertical"
        android:layout_height="wrap_content">

        <LinearLayout
            android:padding="4dp"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="horizontal">

            <TextView
                android:id="@+id/t_from"
                android:layout_width="50dp"
                android:layout_height="wrap_content"
                android:text="From"
                android:textColor="#0099ff"
                android:fontFamily="@font/armata"
                android:textStyle="bold"
                android:textSize="18sp"
                android:textAlignment="center" />

            <TextView
                android:id="@+id/t_from_name"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:paddingStart="10dp"
                android:textStyle="bold"
                android:textColor="@color/black"
                android:fontFamily="@font/armata"
                android:paddingEnd="3dp"
                android:textSize="18sp"
                android:hint="Not Selected"/>

            <TextView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:gravity="end"
                android:textSize="18sp"
                android:fontFamily="@font/armata"
                android:textStyle="bold"
                android:textColor="#0099ff"
                android:text="Amount" />
            </LinearLayout>

        <LinearLayout
            android:padding="6dp"
            android:id="@+id/to_user_info"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="horizontal">

            <TextView
                android:id="@+id/t_to"
                android:fontFamily="@font/armata"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="To "
                android:textColor="#0099ff"
                android:textStyle="bold"
                android:textSize="18sp" />

            <TextView
                android:id="@+id/t_to_name"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:textSize="18sp"
                android:paddingStart="10dp"
                android:textStyle="bold"
                android:textColor="@color/black"
                android:fontFamily="@font/armata"
                android:paddingEnd="3dp"
                android:hint="Transaction Cancelled"/>

            <TextView
                android:id="@+id/t_amount"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:gravity="end"
                android:fontFamily="@font/armata"
                android:textStyle="bold"
                android:textColor="@color/black"
                android:textSize="18sp"
                android:hint="Not Set" />
         </LinearLayout>

        <LinearLayout
            android:padding="6dp"
            android:visibility="gone"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="horizontal">

            <TextView
                android:layout_weight="1"
                android:id="@+id/t_date"
                android:fontFamily="@font/armata"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:text="Date "
                android:textColor="#0099ff"
                android:textStyle="bold"
                android:textSize="18sp"/>

            <TextView
                android:layout_weight="1"
                android:id="@+id/t_time"
                android:textAlignment="textEnd"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:text="Time "
                android:fontFamily="@font/armata"
                android:textColor="#0099ff"
                android:textStyle="bold"
                android:layout_marginStart="15dp"
                android:textSize="18sp"/>
        </LinearLayout>
    </LinearLayout>

</androidx.cardview.widget.CardView>


<?xml version="1.0" encoding="utf-8"?>
<androidx.cardview.widget.CardView xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:cardCornerRadius="10dp"
    app:cardElevation="8dp"
    android:layout_margin="10dp">

    <RelativeLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:padding="5dp"
        android:layout_margin="5dp">

        <TextView
            android:id="@+id/username"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_alignParentTop="true"
            android:layout_marginLeft="10dp"
            android:text="User Name"
            android:fontFamily="@font/armata"
            android:textStyle="bold"
            android:textColor="#0099ff"
            android:textSize="18sp" />

        <LinearLayout
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_alignParentRight="true"
            android:layout_centerVertical="true"
            android:orientation="horizontal"
            android:layout_marginRight="10dp">

            <TextView
                android:id="@+id/rupees"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="Rs."
                android:textColor="@color/black"
                android:fontFamily="@font/armata"
                android:textSize="16sp"
                android:layout_margin="2dp"/>

            <TextView
                android:id="@+id/amount"
                android:layout_margin="2dp"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="7000"
                android:fontFamily="@font/armata"
                android:textColor="@color/black"
                android:textSize="16sp"/>
            <TextView
                android:id="@+id/slash"
                android:layout_margin="2dp"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="/-"

                android:textSize="16sp"/>
        </LinearLayout>
    </RelativeLayout>
</androidx.cardview.widget.CardView>
