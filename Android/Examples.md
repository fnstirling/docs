# Examples

Hide the status bar. Put this in `onViewCreated` in Fragment or Activity.
```kotlin
/*
 * Hide the status bar on this view
 * Using conditional logic because API is deprecated
 * Hiding on R or below - https://developer.android.com/training/system-ui/status
 * One option for hiding on below R - https://stackoverflow.com/questions/33094185/hide-status-bar-in-only-one-fragment-and-display-it-on-the-others#:~:text=4%20Answers&text=Its%20always%20better%20to%20define,show%20the%20status%20bar%20again.
 * Hiding on R or above - https://stackoverflow.com/questions/62577645/android-view-view-systemuivisibility-deprecated-what-is-the-replacement
 * Conditional rendering - https://stackoverflow.com/questions/24287658/if-else-statement-based-on-os-version-in-android
 */
if (Build.VERSION.SDK_INT <= Build.VERSION_CODES.R) {
    activity?.window?.decorView?.systemUiVisibility = View.SYSTEM_UI_FLAG_FULLSCREEN
} else {
    activity?.window?.setDecorFitsSystemWindows(false)
}
```

Change status bar color. Put this in `onViewCreated` in Fragment or Activity
```kotlin
/*
 * Change the status bar background color and the light and dark mode
 * https://stackoverflow.com/a/39596725
 *
 * TODO: Implement R version and above method for this
 * TODO: Put in own class with public static methods, see link above for reference to this
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
	if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
	    var flags = it.window.decorView.systemUiVisibility // get current flag
	    flags = flags or View.SYSTEM_UI_FLAG_LIGHT_STATUS_BAR // add LIGHT_STATUS_BAR to flag
	    it.window.decorView.systemUiVisibility = flags
	}

	/*
	 * Change status bar background color
	 */
	it.window?.statusBarColor = ContextCompat.getColor(it, R.color.melo_purple)
}
```

Change the status bar text. Put this in `onCreate` in the Fragment or Activity.
```kotlin
/*
 * Change status bar text
 * https://stackoverflow.com/q/38382283
 */
try {
    if (Build.VERSION.SDK_INT >= 28) {
        val window: Window = window
        window.addFlags(WindowManager.LayoutParams.FLAG_DRAWS_SYSTEM_BAR_BACKGROUNDS)
        window.setStatusBarColor(ContextCompat.getColor(this, android.R.color.white))
    }
} catch (e: Exception) {
    e.printStackTrace()
}
```

Disable dark mode. Put this in `onCreate` in the Fragment or Activity.
```kotlin
/*
 * Disable dark mode
 * https://stackoverflow.com/q/57175226
 */
AppCompatDelegate.setDefaultNightMode(AppCompatDelegate.MODE_NIGHT_NO)
```

Hide the action bar. Put this in `onCreate` in the Fragment or Activity
```kotlin
/*
 * Hide the action bar
 * https://stackoverflow.com/a/36236700
 * Alternatively https://stackoverflow.com/q/2591036
 */
if (supportActionBar != null) {
	supportActionBar?.hide()
}
```

Set the main activity layout on the entrance. Put this in `onCreate` in the Fragment or Activity
```kotlin
/*
 * Set the main activity layout as the entrance
 */
setContentView(R.layout.main_activity)
```

