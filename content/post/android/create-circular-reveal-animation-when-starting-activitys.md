---
title: "Android: Create circular reveal animation, when starting activitys"
date: 2017-08-15T21:28:56+02:00
draft: true
---
When you browse through Google's Material Design Guidlines you may discover, that many components or animations are not easy to build in android. For some of them you won't find any library to achieve the same result. One of those animations is the circular reveal animation when you start a new activity. To achieve that easily, I made a small class, that you nearly just have to implement.
<p style="text-align: center;">
	<img src="/static/images/create-circular-reveal-animation-when-starting-activitys.gif" alt="Demo image" style="max-height: 500px;" />
</p>

## Step 1
You have two activitys: The first activity with, let's say, has a fab button and when you click it, it opens the second activity with that cool circular reveal animation starting from that fab button.

Make sure, that your activitys both extend the <code>AppCompatActivity</code>
~~~java
public class MainActivity extends AppCompatActivity { ... }
~~~

Then just add this method to your activity:
~~~java
private void startRevealActivity(View v) {
	//calculates the center of the View v you are passing
	int revealX = (int) (v.getX() + v.getWidth() / 2);
	int revealY = (int) (v.getY() + v.getHeight() / 2);

	//create an intent, that launches the second activity and pass the x and y coordinates
	Intent intent = new Intent(this, SecondActivity.class);
	intent.putExtra(RevealAnimation.EXTRA_CIRCULAR_REVEAL_X, revealX);
	intent.putExtra(RevealAnimation.EXTRA_CIRCULAR_REVEAL_Y, revealY);
	
	//just start the activity as an shared transition, but set the options bundle to null
	ActivityCompat.startActivity(this, intent, null);
	
	//to prevent strange behaviours override the pending transitions
	overridePendingTransition(0, 0);
}
~~~
This code can easily be used with an onclicklistener:
~~~java
fabButton.setOnClickListener(new View.OnClickListener() {
	public void onClick(View v) {
		startRevealActivity(v);
	}
});
~~~

## Step 2
Now you can add my class called <code>RevealAnimation.java</code>.
~~~java
public class RevealAnimation {
    public static final String EXTRA_CIRCULAR_REVEAL_X = "EXTRA_CIRCULAR_REVEAL_X";
    public static final String EXTRA_CIRCULAR_REVEAL_Y = "EXTRA_CIRCULAR_REVEAL_Y";

    private final View mView;
    private Activity mActivity;

    private int revealX;
    private int revealY;

    public RevealAnimation(View view, Intent intent, Activity activity) {
        mView = view;
        mActivity = activity;

        //when you're android version is at leat Lollipop it starts the reveal activity
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP &&
                intent.hasExtra(EXTRA_CIRCULAR_REVEAL_X) &&
                intent.hasExtra(EXTRA_CIRCULAR_REVEAL_Y)) {
            view.setVisibility(View.INVISIBLE);

            revealX = intent.getIntExtra(EXTRA_CIRCULAR_REVEAL_X, 0);
            revealY = intent.getIntExtra(EXTRA_CIRCULAR_REVEAL_Y, 0);

            ViewTreeObserver viewTreeObserver = view.getViewTreeObserver();
            if (viewTreeObserver.isAlive()) {
                viewTreeObserver.addOnGlobalLayoutListener(new ViewTreeObserver.OnGlobalLayoutListener() {
                    @Override
                    public void onGlobalLayout() {
                        revealActivity(revealX, revealY);
                        mView.getViewTreeObserver().removeOnGlobalLayoutListener(this);
                    }
                });
            }
        } else {

        	//if you are below android 5 it jist shows the activity
            view.setVisibility(View.VISIBLE);
        }
    }

    public void revealActivity(int x, int y) {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
            float finalRadius = (float) (Math.max(mView.getWidth(), mView.getHeight()) * 1.1);

            // create the animator for this view (the start radius is zero)
            Animator circularReveal = ViewAnimationUtils.createCircularReveal(mView, x, y, 0, finalRadius);
            circularReveal.setDuration(300);
            circularReveal.setInterpolator(new AccelerateInterpolator());

            // make the view visible and start the animation
            mView.setVisibility(View.VISIBLE);
            circularReveal.start();
        } else {
            mActivity.finish();
        }
    }

    public void unRevealActivity() {
        if (Build.VERSION.SDK_INT < Build.VERSION_CODES.LOLLIPOP) {
            mActivity.finish();
        } else {
            float finalRadius = (float) (Math.max(mView.getWidth(), mView.getHeight()) * 1.1);
            Animator circularReveal = ViewAnimationUtils.createCircularReveal(
                    mView, revealX, revealY, finalRadius, 0);

            circularReveal.setDuration(300);
            circularReveal.addListener(new AnimatorListenerAdapter() {
                @Override
                public void onAnimationEnd(Animator animation) {
                    mView.setVisibility(View.INVISIBLE);
                    mActivity.finish();
                    mActivity.overridePendingTransition(0, 0);
                }
            });

            circularReveal.start();
        }
    }
}
~~~

## Step 3
Before we go on, you have to make some adjustments to your activity's style.
You achieve the best results, when you add this style to your activitys. But make sure, you put them into a <code>values-v21</code> folder, because they will only work on SDK Version 21 and up.
~~~xml
<style name="AppTheme" parent="Theme.AppCompat.Light.NoActionBar">
    <!-- Style for the main activity -->
    <item name="colorPrimary">@color/colorPrimary</item>
    <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
    <item name="colorAccent">@color/colorAccent</item>
</style>

<style name="AppTheme.Transparent" parent="AppTheme">
	<!-- Style for the second activity -->
    <item name="android:windowIsTranslucent">true</item>
    <item name="android:windowBackground">@android:color/transparent</item>
    <item name="android:windowDrawsSystemBarBackgrounds">true</item>
</style>
~~~
Now just add the defined styles to your two activitys. 
Just play around with the styles to match them your preferences. But keep in mind, that the second activity **has to be transparent**.
~~~xml
<activity android:name=".MainActivity" android:theme="@style/AppTheme">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />

        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>
<activity android:name=".SecondActivity" android:theme="@style/AppTheme.Transparent" />
~~~

In the second activity you have to **set a background color** (on your root layout), else it won't work, because we previously set the activity as <code>Transparent</code>.

## Step 4
Now you have to init the <code>new RevealAnimation()</code> in the onCreate method of your second activity.
~~~java
RevealAnimation mRevealAnimation;

@Override
protected void onCreate(Bundle savedInstanceState) {

	Intent intent = this.getIntent();	//get the intent to recieve the x and y coords, that you passed before

	LinearLayout rootLayout = (LinearLayout) findViewById(R.id.root_layout); //there you have to get the root layout of your second activity
	mRevealAnimation = new RevealAnimation(rootLayout, intent, this);
}
~~~
Now when you start the activity it should do the desired animation :)

If you want to show that animation backwards when you finish the activity, just call the <code>unRevealActivity()</code> method.
~~~java
@Override
public void onBackPressed()
{
	mRevealAnimation.unRevealActivity();
}
~~~

I hope, you'll get it to work :)

[This article is based on this source](https://android.jlelse.eu/a-little-thing-that-matter-how-to-reveal-an-activity-with-circular-revelation-d94f9bfcae28)