Parse4J - Java Library for parse.com
====================================

The non-official java library for [on Parse](https://parse.com) 

ps.: most of the following code snippets and text have been extracted from the parse website, since the java library mimicks the android library.

Summary
-------

The Parse platform provides a complete backend solution for your mobile application. Our goal is to totally eliminate the need for writing server code or maintaining servers.

If you're familiar with web frameworks like Ruby on Rails, we've taken many of the same principles and applied them to our platform. In particular, our SDK is ready to use out of the box with minimal configuration on your part.

#### Apps

On Parse, you create an App for each of your mobile applications. Each App has its own application id and client key that you apply to your SDK install. Your account on Parse can accommodate multiple Apps. This is useful even if you have one application, since you can deploy different versions for test and production.


Getting Started
---------------

#### Maven

```XML
	<project ...>
	    ...
	    <dependencies>
	        <dependency>
	            <groupId>org.parse4j</groupId>
	            <artifactId>parse4j</artifactId>
	            <version>1.0</version>
	        </dependency>
	    </dependencies>
	    ...
	</project>
```


Objects
-------

Storing data on Parse is built around the ParseObject. Each ParseObject contains key-value pairs of JSON-compatible data. This data is schemaless, which means that you don't need to specify ahead of time what keys exist on each ParseObject. You simply set whatever key-value pairs you want, and our backend will store it.

For example, let's say you're tracking high scores for a game. A single ParseObject could contain:

```Java
	score: 1337, playerName: "Sean Plott", cheatMode: false 
```

Keys must be alphanumeric strings. Values can be strings, numbers, booleans, or even arrays and objects - anything that can be JSON-encoded.

Each ParseObject has a class name that you can use to distinguish different sorts of data. For example, we could call the high score object a GameScore. Parse recommend that you NameYourClassesLikeThis and nameYourKeysLikeThis, just to keep your code looking pretty.

#### Saving Objects

```Java
	ParseObject gameScore = new ParseObject("GameScore");
	gameScore.put("score", 1337);
	gameScore.put("playerName", "Sean Plott");
	gameScore.put("cheatMode", false);
	gameScore.save();
```

Use saveInBackground() if you want to delegate the operation to a background thread

```Java
	gameScore.saveInBackground();  
```

#### Retrieving Objects

```Java
	ParseQuery<ParseObject> query = ParseQuery.getQuery("GameScore");
	query.getInBackground("xWMyZ4YEGZ", new GetCallback<ParseObject>() {
	  public void done(ParseObject object, ParseException e) {
	    if (e == null) {
	      // object will be your game score
	    } else {
	      // something went wrong
	    }
	  }
	}); 
```


To get the values out of the ParseObject, there's a getX method for each data type:

```Java
	int score = gameScore.getInt("score");
	String playerName = gameScore.getString("playerName");
	boolean cheatMode = gameScore.getBoolean("cheatMode");
```

If you don't know what type of data you're getting out, you can call get(key), but then you probably have to cast it right away anyways. In most situations you should use the typed accessors like getString.

The three special values have their own accessors:

```Java
	String objectId = gameScore.getObjectId();
	Date updatedAt = gameScore.getUpdatedAt();
	Date createdAt = gameScore.getCreatedAt();
```

#### Callback functions

You can also add a callback function to the save operation

```Java
	final ParseObject gameScore = new ParseObject("GameScore");
	gameScore.put("score", 1337);
	gameScore.put("playerName", "Sean Plott");
	gameScore.put("cheatMode", false);
	gameScore.saveInBackground(new SaveCallback() {			
			@Override
			public void done(ParseException parseException) {
				System.out.println("saveInBackground(): objectId: " + parseObject.getObjectId());
				System.out.println("saveInBackground(): createdAt: " + parseObject.getCreatedAt());
				System.out.println("saveInBackground(): updatedAt: " + parseObject.getUpdatedAt());
			}
		});
```

#### Delete

To delete an object, just call the method delete().

```Java
	gameScore.delete();
```
You can also attach a callback function on the delete/deleteInBackground methods.

```Java
	gameScore.deleteInBackground(new DeleteCallback() {
			@Override
			public void done(ParseException parseException) {
				//do something
			}
		});
```

#### Counters

You can also increment and decrement values from Number (int, long, double...) attributes.

```Java
	gameScore.increment("score");
	gameScore.decrement("score");
	gameScore.saveInBackground();
```
You can also increment by any amount using increment(key, amount) or decrement by any amount using decrement(key, amount).

#### Arrays

```Java
gameScore.addAllUnique("skills", Arrays.asList("flying", "kungfu"));
gameScore.saveInBackground();
```

Queries
-------

Pending...

Users
-----

In development...

Roles
-----

Pending...


Files
-----

ParseFile lets you store application files in the cloud that would otherwise be too large or cumbersome to fit into a regular ParseObject. The most common use case is storing images but you can also use it for documents, videos, music, and any other binary data (up to 10 megabytes).

Getting started with ParseFile is easy. First, you'll need to have the data in byte[] form and then create a ParseFile with it. In this example, we'll just use a string:

```JAVA
	byte[] data = "Working at Parse is great!".getBytes();
	ParseFile file = new ParseFile("resume.txt", data);
	file.save();
```

Notice in this example that we give the file a name of resume.txt. There's two things to note here:

* You don't need to worry about filename collisions. Each upload gets a unique identifier so there's no problem with uploading multiple files named resume.txt.
* It's important that you give a name to the file that has a file extension. This lets Parse figure out the file type and handle it accordingly. So, if you're storing PNG images, make sure your filename ends with .png.

Next you'll want to save the file up to the cloud. As with ParseObject, there are many variants of the save method you can use depending on what sort of callback and error handling suits you.

```JAVA
	file.saveInBackground();
```
Finally, after the save completes, you can associate a ParseFile onto a ParseObject just like any other piece of data:

```JAVA
	ParseObject jobApplication = new ParseObject("JobApplication");
	jobApplication.put("applicantName", "Joe Smith");
	jobApplication.put("applicantResumeFile", file);
	jobApplication.save();
```

Retrieving it back involves calling one of the getData variants on the ParseObject. Here we retrieve the resume file off another JobApplication object:

```JAVA
	ParseFile applicantResume = (ParseFile)anotherApplication.get("applicantResumeFile");
	applicantResume.getDataInBackground(new GetDataCallback() {
	  public void done(byte[] data, ParseException e) {
	    if (e == null) {
	      // data has the bytes for the resume
	    } else {
	      // something went wrong
	    }
	  }
	});
```

#### Progress

It's easy to get the progress of both uploads and downloads using ParseFile by passing a ProgressCallback to saveInBackground and getDataInBackground. For example:

```JAVA
	byte[] data = getBytes("song.mp3");
	ParseFile file = new ParseFile("song.mp3", data);
	file.save(new ProgressCallback() {
		
		@Override
		public void done(Integer percentDone) {
			//do something
		}
	});
```

or:

```JAVA
	byte[] data = getBytes("song.mp3");
	ParseFile file = new ParseFile("song.mp3", data);
	file.save(new SaveCallback() {
		
		@Override
		public void done(ParseException parseException) {
			//do something
		}
	});
```

There also another possibility to save the file and give both callbacks at one.

Analytics
---------

Pending ...

Cloud Functions
---------------

In development...

GeoPoints
---------



Notes
-----

### License


TODO
-----

### Pending Functionalities

 * Queries
 * Analytics
 * Push Notifications
 * Cloud Functions
 * Instalations
 
### Implemented Functionalities

 * Objects
 * Files
 * Geopoints
  
 
 