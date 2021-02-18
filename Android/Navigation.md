# Navigation

## Navigation Graph

### Implementation
Add the following to the module gradle file dependences
```
implementation 'androidx.navigation:navigation-fragment-ktx:2.3.3'
implementation 'androidx.navigation:navigation-ui-ktx:2.3.3'
```

Add new Android Resource File to resources `res` folder (right click or control click).

Name file something like `nav_graph` and select `Navigation` as the Resource type

Add nav host in the main activity layout file with something like the following
```
<fragment
    android:id="@+id/nav_host_fragment"
    android:name="androidx.navigation.fragment.NavHostFragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:defaultNavHost="true"
    app:navGraph="@navigation/nav_graph" />
```

Go back to navigation graph in the Navigation folder in Resources `res` and you should see the host there.
Add and entry point to the navigation.
Add other fragments and connections.

Load in the navigation controller in the main activity class with something similar to the below.
Be sure to change the id if different.
`Navigation.findNavController(this, R.id.nav_host_fragment)`

To call navigation changes between fragments get the action of the connection in the navigation graph and use code similar to
```
view?.let {
    it.findNavController().navigate(R.id.action)
}
```

## Resources

* [Avoiding Conditional Navigation Pitfalls When Implementing the Android Navigation Library](https://doordash.engineering/2020/08/04/implementing-the-android-navigation-library/) - Doordash Engineering, 4 August 2020
* [Conditional Navaigation](https://developer.android.com/guide/navigation/navigation-conditional) - Android
* [Navigating navigation](https://www.youtube.com/watch?t=284&v=09qjn706ITA&feature=youtu.be) - Android, 23 July 2020
* [Handle Complex Navigation Flow with Single-Activity Architecture and Android Jetpack’s Navigation component](https://proandroiddev.com/handle-complex-navigation-flow-with-single-activity-and-android-jetpacks-navigation-component-6ad988602902) - 8 May 2019 