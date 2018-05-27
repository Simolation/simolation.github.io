---
title: "Android: Volley set headers"
date: 2017-08-15T20:56:32+02:00
draft: true
---
If you are using volley for android as described in [this post](../get-started-with-volley/), you may have to set headers to get the wanted response from your server.

Therefore you just have to add after the wanted request an <code>Map</code>, that returns the desired headers:
~~~java
JsonObjectRequest jsonObjectRequest = new JsonObjectRequest( ... ) {
    @Override
    public Map<String, String> getHeaders() {
        Map<String, String> params = new HashMap<String, String>();
        params.put("x-vacationtoken", "secret_token");
        params.put("content-type", "application/json");
        return params;
    }
};
~~~

# Code Example
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
}) {	//this is the part, that adds the header to the request
    @Override
    public Map<String, String> getHeaders() {
        Map<String, String> params = new HashMap<String, String>();
        params.put("x-vacationtoken", "secret_token");
        params.put("content-type", "application/json");
        return params;
    }
};
 
// Add the request to the queue
Volley.newRequestQueue(this).add(jsonRequest);
~~~