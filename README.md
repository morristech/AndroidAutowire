Android Autowire
======

A library to replace the use of ```findViewById()``` in Android Activity classes with an annotation based approach.

One particularly jarring example of Android boilerplate code is the ```findViewById()``` method.  Every time you want to access an Android view defined in your XML, you need to use this method, often with a typecast.  For large Activities with many views, this can add a lot of code that does nothing but pull variables out of the xml.

This library will help streamline this process into a more readable format using annotations and reflection.  By annotating a class variable for the View with the ```@AndroidView``` custom annotation, you enable the reflection code to pull the view out of the xml.  The variable name will be the view id, or alternatively, the view id can be specified in the annotation.  The annotation processing occurs in an overridden method of ```setContentView(int layoutResID)``` in the Activity’s base class.

This repository is referenced in the blog post: http://www.cardinalsolutions.com/cardinal/blog/mobile/2013/09/android_autowirelib.html.

The Android Way
---------

```java
public class MainActivity extends BaseActivity{

	private ImageView logo;
	private TextView label;
	private EditText enterText;

	@Override
    public void onCreate(Bundle savedInstanceState){
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);

    	logo = (ImageView) findViewById(R.id.logo);
    	label = (TextView findViewById(R.id.lbl_test_label);
    	label = (EditText) findViewById(R.id.txt_enter);
    	
    	label.setText("Hello World");
	}
}
```

With AndroidAutowire
------------

### MainActivity Class

```java
public class MainActivity extends BaseActivity{

	@AndroidView
	private ImageView logo;
	
	@AndroidView(resId=R.id.lbl_test_label)
	private TextView label;
	
	@AndroidView(resId=R.id.txt_enter);
	private EditText enterText;

	@Override
    public void onCreate(Bundle savedInstanceState){
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
        
        label.setText("Hello World");
	}
}
```

### BaseActivity class

```java
public class BaseActivity extends Activity {

	@Override
    public void setContentView(int layoutResID) {
    	super.setContentView(layoutResID);
    	AndroidAutowire.autowire(this, BaseActivity.class);
    }
}
```


Configuration
-------

Import the jar into the project classpath.  Make sure your Activities that want to use this extend from a base class that extends Activity.  Then add the override for setContentView in the base activity class.


Features
------

* Supports Inheritance of Activities. You can have multiple Activities inheriting views, and every view will be picked up and wired in.
* As it uses reflection, it will work with private variables
* Comes with several out of the box ways of specifying IDs allowing for flexibility in naming IDs and implanting the annotations.
* Provides an optional required field in the annotation, so if an ID is not found, the variable will be skipped without an Exception being thrown.

Comparison to Other Libraries
-------

There are some other open source libraries that accomplish something similar to what Android Autowire hopes to provide

**RoboGuice** is a dependency injection library that can inject views in much the same way.  However, you must extend the Robo* classes, and there may be performance issues. (https://github.com/roboguice/roboguice/wiki)

**Android Annotations** can wire in views by annotation, but the approach they take is quite different.  Android Annotations requires you to use an extra compile step, creating generated Activity classes that must be referenced in the AndroidManifest.xml.  As this approach will create subclasses of your Activity, you cannot use this on private variables.  Additionally, there is much more configuration and initial setup. (https://github.com/excilys/androidannotations/wiki)

**Butter Knife** does the same compile time annotation approach as Android Annotations, but instead of generating a new Activity, they generate a class to pass your activity into. This way, you don't have to deal with generated sub classes, but you still get some of the heavy hitting features like onClick Listeners. (http://jakewharton.github.io/butterknife/)

The real advantage to this "Android Autowire" library is ease of use.  There is minimal configuration in just about every IDE, and little overhead, allowing you to quickly start using these annotations in your new or existing project.  Instead of providing a full feature set, this library concentrates only on Android views, allowing it to fill the gap while still being lightweight.


Performance
------

Reflection code is known to be a bit inefficient on Android. However, because this library is only looking at a small subset of the Activity fields, only the declared fields in each class extending from your base activity, performance is virtually the same as using ```findViewById()```, even on some sizable activities with plenty of class variables.


## Author / License

Copyright Cardinal Solutions 2013. Licensed under the MIT license.
<img src="https://raw.github.com/CardinalNow/NSURLConnection-Debug/master/logo_footer.png"/>


