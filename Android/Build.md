# Build / Deployment


* Configure your build https://developer.android.com/studio/build
* Version you app https://developer.android.com/studio/publish/versioning
* Select Build from menu bar in Android Studio
* Select Generate Signed Bundle / APK
* Select your option
* Create your key store if you need one. This will include selecting location, password to protect it, key alias, password and certificate information
* Export the key if using bundle
* Next
* Select release
* Update .gitignore if exporting keystore and certificate to project folder
* Go to the build and upload to Google Play. Your build will be in app directory. For example `PROJECT_FOLDER/app/release/FILE.aab`