---
title: "React Native SDK Reference"
excerpt: ""
---
<Callout intent="alert">
  <Callout.Title>SDK Version Compatibility</Callout.Title>
   <Callout.Description>The latest version of the SDK is compatible with Xcode 10.2.1/10.3 and React Native 0.61.2. For compatibility with earlier React Native versions, please refer to older SDK versions.
</Callout.Description>
</Callout>
This reference guide documents all of the methods available in our React Native SDK, and explains in detail how these methods work. If you want to dig even deeper, our SDKs are open source-- head to our [React Native SDK GitHub repository](https://github.com/launchdarkly/react-native-client-sdk) to look under the hood. Additionally you can clone and run a [sample application](https://github.com/launchdarkly/hello-react-native) using this SDK.
## Getting Started

<Callout intent="warning">
  <Callout.Title>Expo Usage</Callout.Title>
   <Callout.Description>The LaunchDarkly React Native Client SDK does not work with Expo because the SDK uses native modules. Please consider using [ExpoKit](https://docs.expo.io/versions/latest/expokit/expokit/)
</Callout.Description>
</Callout>
Building on top of our [Quickstart](./getting-started) guide, the following steps will get you started with using the LaunchDarkly SDK in your React Native code.

The first step is to install the LaunchDarkly SDK as a dependency. Add the LaunchDarkly npm dependency to your project and link it to your React Native project.
[block:code]
{
  "codes": [
    {
      "code": "npm install --save launchdarkly-react-native-client-sdk\ncd ios/\npod install\n",
      "language": "shell",
      "name": "Installing with npm"
    }
  ]
}
[/block]
You will need a `Podfile` in your `ios/` directory. Once you've written your `Podfile`, run `pod install` in the `ios/` directory. This SDK currently requires Xcode 10.2.1 (it is compatible with Xcode 10.3) and is compiled against Swift 5.
[block:code]
{
  "codes": [
    {
      "code": "platform :ios, '9.0'\nrequire_relative '../node_modules/@react-native-community/cli-platform-ios/native_modules'
target 'YOUR_MODULE_NAME' do\n  # Pods for ReactNativeRestwrapper\n  pod 'FBLazyVector', :path => \"../node_modules/react-native/Libraries/FBLazyVector\"\n  pod 'FBReactNativeSpec', :path => \"../node_modules/react-native/Libraries/FBReactNativeSpec\"\n  pod 'RCTRequired', :path => \"../node_modules/react-native/Libraries/RCTRequired\"\n  pod 'RCTTypeSafety', :path => \"../node_modules/react-native/Libraries/TypeSafety\"\n  pod 'React', :path => '../node_modules/react-native/'\n  pod 'React-Core', :path => '../node_modules/react-native/'\n  pod 'React-CoreModules', :path => '../node_modules/react-native/React/CoreModules'\n  pod 'React-Core/DevSupport', :path => '../node_modules/react-native/'\n  pod 'React-RCTActionSheet', :path => '../node_modules/react-native/Libraries/ActionSheetIOS'\n  pod 'React-RCTAnimation', :path => '../node_modules/react-native/Libraries/NativeAnimation'\n  pod 'React-RCTBlob', :path => '../node_modules/react-native/Libraries/Blob'\n  pod 'React-RCTImage', :path => '../node_modules/react-native/Libraries/Image'\n  pod 'React-RCTLinking', :path => '../node_modules/react-native/Libraries/LinkingIOS'\n  pod 'React-RCTNetwork', :path => '../node_modules/react-native/Libraries/Network'\n  pod 'React-RCTSettings', :path => '../node_modules/react-native/Libraries/Settings'\n  pod 'React-RCTText', :path => '../node_modules/react-native/Libraries/Text'\n  pod 'React-RCTVibration', :path => '../node_modules/react-native/Libraries/Vibration'\n  pod 'React-Core/RCTWebSocket', :path => '../node_modules/react-native/'
  pod 'React-cxxreact', :path => '../node_modules/react-native/ReactCommon/cxxreact'\n  pod 'React-jsi', :path => '../node_modules/react-native/ReactCommon/jsi'\n  pod 'React-jsiexecutor', :path => '../node_modules/react-native/ReactCommon/jsiexecutor'\n  pod 'React-jsinspector', :path => '../node_modules/react-native/ReactCommon/jsinspector'\n  pod 'ReactCommon/jscallinvoker', :path => \"../node_modules/react-native/ReactCommon\"\n  pod 'ReactCommon/turbomodule/core', :path => \"../node_modules/react-native/ReactCommon\"\n  pod 'Yoga', :path => '../node_modules/react-native/ReactCommon/yoga'
  pod 'DoubleConversion', :podspec => '../node_modules/react-native/third-party-podspecs/DoubleConversion.podspec'\n  pod 'glog', :podspec => '../node_modules/react-native/third-party-podspecs/glog.podspec'\n  pod 'Folly', :podspec => '../node_modules/react-native/third-party-podspecs/Folly.podspec'
  #launchdarkly-react-native-client-sdk dependencies\n  use_frameworks!\n  pod 'LaunchDarkly', '4.1.2'
  use_native_modules!\nend
",
      "language": "text",
      "name": "Podfile"
    }
  ]
}
[/block]
Once the SDK is downloaded and saved in `package.json` and the other install steps are complete, you'll need to import it into your project:
[block:code]
{
  "codes": [
    {
      "code": "import LDClient from 'launchdarkly-react-native-client-sdk';",
      "language": "javascript"
    }
  ]
}
[/block]
Once the SDK is installed and imported, you'll want to create a single, shared instance of LDClient, passing in the client and user configuration objects. To create a client instance, you need your environment's mobile key (available on your [account settings page](https://app.launchdarkly.com/settings#/projects)). Mobile keys are not secret-- they can be safely exposed in your client-side code.
[block:code]
{
  "codes": [
    {
      "code": "let client = new LDClient();
let clientConfig =\n    { \"mobileKey\": \"YOUR_MOBILE_KEY\",\n      \"stream\": true,\n      \"offline\": false\n    };
let userConfig = { \"key\": \"user_key\" };
await client.configure(clientConfig, userConfig);",
      "language": "javascript"
    }
  ]
}
[/block]

<Callout intent="info">
  <Callout.Title>Mobile Keys</Callout.Title>
   <Callout.Description>Be sure to use a mobile key from your [Environments](https://app.launchdarkly.com/settings#/environments) page. Never embed a server-side SDK key into a mobile application.</Callout.Description>
</Callout>

## Client Configuration Options

<Callout intent="info">
  <Callout.Title>Default Values</Callout.Title>
   <Callout.Description>This complete config object shows all possible config options in their default form. Do not change the defaults unless you are required to.
</Callout.Description>
</Callout>
Here is a complete client configuration object utilizing all available fields:
[block:code]
{
  "codes": [
    {
      "code": "let clientConfig =\n  { \"mobileKey\": \"YOUR_MOBILE_KEY\",\n    \"baseUri\": \"https://app.launchdarkly.com\",\n    \"streamUri\": \"https://clientstream.launchdarkly.com\",\n    \"eventsUri\": \"https://mobile.launchdarkly.com\",\n    \"eventsCapacity\": 200,\n    \"eventsFlushIntervalMillis\": 5000,\n    \"connectionTimeoutMillis\": 3000,\n    \"pollingIntervalMillis\": 4000,\n    \"backgroundPollingIntervalMillis\": 6000,\n    \"useReport\": false,\n    \"stream\": true,\n    \"disableBackgroundUpdating\": false,\n    \"offline\": false,\n    \"debugMode\": true\n  };",
      "language": "javascript"
    }
  ]
}
[/block]

## User Configuration Options
Here is a complete user configuration object utilizing all available fields:
[block:code]
{
  "codes": [
    {
      "code": "let userConfig =\n    { key: '2fbfa269b78e24c8e967f482b89c939b',\n     \tname: 'grace_hopper',\n     \tfirstName: 'Grace', \n     \tlastName: 'Hopper', \n     \temail: 'gracehopper@example.net', \n     \tanonymous: false, \n     \tprivateAttributeNames: ['name'], \n     \tcustom: {'groups': 'users'}\n    };\n  ",
      "language": "javascript"
    }
  ]
}
[/block]
Let's walk through this code. The first key in the object is the user's key-- in this case we've used the value `2fbfa269b78e24c8e967f482b89c939b`. The user key is the only mandatory user attribute. The key should also uniquely identify each user. You can use any value such as a primary key, an e-mail address, or a hash, as long as the same user always has the same key. We recommend using a hash if possible.

All of the other keys (like firstName, email, and the custom attributes) are optional. The attributes you specify will automatically appear on the LaunchDarkly application dashboard, meaning that you can start segmenting and targeting users with these attributes.

In addition to built-in attributes like names and e-mail addresses, you can pass us any of your own user data by passing custom attributes, like the groups attribute in the example above. Custom attributes are one of the most powerful features of LaunchDarkly. They let you target users according to any data that you want to send to us-- organizations, groups, account plans-- anything you pass to us becomes available instantly on your dashboard.

Private attributes allow you to specify keys you do not want to be sent to the LaunchDarkly servers. Private user attributes can be used for targeting purposes, but are removed from the user data sent back to LaunchDarkly. When this user is sent back to LaunchDarkly, the `name` attribute will be removed.

The `anonymous` key allows you to distinguish logged out or unregistered users while still using LaunchDarkly. Anonymous users work just like regular users, except that they won't appear on your Users page in LaunchDarkly.
## Variations
The variation method determines whether a flag is enabled or not for a specific user. In React Native, there is a variation method for each type (e.g. `boolVariation`,` stringVariation`):
[block:code]
{
  "codes": [
    {
      "code": "let jsonResult = await client.jsonVariation('MY_JSON_FLAG_KEY', {});",
      "language": "javascript"
    }
  ]
}
[/block]
Variation calls take the feature flag key and a fallback value.

The fallback value will only be returned if an error is encountered-- for example, if the feature flag key doesn't exist or the user doesn't have a key specified.

The variation call will automatically create a user in LaunchDarkly if a user with that user key doesn't exist already. There's no need to create users ahead of time (but if you do need to, take a look at `identify`).
<Callout intent="info">
  <Callout.Title>Handling flag values on initial application launch</Callout.Title>
   <Callout.Description>When LDClient is initialized for the first time at app launch, users will receive feature flag fallback values until an initial connection to LaunchDarkly is completed for the first time. Take a look at the section above on various ways to initialize the client.

## All Flags

<Callout intent="alert">
  <Callout.Title>Creating Users</Callout.Title>
   <Callout.Description>Note that unlike `variation` and `identify` calls, `allFlags` does not send events to LaunchDarkly. Thus, users are not created or updated in the LaunchDarkly dashboard.
</Callout.Description>
</Callout>
The `allFlags` method returns an object containing flag keys and values.
[block:code]
{
  "codes": [
    {
      "code": "let allFlagsResult = client.allFlags();\nallFlagsResult.then(values => { \n  console.log(values);\n});",
      "language": "javascript"
    }
  ]
}
[/block]

## Track
The `track` method allows you to record actions your users take in your app. In the LaunchDarkly dashboard, you can tie these events to goals in A/B tests. You can also attach custom JSON data to your event by passing an extra parameter to `track`. Here's a simple example:


[block:code]
{
  "codes": [
    {
      "code": "client.track('MY_GOAL_FLAG_KEY', false);\nclient.track('MY_GOAL_FLAG_KEY_WITH_DATA', {'some_data':'value'});",
      "language": "javascript"
    }
  ]
}
[/block]

## Offline Mode
In some situations, you might want to stop making remote calls to LaunchDarkly and switch to the last known values for your feature flags. Offline mode lets you do this easily. 
[block:code]
{
  "codes": [
    {
      "code": "let offlineResult = client.setOffline(); //true or false\nlet offlineStatus = client.isOffline(); //true or false\nlet onlineResult = client.setOnline(); //true or false",
      "language": "javascript"
    }
  ]
}
[/block]

<Callout intent="info">
<Callout.Title>Lack of Network Connectivity</Callout.Title>
   <Callout.Description>If a user's device is in airplane/flight mode or if they are not connected to a network, LaunchDarkly will use the latest stored flag settings in memory. If there are no previously stored flag settings, then the fallback values will be used.</Callout.Description>

</Callout>

## Flush
Internally, the LaunchDarkly SDK keeps an event buffer for `track` calls. These are flushed periodically in a background thread. In some situations (for example, if you're testing out the SDK in a simulator), you may want to manually call `flush` to process events immediately.


[block:code]
{
  "codes": [
    {
      "code": "client.flush();",
      "language": "text"
    }
  ]
}
[/block]

## Identify
If your app is used by multiple users on a single device, you may want to change users and have separate flag settings for each user. To achieve this, the SDK will store the last 5 user contexts on a single device, with support for switching between different user contexts.

You can use the `identify` method to switch user contexts:
[block:code]
{
  "codes": [
    {
      "code": "let user = {'key': 'minimal_user'};\nawait client.identify(user); //promise resolves to nil/null\nAlert.alert('identify', 'success');",
      "language": "javascript"
    }
  ]
}
[/block]
The `identify` call will load any saved flag values for the new user and immediately trigger an update of the latest flags from LaunchDarkly.
## Real-time Updates
LaunchDarkly manages all flags for a user context in real-time by updating flags based on a real-time event stream. When a flag is modified via the LaunchDarkly dashboard, the flag values for the current user will update almost immediately.

To accomplish real-time updates, LaunchDarkly broadcasts an event stream that is listened to by the React Native SDK. Whenever an event is performed on the dashboard, the React Native SDK is notified of the updated flag settings in real-time.

To perform real-time updates in your app, your app will need to register listeners for each flag you'd like to watch:
[block:code]
{
  "codes": [
    {
      "code": "if (this.state.listeners.hasOwnProperty(key))\n  return;\nlet listener = value => Alert.alert('Listener Callback', value);\nclient.registerFeatureFlagListener('MY_LISTEN_FLAG_KEY', listener);\nthis.setState({listeners: {...this.state.listeners, ...{['MY_LISTEN_FLAG_KEY']: listener}}});
",
      "language": "javascript"
    }
  ]
}
[/block]
Similarly you can unregister listeners to disable them:
[block:code]
{
  "codes": [
    {
      "code": "this.state.ldClient.unregisterFeatureFlagListener('MY_LISTEN_FLAG_KEY', this.state.listeners['MY_LISTEN_FLAG_KEY']);\nlet {['MY_LISTEN_FLAG_KEY']: omit, ...newListeners} = this.state.listeners;\nthis.setState({listeners: newListeners});
",
      "language": "javascript"
    }
  ]
}
[/block]

## Background Fetch
The React Native SDK defaults to the background fetch behavior for the platform it's running on.