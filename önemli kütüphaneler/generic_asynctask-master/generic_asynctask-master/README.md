### *Attention: this class is deprecated due its inefficiency. You are adviced not to use it any more. Please use my newer version (1.2) library at https://github.com/kosalgeek/generic_asynctask_v2 instead. Thank you very much.* ###

# Generic AsyncTask -- *Deprecated*
This is a custom class that performs a POST call from Android App to Web Server and get the response's result back.

#### Now you can watch the tutorial of using this class on YouTube at https://www.youtube.com/watch?v=e4eFBQmWs8Q ####

## What For?
In Android App, usually when you want to read data in text, XML, or JSON format from a web service, you need to have a private class that extends AsyncTask class. So that will create a big chunk of codes, i.e. private class, in your existing codes.

Hence, this **PostResponseAsyncTask** class will help you to cut out the private class. You can read and write (POST) by using the same class with a different constructor call. Because it is async-ed, to get a callback result, you let your class implement **AsyncResponse** class and you can get a output from its method *processFinish()*. See examples below:
* Read Data
```java
public class ReadDataActivity extends AppCompatActivity implements AsyncResponse {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_read_user);

        PostResponseAsyncTask readData = new PostResponseAsyncTask(this);
        readData.execute("http://yoursite.com/readdata.php");
    }

    @Override
    public void processFinish(String output) {
        //you can get 'output' from here.
    }
}
```
* POST and get Result
```java
public class PostDataActivity extends AppCompatActivity implements AsyncResponse {

    EditText etUsername, etPassword;
    Button btnLogin;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_login);

        HashMap<String, String> postData = new HashMap<String, String>();
        postData.put("key1", "value1");
        postData.put("key2", "value2");
        
        //You pass postData as the 2nd argument of the constructor
        PostResponseAsyncTask loginTask = new PostResponseAsyncTask(this, postData);
        loginTask.execute("http://yoursite.com/post.php");
    }

    @Override
    public void processFinish(String output) {
        //you can get 'output' from here
    }

}
```

# Usage

## Class
```java
public class PostResponseAsyncTask extends AsyncTask<String, Void, String>
```
### Constuctor
#### PostResponseAsyncTask(AsyncResponse delegate)
```java
public PostResponseAsyncTask(AsyncResponse delegate)
```
You have to at least call this constructor and pass in argument of an AsyncResponse object. To pass in the argument, usually you have your activity class implement the AsyncResponse. Note that AsyncResponse class is also a custom class, not part of Android library at all.

This class is used to get data back from reponse. Because it is async-ed, you can get the data through the AsyncReponse's method called *processFinish(string output)*. In the method, you get the data via the 'output' String variable.

Here is an example:

```java  
  public class ReadUserActivity extends AppCompatActivity implements AsyncResponse {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_read_user);

        PostResponseAsyncTask readData = new PostResponseAsyncTask(this);
        //For local emulator connecting to local machine, use 10.0.2.2 
        //or for GenyMotion, use 10.0.3.2 instead of yoursite.com
        readData.execute("http://yoursite.com/getdata.php"); 
    }

    @Override
    public void processFinish(String output) {
        Toast.makeText(this, output, Toast.LENGTH_LONG).show();
    }
  }
```
### PostResponseAsyncTask(AsyncResponse delegate, HashMap&lt;String, String&gt; postData)
```java
public PostResponseAsyncTask(AsyncResponse delegate, HashMap<String, String> postData)
```
This constructor is used for sending POST data into a web server. This constructor can get a second argument as **HashMap<String, String>**. See an example below:
```java

public class LoginActivity extends AppCompatActivity implements AsyncResponse {

    EditText etUsername, etPassword;
    Button btnLogin;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_login);

        etUsername = (EditText)findViewById(R.id.etUsername);
        etPassword = (EditText)findViewById(R.id.etPassword);
        btnLogin = (Button)findViewById(R.id.btnLogin);
        btnLogin.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                HashMap<String, String> postData = new HashMap<String, String>();
                postData.put("username", etUsername.getText().toString());
                postData.put("password", etPassword.getText().toString() );
                PostResponseAsyncTask loginTask = new PostResponseAsyncTask(LoginActivity.this, postData);
                loginTask.execute("http://yoursite.com/login.php");
            }
        });
    }

    @Override
    public void processFinish(String output) {
        if(output.equals("success")){
          startActivity(new Intent(this, MainActivity.class));
        }
    }

}
```
### And below is a sample PHP Code:
```php
if(isset($_POST['username']) && isset($_POST['password'])){
    //Connection
    $servername = "yourServer";
    $username = "yourUserName";
    $password = "yourP@ssword";
    $dbname = "YourDBName";
    
    // Create connection
    $conn = mysqli_connect($servername, $username, $password, $dbname);
    // Check connection
    if (!$conn) {
        die("Connection failed: " . mysqli_connect_error());
    }
    //End Connection
    
    $sql = "SELECT username, password FROM tbl_user WHERE username = ? AND password = ?";

    $stmt = $conn->prepare($sql);

    $stmt->bind_param("ss", $username, $password);
    $username = $_POST['username'];
    $password = $_POST['password'];

    $stmt->execute();

    $stmt->bind_result($username, $password);

    if($stmt->fetch()){
      echo "success";
      exit;
    }
    else{
      echo "failed";
      exit;
    }
}
```
## Additional Constructors & Methods
```java
public PostResponseAsyncTask(AsyncResponse delegate, String loadingMessage)
```
By the default, while reading data from a web server, the loading message appears "Loading...". And if you want to say something else, you can set a message as the 2nd argument of the constructor.
```java
public PostResponseAsyncTask(AsyncResponse delegate, HashMap<String, String> postData, String loadingMessage)
```
This method is for sending data to a web server and set a message.
```java
public void setLoadingMessage(String loadingMessage)
```
You can also explicitly call setLoadingMessage to set a message while loading.
```java
public void setPostData(HashMap<String, String> postData)
```
It is for setting postData.

# Set Up

1. Download **GenAsync.jar** from this github to your local machine. 
2. Go to your project in Android Studio, on a Project panel (on the left), go to *app > libs*. You copy the *GenAsync.jar* and paste it in the *libs* folder > Press OK if a asked message appeared.
3. Right click on the jar file > Choose *Add As Library*. And if you are still not sure how to do that, please check out this link http://code2care.org/pages/import-external-jars-to-android-studio-project/.
4. Try this code below:
```java
PostResponseAsyncTask task = new PostResponseAsyncTask(this);
```
5. If it shows no errors and you see  **import com.kosalgeek.asynctask.PostResponseAsyncTask;** correctly, then you are good to go.

## Follow Me
 * Get more tutorials at http://www.kosalgeek.com and http://www.top12review.com
 * Get more free source codes at https://github.com/kosalgeek
 * Subscribe my YouTube channel https://youtube.com/user/oumsaokosal
 * Follow me on Twitter https://twitter.com/kosalgeek and https://twitter.com/okosal
 

# LICENSE

(The MIT License)
Copyright (c) 2015 KosalGeek. (kosalgeek at gmail dot com)

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the 'Software'), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
