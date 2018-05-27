---
title: "Android: Get started with volley"
date: 2017-08-15T13:26:38+02:00
draft: true
---
In android it is very useful to make network requests to mostly interact with an api. There are many ways to achieve that, but the simplest way - that is also recommended by Google - is the library [volley](https://developer.android.com/training/volley/index.html).
# Get Started
At first you have to add the volley library to your <code>build.gradle</code>
~~~java
dependencies {
    ...
    compile 'com.android.volley:volley:1.0.0'
}
~~~
Next you have to add the Internet permission to your <code>AndroidManifest.xml</code>, if you haven't already.
~~~xml
<uses-permission android:name="android.permission.INTERNET" />
~~~

## StringRequest
For an normal <code>GET</code> request were starting with an standard <code>StringRequest</code> that you have to add to volley's <code>Volley.newRequestQueue</code>.
~~~java
String url = "http://httpbin.org/get";
 
//request a string response
StringRequest stringRequest = new StringRequest(Request.Method.GET, url, new Response.Listener<String>() {
    @Override
    public void onResponse(String response) {
 
        //now handle the response
        Toast.makeText(MainActivity.this, response, Toast.LENGTH_SHORT).show();
 
    }
}, new Response.ErrorListener() {
    @Override
    public void onErrorResponse(VolleyError error) {
         
        //handle the error
        Toast.makeText(MainActivity.this, "An error occurred", Toast.LENGTH_SHORT).show();
        error.printStackTrace();
 
    }
});
 
// Add the request to the queue
Volley.newRequestQueue(this).add(stringRequest);
~~~
Now that was just the simplest implementation. You can do also other requests:

* [<code>StringRequest</code>](#stringrequest)
* [<code>JSONArrayRequest</code>](#jsonrequest)
* [<code>JSONObjectRequest</code>](#jsonrequest)
* [<code>ImageRequest</code>](#imagerequest)

## JsonRequest

When you want to make a <code>POST</code>-request you usually want to pass some data, too. For that we are using one of the <code>JSONRequest</code>s.
~~~java
String url = "http://httpbin.org/post";

//create post data as JSONObject - if your are using JSONArrayRequest use obviously an JSONArray :)
JSONObject jsonBody = new JSONObject("{\"message\": \"Hello\"}");
 
//request a json object response
JsonObjectRequest jsonRequest = new JsonObjectRequest(Request.Method.POST, url, jsonBody, new Response.Listener<JSONObject>() {
    @Override
    public void onResponse(JSONObject response) {
 
        //now handle the response
        Toast.makeText(MainActivity.this, response, Toast.LENGTH_SHORT).show();
 
    }
}, new Response.ErrorListener() {
    @Override
    public void onErrorResponse(VolleyError error) {
         
        //handle the error
        Toast.makeText(MainActivity.this, "An error occurred", Toast.LENGTH_SHORT).show();
        error.printStackTrace();
 
    }
});
 
// Add the request to the queue
Volley.newRequestQueue(this).add(jsonRequest);
~~~
When you don't want to pass data on the request, you can just replace <code>jsonBody</code> with <code>null</code>:
~~~java
JsonObjectRequest jsonRequest = new JsonObjectRequest(Request.Method.POST, url, null, new Response.Listener<JSONObject>() {
...
}
~~~

## ImageRequest
Sometimes you may want to load images from the web and display it in an ImageView or process it. For that you could use volley's <code>ImageRequest</code>
~~~java
String url = "http://www.underconsideration.com/brandnew/archives/google_2015_logo_detail.png";

//you could use an image view to display the resulting image
imageView = (ImageView) findViewById(R.id.image);
 
ImageRequest imageRequest = new ImageRequest(url, new Response.Listener<Bitmap>() {
    @Override
    public void onResponse(Bitmap response) {
		
		//now handle the response
        imageView.setImageBitmap(response);

    }
},	0,	//maxWidth if 0 it's going to be ignored
	0, 	//maxHeight if 0 it's going to be ignored
	ImageView.ScaleType.FIT_XY, //ScaleType
	Bitmap.Config.ARGB_8888, //just leave it. Fits for the most
	new Response.ErrorListener() {
    @Override
    public void onErrorResponse(VolleyError error) {

        //handle the error
        Toast.makeText(MainActivity.this, "An error occurred", Toast.LENGTH_SHORT).show();

        error.printStackTrace();
    }
});

// Add the request to the queue
Volley.newRequestQueue(this).add(imageRequest);
~~~
But in fact I would recommend to use an image loading library like glide or picasso. They are a lot easier to implement and to use and they do have other helpful image related features as well.