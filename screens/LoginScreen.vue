<template>
  <view class="container">
    <status-bar
      :background-color="theme.COLORS.VUE"
      bar-style="light-content"
    />
    <Block>
      <image
        :style="{marginLeft: 'auto', marginRight: 'auto'}"
        :source="require('../assets/logo.png')"
      />
      <text :style="{fontSize: 20, textAlign: 'center'}">
        Please Login Here
      </text>
      <Input rounded placeholder="Email" :style="{width: width * 0.9}" />
      <Input
        rounded
        password
        viewPass
        placeholder="Password"
        :style="{width: width * 0.9}"
      />
      <button
        :on-press="changeRoute"
        title="Learn More"
        color="#42b983"
        :style="{borderRadius: '20px'}"
        accessibility-label="Learn more about this purple button"
      />
    </Block>
    <!-- <activity-indicator size="large" color="#0000ff" /> -->
  </view>
</template>

<script>
import {Block, Button, Input, NavBar, Text} from 'galio-framework';
import theme from '../theme';
import {
  StyleSheet,
  Platform,
  Dimensions,
  KeyboardAvoidingView,
} from 'react-native';
import * as React from 'react';
const {height, width} = Dimensions.get('window');
export default {
  props: {
    navigation: {type: Object},
  },
  components: {
    Block,
    Input,
    NavBar,
    GButton: Button,
    Text,
    KeyboardAvoidingView,
  },
  data() {
    return {
      text: '',
      theme,
      height,
      width,
      styles: StyleSheet.create({
        container: {
          flex: 1,
          alignItems: 'center',
          justifyContent: 'space-around',
          paddingTop: theme.SIZES.BASE * 0.3,
          paddingHorizontal: theme.SIZES.BASE,
          backgroundColor: theme.COLORS.WHITE,
        },
        social: {
          width: theme.SIZES.BASE * 3.5,
          height: theme.SIZES.BASE * 3.5,
          borderRadius: theme.SIZES.BASE * 1.75,
          justifyContent: 'center',
        },
      }),
    };
  },
  methods: {
    changeRoute() {
      this.navigation.navigate('Details');
    },
  },
};
</script>

<style>
.container {
  align-items: center;
  justify-content: center;
  flex: 1;
  flex-direction: row;
}
</style>
