# PowerSpinner
<p align="center">
  <a href="https://opensource.org/licenses/Apache-2.0"><img alt="License" src="https://img.shields.io/badge/License-Apache%202.0-blue.svg"/></a>
  <a href="https://android-arsenal.com/api?level=17"><img alt="API" src="https://img.shields.io/badge/API-17%2B-brightgreen.svg?style=flat"/></a>
  <a href="https://travis-ci.com/skydoves/PowerSpinner"><img alt="Build Status" src="https://travis-ci.com/skydoves/PowerSpinner.svg?branch=master"/></a>
  <a href="https://skydoves.github.io/libraries/powerspinner/javadoc/powerspinner/com.skydoves.powerspinner/index.html"><img alt="Javadoc" src="https://img.shields.io/badge/Javadoc-PowerSpinner-yellow"/></a>
</p>

<p align="center">
🌀 A lightweight dropdown popup spinner with an arrow and animations.
</p>

<p align="center">
<img src="https://user-images.githubusercontent.com/24237865/71555542-eaaafe80-2a70-11ea-85d4-e20528459dc1.gif" width="32%"/>
</p>


## Including in your project
### Gradle 
Add below codes to your **root** `build.gradle` file (not your module build.gradle file).
```gradle
allprojects {
    repositories {
        jcenter()
    }
}
```
And add a dependency code to your **module**'s `build.gradle` file.
```gradle
dependencies {
    implementation "com.github.skydoves:powerspinner:1.0.0"
}
```

## Usage
Add following XML namespace inside your XML layout file.

```gradle
xmlns:app="http://schemas.android.com/apk/res-auto"
```

### PowerSpinnerView
Here is a basic example of implementing `PowerSpinnerView`. </br>
Basically the `PowerSpinnerView` extends `TextView`, so we can use it like a `TextView`.</br>
You can set the unselected text using `hint` and `textColorHint` attributes.

```gradle
<com.skydoves.powerspinner.PowerSpinnerView
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:background="@color/md_blue_200"
  android:gravity="center"
  android:hint="Question 1"
  android:padding="10dp"
  android:textColor="@color/white_93"
  android:textColorHint="@color/white_70"
  android:textSize="14.5sp"
  app:spinner_arrow_gravity="end"
  app:spinner_arrow_padding="8dp"
  app:spinner_divider_color="@color/white_70"
  app:spinner_divider_show="true"
  app:spinner_divider_size="0.4dp"
  app:spinner_item_array="@array/questions"
  app:spinner_popup_animation="dropdown"
  app:spinner_popup_background="@color/background800"
  app:spinner_popup_elevation="14dp" />
```

### Create using builder class
We can create an instance of `PowerSpinnerView` using the builder class.
```gradle
val mySpinnerView = createPowerSpinnerView(this) {
  setSpinnerPopupWidth(300)
  setSpinnerPopupHeight(350)
  setArrowPadding(6)
  setArrowAnimate(true)
  setArrowAnimationDuration(200L)
  setArrowGravity(SpinnerGravity.START)
  setArrowTint(ContextCompat.getColor(this@MainActivity, R.color.md_blue_200))
  setSpinnerPopupAnimation(SpinnerAnimation.BOUNCE)
  setShowDivider(true)
  setDividerColor(Color.WHITE)
  setDividerSize(2)
  setLifecycleOwner(this@MainActivity)
}
```

### Show and dismiss
Basically, when the `PowerSpinnerView` is clicked, the spinner popup will be showed and </br>
when an item is selected, the spinner popup will be dismissed.</br>
But you can show and dismiss manually using methods.

```kotlin
powerSpinnerView.show() // show the spinner popup
powerSpinnerView.dismiss() // dismiss the spinner popup

// If the popup is not showing, shows the spinner popup menu.
// If the popup is already showing, dismiss the spinner popup menu.
powerSpinnerView.showOrDismiss()
```

And you can set the show and dismiss actions using some listener and attributes.
```kotlin
// the spinner popup will not be shown when clicked.
powerSpinnerView.setOnClickListener { }

// the spinner popup will not be dismissed when item selected.
powerSpinnerView.dismissWhenNotifiedItemSelected = false
```

### OnSpinnerItemSelectedListener
Interface definition for a callback to be invoked when selected item on the spinner popup.
```kotlin
setOnSpinnerItemSelectedListener { index, text ->
   toast("$text selected!")
}
```
Here is the java way.
```java
powerSpinnerView.setOnSpinnerItemSelectedListener(new OnSpinnerItemSelectedListener<String>() {
  @Override public void onItemSelected(int position, String item) {
    toast(item + " selected!")
  }
});
```

