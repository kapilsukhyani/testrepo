# **Atlassian MobileKit Profiles Module**
Integrate this library in your Android app to show User Profile Cards.

## **Features**
* Provides simple api to display profile cards.


## **Requirements**
* This library requires at minimum Android 4.1 , [API Level 16][1]
* It has these external gradle dependencies
```
'com.android.support:appcompat-v7:24.1.1',
'com.android.support:design:24.1.1',
'com.atlassian.mobilekit.infrastructure:sawyer-java:1.0.0',
'com.atlassian.mobilekit.infrastructure:sawyer-android:1.0.0',
'com.facebook.shimmer:shimmer:0.1.0@aar'
```



## **Download**
Make sure you have the latest version of MobileKit gradle plugin integrated. Please refer to [this page](https://bitbucket.org/atlassianmobile/mobilekit-plugin) for further instruction.
Add the following to your app's `build.gradle` file
```groovy
compile (mobilekit.modules.profilesAndroid) {
    exclude group: "com.facebook.shimmer"
}
compile 'com.facebook.shimmer:shimmer:0.1.0@aar'
```

As per [Shimmer Library documentation][2] the gradle dependency must refer to an 'aar' file.
Due to this, the consuming app also needs to explicitly specify it and exclude its reference from the mobilekit dependency.
_
_## **Integration Guide**

### **Basic Setup**

**Step-1:** Profile module relies on the theme defined by AtlasKit i.e. AtlasKit.Base to get values defined for these theme attributes: colorPrimary, colorPrimaryDark and colorAccent.

**Note:** Atlassian apps should use AtlasKit.Base to have ADG3 compliant colors for the same theme attributes.



**Step-2:** Implement a ProfileFetcher that is responsible to download and provide a `Profile` for a given `profileId`

~~~~java
public interface ProfileFetcher {

    Future<Profile> fetchProfile(@NonNull String profileId);
}
~~~~

**Step-3:** In your activity or fragment implement `OnProfileActionClickListener` 
in order to handle action button click events. The calls will be directed with actual `Profile` instance
and corresponding `@StringRes` identifier specified during the action button configuration
~~~~java
public class SampleFragment extends Fragment implements OnProfileActionClickListener{

    @Override
    public void onProfileActionClicked(int actionResId, Profile profile) {
        Toast.makeText(getActivity(), actionResId, Toast.LENGTH_SHORT).show();
    }
}
~~~~

**Step-4:** Construct `ProfileCard` with the help of `ProfileCardBuilder` as following.

~~~~java
public class SampleFragment extends Fragment implements OnProfileActionClickListener{

    public void showProfileCard() {
        ProfileCard card = ProfileCardBuilder.with(this)
                .withProfileConfig(id, new MockProfileFetcherFactory())
                .withPrimaryButton(R.string.mention)
                .withSecondaryButton(R.string.open_chat)
                .build();
        card.startDisplay(getActivity());
    }
}
~~~~
**NOTE** at the moment you need to specify `ProfileFetcherFactory` explicitly with `profileId` parameter because there is no
final version of default `ProfileFetcher` implementation exists.

### **Handling Configuration Changes & Activity Restore**

* The ProfileCard is a DialogFragment that is attached to an activity.
* If the activity is destroyed due to config changes such as device rotation or if the OS destroys and recreates it, the App should update the ProfileCard.
* Current implementation takes care of it for you, however you should be aware of any additional your application specific cases.

 [1]: https://developer.android.com/guide/topics/manifest/uses-sdk-element.html#ApiLevels
 [2]: http://facebook.github.io/shimmer-android/