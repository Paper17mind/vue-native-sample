<template>
  <view>
    <ScrollView :showsHorizontalScrollIndicator="false" :horizontal="true">
      <flat-list
        horizontal
        :data="users"
        :render-item="(item) => renderUsers(item)"
        :onEndReachedThreshold="1.0"
      />
    </ScrollView>
    <ScrollView>
      <flat-list
        :data="contents"
        :render-item="(item) => renderList(item)"
        :onEndReachedThreshold="1.0"
      />
      <activity-indicator
        size="large"
        :style="{marginTop: 10}"
        v-if="loading"
        :color="theme.COLORS.VUE"
        animated
      />
      <button
        v-if="!loading"
        :on-press="() => getData(page, slug)"
        title="Load More"
        color="#42b983"
        :style="{borderRadius: '20px'}"
        accessibility-label="Load more photos"
      />
      <text v-if="!loading" :style="{marginBottom: 30, marginTop: 20}">
        Click Load more buttom to load more photos from this user
      </text>
    </ScrollView>
  </view>
</template>

<script>
import {DeckSwiper, Block} from 'galio-framework';
import {
  View,
  Text,
  StyleSheet,
  Image,
  Button,
  ScrollView,
  Dimensions,
} from 'react-native';
import {
  List,
  Card,
  Caption,
  Paragraph,
  Avatar,
  Surface,
} from 'react-native-paper';
import collect from 'collect.js';
import moment from 'moment';
import React from 'react';
import theme from '../theme';
import axios from 'axios';
export default {
  props: {
    navigation: {type: Object},
  },
  components: {
    DeckSwiper,
    Block,
    Card,
    Surface,
  },
  data() {
    return {
      theme,
      height: Dimensions.height,
      color: theme.COLORS.NAVBAR,
      styles: StyleSheet.create({
        social: {
          width: theme.SIZES.BASE * 15.5,
          height: theme.SIZES.BASE * 3.5,
          borderRadius: 10,
          justifyContent: 'center',
        },
      }),
      page: 1,
      contents: [],
      loading: false,
      hasNext: true,
    };
  },
  computed: {
    info() {
      return this.navigation.state.params.info;
    },
    slug() {
      return this.navigation.state.params.slug;
    },
    users() {
      return collect(this.contents).pluck('user').unique('id').all();
    },
  },
  mounted() {
    const slug = this.navigation.state.params.slug;
    const info = this.navigation.state.params.info;
    // this.getSummary(slug);
    this.getData(this.page, slug);
  },
  methods: {
    goToHomeScreen() {
      this.navigation.navigate('Home');
    },
    showProfile(user) {
      console.log(`navigation to ${user.name}`);
      this.navigation.navigate('Profile', {
        id: user.id,
        user: user,
      });
    },
    renderHead({item}) {
      let avatars = item.top_contributors.map((x) => (
        <Card.Cover
          style={{width: 150}}
          source={{uri: x.profile_image.medium}}
        />
      ));
      return (
        <Card
          elevation={1}
          mode="elevated"
          style={{
            backgroundColor: this.info.color,
            paddingBottom: 10,
          }}>
          <Card.Title
            titleStyle={{
              fontSize: 15,
            }}
            subtitleStyle={{
              fontSize: 13,
              flexWrap: 'wrap',
            }}
            title={item.title}
            subtitle={item.description}
            right={(props) => (
              <Caption>{`${item.total_photos} Photos`}</Caption>
            )}
          />
          <Card.Cover source={{uri: item.cover_photo.urls.thumb}} />
          <Card.Title
            titleStyle={{
              fontSize: 15,
            }}
            subtitleStyle={{
              fontSize: 13,
            }}
            title={item.cover_photo.user.name}
            subtitle={`${item.cover_photo.user.bio}`}
            right={(props) => (
              <Caption>{`Country: ${item.cover_photo.user.location}`}</Caption>
            )}
            left={(props) => (
              <Avatar.Image
                source={{uri: item.cover_photo.user.profile_image.small}}
              />
            )}
          />
          <ScrollView showsHorizontalScrollIndicator={false} horizontal={true}>
            {avatars}
          </ScrollView>
        </Card>
      );
    },
    renderUsers({item}) {
      return (
        <Card
          style={{marginHorizontal: 5, padding: 0, height: 63, width: 63}}
          mode="elevated"
          onPress={() => this.showProfile(item)}>
          <Avatar.Image
            source={{uri: item.profile_image.small}}
            size={58}
            onPress={() => this.showProfile(item)}
          />
          <Caption>{`${item.name.substr(0, 8)}...`}</Caption>
        </Card>
      );
    },
    renderList({item, index}) {
      // if (index == 0 && item) this.color = item.cover_photo.color;
      const topic = Object.keys(item.topic_submissions)[0];
      return (
        <Card
          elevation={1}
          mode="elevated"
          onPress={() => this.showProfile(item.user)}>
          <Card.Cover source={{uri: item.urls.thumb}} />
          <Card.Title
            subtitle={item.user.name || item.description}
            title={topic}
            right={(props) => (
              <Caption>{moment(item.updated_at).fromNow()}</Caption>
            )}
            left={(props) => <Caption>{`Liked by ${item.likes}`}</Caption>}
          />
        </Card>
      );
    },
    async getSummary(slug) {
      const res = await axios
        .post(theme.api, {
          target: `${theme.url}/topics/${slug}/`,
        })
        .catch((e) => console.error(e));
      console.log(res.data.slug);
      if (res.data.slug) this.summary = res.data;
    },
    async getData(page = 1, slug) {
      if (!this.hasNext || this.loading) return;
      this.loading = true;
      console.log(`Fetching content >>>>>>>>>>>>>>>>>>>>>>>>>>> page-${page}`);
      const res = await axios
        .post(theme.api, {
          target: `${theme.url}/topics/${slug}/photos?per_page=12&page=${page}`,
        })
        .catch((e) => console.error(e));
      if (page > 1) {
        console.log(res);
        this.contents.push(...res.data);
      } else this.contents = res.data;
      if (res.data.length < 12) this.hasNext = false;
      this.page += 1;
      this.loading = false;
    },
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
