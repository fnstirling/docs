# Status Bar

Programmatically hide the status bar in a fragment
* [Hide the status bar](https://developer.android.com/training/system-ui/status)
* [Hide status bar in only one fragment and display it on the others](https://stackoverflow.com/q/33094185) Stack Overflow, 13 October 2015
```
/*
 * Hide the status bar on this view
 * Using conditional logic because API is deprecated
 * Hiding on R or below - https://developer.android.com/training/system-ui/status
 * One option for hiding on below R - https://stackoverflow.com/questions/33094185/hide-status-bar-in-only-one-fragment-and-display-it-on-the-others#:~:text=4%20Answers&text=Its%20always%20better%20to%20define,show%20the%20status%20bar%20again.
 * Hiding on R or above - https://stackoverflow.com/questions/62577645/android-view-view-systemuivisibility-deprecated-what-is-the-replacement
 * Conditional rendering - https://stackoverflow.com/questions/24287658/if-else-statement-based-on-os-version-in-android
 */
// if (Build.VERSION.SDK_INT <= Build.VERSION_CODES.R) {
//     activity?.window?.decorView?.systemUiVisibility = View.SYSTEM_UI_FLAG_FULLSCREEN
// } else {
//     activity?.window?.setDecorFitsSystemWindows(false)
// }
```

Programmatically change the status bar background color and light and dark mode (text color)
* [Android M Light and Dark status bar programmatically - how to make it dark again?](https://stackoverflow.com/a/39596725) - Stack Overflow, 20 September 2016
```
/*
 * Change the status bar background color and the light and dark mode
 * https://stackoverflow.com/a/39596725
 */
activity?.let {
    /*
     * Change status bar to dark mode (for light text color)
     */
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
        var flags = it.window.decorView.systemUiVisibility // get current flag
        flags = flags xor View.SYSTEM_UI_FLAG_LIGHT_STATUS_BAR // use XOR here for remove LIGHT_STATUS_BAR from flags
        it.window.decorView.systemUiVisibility = flags
    }

    /*
     * Change status bar to light mode (for dark text color)
     */
    // if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
    //     var flags = it.window.decorView.systemUiVisibility // get current flag
    //     flags = flags or View.SYSTEM_UI_FLAG_LIGHT_STATUS_BAR // add LIGHT_STATUS_BAR to flag
    //     it.window.decorView.systemUiVisibility = flags
    // }

    /*
     * Change status bar background color
     */
    it.window?.statusBarColor = ContextCompat.getColor(it, R.color.melo_purple)
}
```