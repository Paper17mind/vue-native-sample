<template>
  <view class="container">
    <status-bar :background-color="color" bar-style="dark-content" />
    <ScrollView>
      <flat-list :data="contents" :render-item="item => renderItem(item)" />
      <activity-indicator size="large" :color="theme.COLORS.VUE" />
    </ScrollView>
  </view>
</template>

<script>
import {Block, Button, Input, NavBar, Text} from 'galio-framework';
import theme from '../theme';
import {
  StyleSheet,
  Dimensions,
  ScrollView,
  SafeAreaView,
  FlatList,
  Image,
} from 'react-native';
import {Card, Caption, Avatar, Title} from 'react-native-paper';

import * as React from 'react';
import axios from 'axios';
const {height, width} = Dimensions.get('window');
export default {
  props: {
    navigation: {type: Object},
  },
  components: {
    Block,
    Text,
  },
  data() {
    return {
      text: '',
      theme,
      color: theme.COLORS.VUE,
      contents: require('../assets/home.json'),
    };
  },
  methods: {
    changeRoute(params) {
      this.navigation.navigate('Details', {
        slug: params.slug,
        info: {
          cover: params.cover_photo.urls.regular,
          color: params.cover_photo.color,
          description: params.cover_photo.description,
        },
      });
    },
    renderItem({item, index}) {
      if (index == 0) this.color = item.cover_photo.color;
      let avatars = item.preview_photos.map((x, i) =>
        this.renderPreview(x, i, index),
      );
      const fc = item.cover_photo.color === '#262626' ? '#FFFFFF' : '#000000';
      return (
        <Card
          key={`${index}_${item.id}`}
          elevation={1}
          mode="elevated"
          onPress={() => this.changeRoute(item)}
          style={{
            backgroundColor: item.cover_photo.color,
            paddingBottom: 10,
          }}>
          <Card.Title
            titleStyle={{
              fontSize: 15,
              color: fc,
            }}
            subtitleStyle={{
              fontSize: 13,
              color: fc,
              flexWrap: 'wrap',
            }}
            title={item.title}
            subtitle={item.description}
            right={props => <Caption>{`${item.total_photos} Photos`}</Caption>}
          />
          <Card.Cover source={{uri: item.cover_photo.urls.thumb}} />
          <ScrollView showsHorizontalScrollIndicator={false} horizontal={true}>
            {avatars}
          </ScrollView>
        </Card>
      );
    },
    renderPreview(item, id, index) {
      return (
        <Card.Cover
          key={`${id},${index},${item.id}`}
          style={{width: 150}}
          source={{uri: item.urls.thumb}}
        />
      );
    },
    async getData() {
      const res = await axios.post(theme.api, {
        target: `${theme.url}/topics/`,
      });
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
