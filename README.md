## react-native-truecaller Features 
* Support both IOS and Android

## Getting started



- `npm install react-native-truecaller --save` or  `yarn add react-native-truecaller`
- for linking both  android and ios run  `react-native link`
- [Link libraries](https://facebook.github.io/react-native/docs/linking-libraries-ios.html)
  Note: the auto link doesn't work with xcworkspace so CocoaPods user needs to do manual linking
  
## Android Configuration
- [Follow steps](https://github.com/truecaller/android-sdk/blob/master/README.md)  https://github.com/truecaller/android-sdk/blob/master/README.md 


## IOS Configuration




You should have:

1. Truecaller app installed
2. App ID in the "Apple development portal". If you do not have App ID yet, then open Project -> Capabilities -> Enable Associated domains. New app id will be automatically created by Xcode.
3. Sign up at https://developer.truecaller.com/sign-up

### Installation

#### Manual Installation

1. Download the project zip file from the [release section](https://github.com/truecaller/ios-sdk/releases)
2. Unzip the file
3. Copy the TruecallerSDK project files into your project (TrueSDK directory, TrueSDKTests directory and TrueSDK.xcodeproj)
4. Drag and drop TrueSDK.xcodeproj into your project (ie add it as a subproject to your main project).
Embedding it this way will not require any additional script to be run.
5. Add the TruecallerSDK framework (from Products output of TrueSDK.xcodeproj) into the Embedded Binaries section of the General tab of your target

NOTE: We recommend using the CocoaPods integration.

#### Installation with CocoaPods

[CocoaPods](http://cocoapods.org) is a dependency manager which automates and simplifies the process of using 3rd-party libraries.

You can install it with the following command:
```bash
$ gem install cocoapods
```
You can create your Podfile using the command (in case you do not already have it):
```bash
$ pod init
```

To integrate TruecallerSDK into your Xcode project using CocoaPods, specify it in your `Podfile`:

```ruby
platform :ios, '8.0'
use_frameworks!

target 'TargetName' do
pod 'TrueSDK'
end
```
Then, run the following command:
```bash
$ pod install
```

### Usage

Add the entry truesdk under LSApplicationQueriesSchemes in into your Info.plist file

```
<key>LSApplicationQueriesSchemes</key>
<array>
<string>truesdk</string>
</array>
```

![Associated domains](https://raw.githubusercontent.com/truecaller/ios-sdk/master/documentation/images/associated-domains.png)

Add the associated domain provided by Truecaller (for example applinks:si44524554ef8e45b5aa83ced4e96d5xxx.truecallerdevs.com) in Your project -> Capabilities > Associated Domains. The prefix 'applinks:' is needed for universal links to function properly. 

**Important:** Replace the '**https://**' part from the provided app link with "**applinks:**". ie _`https://si44524554ef8e45b5aa83ced4e96d5xxx.truecallerdevs.com`_ should become _`applinks:si44524554ef8e45b5aa83ced4e96d5xxx.truecallerdevs.com`_ while adding to entitlements.

(Note that there is **no** _http://_ or _https://_ prefix when setting up the applinks:)





    




1. Import the TruecallerSDK framework in the class where you want to initialize it (for example AppDelegate) and in the class that you want to receive the profile response. Usually, this will be the ViewController responsible for displaying the True Profile info.

    ```objectivec
    #import <TrueSDK/TrueSDK.h>
    ```

2. Check if the current device supports the use of TruecallerSDK and (if so) setup TruecallerSDK. We recommend this to be done in the application:didFinishLaunchingWithOptions:

    ```objectivec
    if ([[TCTrueSDK sharedManager] isSupported]) {
        [[TCTrueSDK sharedManager] setupWithAppKey:<#YOUR_APP_KEY#> appLink:<#YOUR_APP_LINK#>];
        
        
    }
    
    ```
    
    Use the entire associated domain link provided by Truecaller for YOUR_APP_LINK. For example: `https://si44524554ef8e45b5aa83ced4e96d5xxx.truecallerdevs.com` (including https://).
    
    **Important:** Make sure you type the YOUR_APP_KEY and YOUR_APP_LINK fields correctly. If you mistype the YOUR_APP_LINK field, the permission screen in Truecaller will be shown and immediatelly dismissed. In this case, the SDK will not be able to send a corresponding error back to your app.

3. In AppDelegate implement the method application:continueUserActivity:restorationHandler: and call the corresponding method of the [TCTrueSDK sharedManager]. If the method returns false that means the activity need not be addressed by TruecallerSDK and you can handle it as desired.

    ```objectivec
    - (BOOL)application:(UIApplication *)application continueUserActivity:(NSUserActivity *)userActivity restorationHandler:(void (^)(NSArray *restorableObjects))restorationHandler {
        return [[TCTrueSDK sharedManager] application:application continueUserActivity:userActivity restorationHandler:restorationHandler];
    }
    ```
### Example

- [Sample APP](https://github.com/truecaller/ios-sdk)
``` sample
import TRUECALLER,{TRUECALLEREvent} from "react-native-truecaller"


type Props = {};
export default class App extends Component<Props> 
{
  constructor(props) 
  {
    super(props);
    this.state = { name :"", phone:"", address:"", email:"", job:"", company:"", countryCode:"", gender:"", url:avatar, };
      this.onButtonPress=this.onButtonPress.bind(this)
  }
  componentDidMount()
  {
    TRUECALLER.initializeClient();
  }
  onButtonPress()
  {
    TRUECALLER.requestTrueProfile();
    TRUECALLER.on(TRUECALLEREvent.TrueProfileResponse, (profile) => 
    {
      
      this.setState({
        name:profile.firstName+" "+profile.lastName,
        phone:profile.phoneNumber,
        address:profile.city,
        email:profile.email,
        job:profile.jobTitle,
        company:profile.companyName,
        countryCode:profile.countryCode,
        gender:profile.gender,
        url:{uri:profile.avatarUrl}
      })
    });
    TRUECALLER.on(TRUECALLEREvent.TrueProfileResponseError, (error) => 
    {
    });
  }
 ```
- `TRUECALLER.initializeClient()` (set delegate in ios and set callback in android)
- `TRUECALLER.requestTrueProfile()` this is method to request profile form truecaller app
-  `TRUECALLER.on(TRUECALLEREvent.TrueProfileResponse, (profile) => 
    {
    }` after fetching profile callback will call in both android and ios 
- `TRUECALLER.on(TRUECALLEREvent.TrueProfileResponseError, (error) => 
    {
    }); ` this callback will call if any error occurs  
- we call access all `profile  value` here the details 
  ```
  profile.firstName  
  profile.lastName
  profile.phoneNumber
  profile.gender
  profile.street
  profile.city
  profile.zipcode
  profile.countryCode
  profile.facebookId
  profile.twitterId
  profile.email
  profile.url
  profile.avatarUrl
  profile.isTrueName
  profile.companyName
  profile.jobTitle
  profile.payload
  profile.signature **
  profile.signatureAlgorithm **
  profile.requestNonce
  profile.isSimChanged
  profile.verificationMode
  
  ** is not available  in ios instead we will get empty string
  ```
  


