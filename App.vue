<template>
  <app-navigator></app-navigator>
</template>

<script>
import {
  createAppContainer,
  createStackNavigator,
  createDrawerNavigator
} from "vue-native-router";

import HomeScreen from "./screens/HomeScreen.vue";
import About from "./screens/About.vue";
import Country from "./screens/Country.vue";

const DrawerNavigator = createDrawerNavigator(
  {
    Home: HomeScreen,
    About: About,
  },
  {
    initialRouteName: 'Home',
    contentOptions: {
      activeTintColor: "#e91e63"
    },
  }
);

const StackNavigator = createStackNavigator(
  {
    Drawer: DrawerNavigator,
    Home: HomeScreen,
    About: About,
    Country: Country,
  },{
    initialRouteName: 'Drawer',
    headerMode: 'none'
  }
);

const AppNavigator = createAppContainer(StackNavigator);

export default {
  components: { AppNavigator },
}
</script>