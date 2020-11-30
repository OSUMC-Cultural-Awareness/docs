# Material Design

The recommended option for implementing material design in the app is React Native Paper as it has the features we want, is still being supported, and is compatible with technology currently being used.

## Link to Paper homepage

https://callstack.github.io/react-native-paper/index.html

## Installation
In the terminal:
```
yarn add react-native-paper
```
Make sure that babel.config.js includes babel-preset-expo. This should be correct already.
```
module.exports = function(api) {
  api.cache(true);
  return {
    presets: ['babel-preset-expo'],
  };
};
```
Then you must import the modules that you wish to use in the .tsx files. Ex:
```
import { Provider as PaperProvider } from 'react-native-paper';
```
