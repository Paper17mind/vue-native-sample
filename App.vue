<template>
  <router-views></router-views>
</template>

<script>
import React from 'react';
import {
  createAppContainer,
  createStackNavigator,
  HeaderTitle,
} from 'vue-native-router';
import {NavigationContainer} from '@react-navigation/native';
import {Appbar} from 'react-native-paper';

import HomeScreen from './screens/HomeScreen.vue';
import DetailsScreen from './screens/DetailsScreen.vue';
import ProfileScreen from './screens/tabs/profile';
import theme from './theme';

function CustomNavigationBar() {
  return (
    <Appbar.Header>
      <Appbar.Content title="My awesome app" />
    </Appbar.Header>
  );
}
const StackNavigator = createStackNavigator(
  {
    Home: {
      screen: HomeScreen,
      options: {
        title: 'My home',
        headerStyle: {
          backgroundColor: '#f4511e',
        },
        headerTintColor: '#fff',
        headerTitleStyle: {
          fontWeight: 'bold',
        },
      },
    },
    Details: {
      screen: DetailsScreen,
      headerMode: theme.COLORS.VUE,
    },
    Profile: ProfileScreen,
  },
  {
    initialRouteName: 'Home',
    contentOptions: {
      activeTintColor: theme.COLORS.VUE,
    },
    headerMode: theme.COLORS.VUE,
  },
);
const AppNavigator = createAppContainer(StackNavigator);

export default {
  components: {
    RouterViews: AppNavigator,
  },
};
</script>
<style>
.container {
  align-items: flex-start;
  justify-content: center;
  flex: 1;
  flex-direction: row;
}
</style>
