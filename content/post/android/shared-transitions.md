---
title: "Android: Shared transitions"
date: 2017-08-17T22:14:52+02:00
draft: true
---
In android you can make cool animations, that let objects move into another activity. For that you should use shared transitions.

## Get started
To start you have to add this tag <code>android:transitionName="transition_name"</code> to your view, that is going to move.
Chose an unique name for the transition, else it won't work.
~~~xml
<ImageView
        android:id="@+id/image"
        android:layout_width="40dp"
        android:layout_height="40dp"
        android:transitionName="transition_image"
        android:src="@drawable/avatar" />
~~~

Now you can go on with your activity.
~~~java
Intent intent = new Intent(MainActivity.this, SecondActivity.class);

ImageView imageView = findViewById(R.id.image);

ActivityOptionsCompat options = ActivityOptionsCompat.makeSceneTransitionAnimation(MainActivity.this, 
	(View) imageView, //put here the view, that should be shared
	"transition_image"); //this has to be the string, you set at the transitionName tag

//start the activity with the options as bundle
startActivity(intent, options.toBundle());
~~~

Now make sure, you have an ImageView with the same <code>transitionName</code> tag on the second activity's layout and you are ready to go :)

When you also want to have the shared transition backwards, you just have to replace every <code>finish();</code> call with <code>supportFinishAfterTransition();</code>

## Tip
When you are loading your images with glide or picasso, there are often problems with shared transitions. For that there is a trick:
Add this code to your second activity.
~~~java
//This pauses the transition
supportPostponeEnterTransition();

Glide
    .with( SecondActivity.this )
    .load( url )
    .diskCacheStrategy(DiskCacheStrategy.SOURCE) //cache the image, to reduce the time to wait
    .dontAnimate() //make sure, there is no animation on the image
    .listener(new RequestListener<String, GlideDrawable>() {
        @Override
        public boolean onException(Exception e, String model, Target<GlideDrawable> target, boolean isFirstResource) {
			
			//loading the image failed, so you can display an error or anyways finish the transition
            supportStartPostponedEnterTransition();
            return false;

        }

        @Override
        public boolean onResourceReady(GlideDrawable resource, String model, Target<GlideDrawable> target, boolean isFromMemoryCache, boolean isFirstResource) {

        	//now when the image has loaded, you can resume the transition
            supportStartPostponedEnterTransition();
            return false;

        }
    })
    .into( mImageProfile );
~~~
Basically you could also use this to wait until a server request is done. Just try it out :)