### SpinnerAnimation
We can customize the showing and dimsmiss animation.
```kotlin
SpinnerAnimation.DROPDOWN
SpinnerAnimation.FADE
SpinnerAnimation.BOUNCE
```

### Customized adapter
We can use our customized adapter and binds to the `PowerSpinnerView`.</br>
The `PowerSpinnerView` provides the spinner popup's recyclerview via `getSpinnerRecyclerView` method.

Here is a sample of the customized adapter.
```kotlin
val adapter = IconSpinnerAdapter(spinnerView)
spinnerView.getSpinnerRecyclerView().adapter = adapter
```

#### IconSpinnerAdapter
Basically, this library provides a customized adapter.</br>
We should create an instance of the `IconSpinnerAdapter` and call `setItems` using a list of `IconSpinnerItem`.

```kotlin
val adapter = IconSpinnerAdapter(spinnerView)
adapter.setItems(
    arrayListOf(
        IconSpinnerItem(ContextCompat.getDrawable(this, R.drawable.ic_dashboard_white_24dp),
          "Item0")))
spinnerView.apply {
  lifecycleOwner = this@MainActivity
  getSpinnerRecyclerView().adapter = adapter
}
```

#### Customized adapter
Here is a way to customize your adapter for binding the `PowerSpinnerView`.
Firstly, create a new adapter and viewHolder extending `RecyclerView.Adapter`.
If you create a new adapter, `OnSpinnerItemSelectedListener` will not work anymore. 
So you should create a field, and invoke it manually.

```kotlin
class MySpinnerAdapter(
  private val spinnerView: PowerSpinnerView
) : RecyclerView.Adapter<MySpinnerAdapter.MySpinnerViewHolder>() {

    private var onSpinnerItemSelectedListener: OnSpinnerItemSelectedListener<IconSpinnerItem>? = null

```
On the customized adapter, you must call `spinnerView.notifyItemSelected` method </br>
when your item is clicked or the spinner item should be changed.

```kotlin
override fun onBindViewHolder(holder: MySpinnerViewHolder, position: Int) {
  holder.itemView.setOnClickListener {
    spinnerView.notifyItemSelected(position, text)
  }
}
```

### Avoid Memory leak
Dialog, PopupWindow and etc.. have memory leak issue if not dismissed before activity or fragment are destroyed.<br>
But Lifecycles are now integrated with the Support Library since Architecture Components 1.0 Stable released.<br>
So we can solve the memory leak issue so easily.<br>

Just use `setLifecycleOwner` method. Then `dismiss` method will be called automatically before activity or fragment would be destroyed.
```java
.setLifecycleOwner(lifecycleOwner)
```

## PowerSpinnerView Attributes
Attributes | Type | Default | Description
--- | --- | --- | ---
spinner_arrow_drawable | Drawable | arrow | arrow drawable.
spinner_arrow_show | Boolean | true | sets the visibility of the arrow.
spinner_arrow_gravity | SpinnerGravity | end | the gravity of the arrow.
spinner_arrow_padding | Dimension | 2dp | padding of the arrow.
spinner_arrow_animate | Boolean | true | show arrow rotation animation when showing.
spinner_arrow_animate_duration | integer | 250 | the duration of the arrow animation.
spinner_divider_show | Boolean | true | show the divider of the popup items.
spinner_divider_size | Dimension | 0.5dp | sets the height of the divider.
spinner_divider_color | Color | White | sets the color of the divider.
spinner_popup_width | Dimension | spinnerView's width | the width of the popup.
spinner_popup_height | Dimension | WRAP_CONTENT | the height of the popup.
spinner_popup_background | Color | spinnerView's background | the background color of the popup.
spinner_popup_animation | SpinnerAnimation | Dropdown | the spinner animation when showing.
spinner_popup_animation_style | Style Resource | -1 | sets the customized animation style.
spinner_popup_elevation | Dimension | 4dp | the elevation size of the popup.
spinner_item_array | String Array Resource | null | sets the items of the popup.
spinner_dismiss_notified_select | Boolean | true | sets dismiss when the popup item is selected.

## Find this library useful? :heart:
Support it by joining __[stargazers](https://github.com/skydoves/PowerSpinner/stargazers)__ for this repository. :star:<br>
And __[follow](https://github.com/skydoves)__ me for my next creations! 🤩

# License
```xml
Copyright 2019 skydoves (Jaewoong Eum)

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the L
