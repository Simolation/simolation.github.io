---
title: "Android: Programmatically hide and show the keyboard"
date: 2017-08-15T12:16:14+02:00
draft: true
---
Sometimes you may want to programmatically close or open the soft keyboard in your app.

# Open soft keyboard
When you click on a button you can focus an editText and open the keyboard, to start typing
~~~java
//Get EditText instance
EditText editText = (EditText) findViewById(R.id.edit_text);

//focus editText
editText.requestFocus();

//create InputMethodManager
InputMethodManager imm = (InputMethodManager) getSystemService(Context.INPUT_METHOD_SERVICE);

//show soft keyboard and you can start typing into editText
imm.showSoftInput(editText, InputMethodManager.SHOW_IMPLICIT);
~~~

# Hide soft keyboard
When you submit a form, you may want to hide the keyboard, even if the editText is still focused
~~~java
//create InputMethodManager
InputMethodManager imm = (InputMethodManager) getSystemService(Context.INPUT_METHOD_SERVICE);

//hide soft keyboard
imm.toggleSoftInput(InputMethodManager.HIDE_IMPLICIT_ONLY, 0);
~~~