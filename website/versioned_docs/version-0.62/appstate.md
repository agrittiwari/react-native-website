---
id: appstate
title: AppState
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

`AppState` can tell you if the app is in the foreground or background, and notify you when the state changes.

AppState is frequently used to determine the intent and proper behavior when handling push notifications.

### App States

- `active` - The app is running in the foreground
- `background` - The app is running in the background. The user is either:
  - in another app
  - on the home screen
  - [Android] on another `Activity` (even if it was launched by your app)
- [iOS] `inactive` - This is a state that occurs when transitioning between foreground & background, and during periods of inactivity such as entering the Multitasking view or in the event of an incoming call

For more information, see [Apple's documentation](https://developer.apple.com/documentation/uikit/app_and_scenes/managing_your_app_s_life_cycle)

## Basic Usage

To see the current state, you can check `AppState.currentState`, which will be kept up-to-date. However, `currentState` will be null at launch while `AppState` retrieves it over the bridge.

<Tabs groupId="syntax" queryString defaultValue={constants.defaultSyntax} values={constants.syntax}>
<TabItem value="functional">

```SnackPlayer name=AppState%20Function%20Component%20Example
import React, { useEffect, useState } from "react";
import { AppState, StyleSheet, Text, View } from "react-native";

const AppStateExample = () => {
  const [appState, setAppState] = useState(AppState.currentState);

  useEffect(() => {
    AppState.addEventListener("change", _handleAppStateChange);

    return () => {
      AppState.removeEventListener("change", _handleAppStateChange);
    };
  }, []);

  const _handleAppStateChange = nextAppState => {
    if (appState.match(/inactive|background/) && nextAppState === "active") {
      console.log("App has come to the foreground!");
    }
    setAppState(nextAppState);
  };

  return (
    <View style={styles.container}>
      <Text>Current state is: {appState}</Text>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: "center",
    alignItems: "center"
  }
});

export default AppStateExample;
```

</TabItem>
<TabItem value="classical">

```SnackPlayer name=AppState%20Class%20Component%20Example
import React, { Component } from "react";
import { AppState, StyleSheet, Text, View } from "react-native";

export default class AppStateExample extends Component {
  state = {
    appState: AppState.currentState
  };

  componentDidMount() {
    AppState.addEventListener("change", this._handleAppStateChange);
  }

  componentWillUnmount() {
    AppState.removeEventListener("change", this._handleAppStateChange);
  }

  _handleAppStateChange = nextAppState => {
    if (
      this.state.appState.match(/inactive|background/) &&
      nextAppState === "active"
    ) {
      console.log("App has come to the foreground!");
    }
    this.setState({ appState: nextAppState });
  };

  render() {
    return (
      <View style={styles.container}>
        <Text>Current state is: {this.state.appState}</Text>
      </View>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: "center",
    alignItems: "center"
  }
});
```

</TabItem>
</Tabs>

This example will only ever appear to say "Current state is: active" because the app is only visible to the user when in the `active` state, and the null state will happen only momentarily.

---

# Reference

## Events

### `change`

This event is received when the app state has changed. The listener is called with one of [the current app state values](appstate#app-states).

### `memoryWarning`

This event is used in the need of throwing memory warning or releasing it.

### `focus`

[Android only] Received when the app gains focus (the user is interacting with the app).

### `blur`

[Android only] Received when the user is not actively interacting with the app. Useful in situations when the user pulls down the [notification drawer](https://developer.android.com/guide/topics/ui/notifiers/notifications#bar-and-drawer). `AppState` won't change but the `blur` event will get fired.

## Methods

### `addEventListener()`

```jsx
addEventListener(type, handler);
```

Add a handler to AppState changes by listening to the `change` event type and providing the handler

TODO: now that AppState is a subclass of NativeEventEmitter, we could deprecate `addEventListener` and `removeEventListener` and use `addListener` and `listener.remove()` directly. That will be a breaking change though, as both the method and event names are different (addListener events are currently required to be globally unique).

---

### `removeEventListener()`

```jsx
removeEventListener(type, handler);
```

Remove a handler by passing the `change` event type and the handler

## Properties

### `currentState`

```jsx
AppState.currentState;
```