Loading Fragment and ViewModel
```kotlin
// Fragment
package app.getmelo.android.ui.loading

import android.content.Context
import android.net.ConnectivityManager
import android.os.Build
import android.os.Bundle
import android.os.Handler
import android.os.Looper
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import androidx.core.content.ContextCompat
import androidx.fragment.app.Fragment
import androidx.lifecycle.ViewModelProvider
import androidx.navigation.Navigation
import app.getmelo.android.App
import app.getmelo.android.R
import app.getmelo.android.networking.NetworkStatusChecker

class LoadingFragment : Fragment() {

    companion object {
        fun newInstance() = LoadingFragment()
    }

    private lateinit var viewModel: LoadingFragmentViewModel
    private val remoteApi = App.remoteApi
    private val networkStatusChecker by lazy {
        NetworkStatusChecker(activity?.getSystemService(ConnectivityManager::class.java))
    }

    // Inflates the layout
    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View {
        return inflater.inflate(R.layout.app_loading_fragment, container, false)
    }

    // Called immediately after onCreateView
    override fun onViewCreated(
        view: View,
        savedInstanceState: Bundle?
    ) {
        super.onViewCreated(view, savedInstanceState)
        /*
         * Change the status bar background color and the light and dark mode
         * https://stackoverflow.com/a/39596725
         *
         * TODO: Implement R version and above method for this
         * TODO: Put in own class with public static methods, see link above for reference to this
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

        // Out view model needs context so must see if the activity is present
        // Activity inherits from Context
        // If we didn't need Context, i.e. normal ViewModel class, we wouldn't need to wrap in activity?.let {}
        // Would just need to call viewModel = ViewModelProvider(this).get(AppLoadingViewModel::class.java)
        activity?.let {
            // Use get() with the class of the model we are getting
            viewModel = ViewModelProvider(this).get(LoadingFragmentViewModel::class.java)
        }

        /*
         * Trigger a navigation action (i.e. change view) after a few seconds
         */
        Handler(Looper.getMainLooper()).postDelayed({
            Navigation.findNavController(view).navigate(R.id.welcomeFragment);
        }, 2000);
    }

    override fun onActivityCreated(
        savedInstanceState: Bundle?
    ) {
        super.onActivityCreated(savedInstanceState)
        viewModel = ViewModelProvider(this).get(LoadingFragmentViewModel::class.java)
        // TODO: Use the ViewModel
    }

    override fun onAttach(
        context: Context
    ) {
        super.onAttach(context)
    }

    override fun onDetach() {
        super.onDetach()
    }

}


// View model
package app.getmelo.android.ui.loading

import android.app.Application
import androidx.lifecycle.AndroidViewModel
import app.getmelo.android.managers.AuthenticationDataManager

/**
 * Using AndroidViewModel because we need a reference to context in the view model.
 * We will be using shared preferences as well as moving fragments so will be using context.
 *
 * Loads in the AuthenticationDataManager to manage shared preferences.
 */
class LoadingFragmentViewModel(application: Application): AndroidViewModel(application) {
    // Property for context
    private val context = application.applicationContext
    // Additional properties
    private val authenticationDataManager = AuthenticationDataManager(context)
}
```

Loading saved instance state. Put this in `onCreate` in Fragment or Activity.
```kotlin
// if (savedInstanceState == null) {
//     supportFragmentManager.beginTransaction()
//             .replace(R.id.container, AppLoadingFragment.newInstance())
//             .commitNow()
// }
```

Data manager for authentication.
```kotlin
package app.getmelo.android.managers

import android.content.Context
import androidx.preference.PreferenceManager

/**
 * Using Shared Preferences (shared prefs) to store the token data.
 * Shared prefs uses key value pairs to store data.
 * Use apply() to save the data.
 * Use to retrieve the data.
 *
 * Loading in the androidx preference manager.
 * https://developer.android.com/jetpack/androidx/migrate/class-mappings
 * Add `implementation 'androidx.preference:preference:1.1.1'` dependency into module grade file
 * Then re-sync grade file
 *
 * Not applicable here but preference manager doesn't save sets.
 * So if working with arrays need to convert arrays to sets and then save.
 * Please see Save Preferences section in Your Second Kotlin Android App on raywenderlich.
 */
class AuthenticationDataManager(private val context: Context) {
    companion object {
        private const val ACCESS_TOKEN_KEY = "accessToken"
    }

    /**
     * Saves user token to default shared preferences.
     */
    fun saveUserToken(token: String) {
        val sharedPreferences = PreferenceManager.getDefaultSharedPreferences(context).edit()
        sharedPreferences.putString(ACCESS_TOKEN_KEY, token)
        sharedPreferences.apply()
    }

    /**
     * Reads user token from default shared preferences.
     *
     * @return String representation of user token.
     */
    fun readUserToken(): String {
        val sharedPreferences = PreferenceManager.getDefaultSharedPreferences(context)
        // Get the contents, which is a map of keys and values
        val contents = sharedPreferences.all

        for (content in contents) {
            if (content.key == ACCESS_TOKEN_KEY) {
                return content.value as String
            }
        }

        return ""
    }
}
```

Trigger a navigation change after a delay. Trigger an action after a delay.
```kotlin
/*
 * Trigger a navigation action (i.e. change view) after a few seconds
 */
Handler(Looper.getMainLooper()).postDelayed({
	Navigation.findNavController(view).navigate(R.id.welcomeFragment);
}, 2000);
```