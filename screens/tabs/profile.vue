<template>
  <view>
    <ScrollView :horizontal="true">
      <button v-for="x in contents" :key="x.id" :title="x.id">
        {{ x.id }}
      </button>
    </ScrollView>
    <ScrollView :style="{paddingBottom: 70}">
      <flat-list
        :data="[user.profile_image, user]"
        :render-item="(item) => renderUser(item)"
      />

      <flat-list :data="contents" :render-item="(item) => renderList(item)" />
      <activity-indicator
        size="large"
        :style="{marginTop: 10}"
        v-if="loading"
        :color="theme.COLORS.VUE"
        animated
      />
      <button
        v-if="!loading"
        :on-press="() => getProfile(page)"
        title="Load More"
        color="#42b983"
        :style="{borderRadius: 20}"
        accessibility-label="Load more photos"
      />
      <text v-if="!loading" :style="{marginBottom: 20, marginTop: 20}">
        Click Load more buttom to load more photos from this user
      </text>
    </ScrollView>
  </view>
</template>
<script>
import React from 'react';
import axios from 'axios';
import moment from 'moment';
import theme from '../../theme';
import {NavBar, Block} from 'galio-framework';
import {StyleSheet, Image} from 'react-native';
import {List, Card, Caption, Paragraph} from 'react-native-paper';
// import {Fo} from "react-native-vector-icons";

export default {
  name: 'Profile',
  props: {
    navigation: Object,
  },
  components: {
    Block,
    Card,
    List,
  },
  data() {
    return {
      theme,
      contents: [],
      page: 1,
      loading: false,
      hasMore: true,
    };
  },
  computed: {
    user() {
      return this.navigation.state.params.user;
    },
  },
  mounted() {
    this.getProfile(this.page);
  },
  methods: {
    renderList({item}) {
      const topic = Object.keys(item.topic_submissions)[0];
      return (
        <Card elevation={1} mode="elevated">
          <Card.Cover source={{uri: item.urls.thumb}} />
          <Card.Title
            titleStyle={{fontSize: 15}}
            title={topic || item.user.name}
            subtitle={item.description || `${item.likes} Liked`}
            right={(props) => (
              <Caption>{moment(item.updated_at).fromNow()}</Caption>
            )}
          />
        </Card>
      );
    },
    renderUser({item}) {
      if (item.id) {
        return (
          <Card elevation={1} mode="elevated">
            <Card.Title
              title={item.name}
              subtitle={`${item.total_photos} Photos, ${item.total_collections} Collections`}
            />
            <Card.Content>
              <Paragraph>{`Liked: ${item.total_likes}`}</Paragraph>
              <Paragraph>{`Instagram Account: @${item.social.instagram_username}`}</Paragraph>
              <Paragraph>{`Twitter Account: @${item.social.twitter_username}`}</Paragraph>
              <Caption>{item.bio}</Caption>
            </Card.Content>
          </Card>
        );
      }
      return <Card.Cover source={{uri: item.large}} />;
    },
    async getProfile(page = 1) {
      if (!this.hasMore || this.loading) return;
      this.loading = true;
      const res = await axios.post(theme.api, {
        target: `${theme.url}/users/${this.user.username}/photos?per_page=20&order_by=latest&page=${page}`,
      });
      if (page > 1) this.contents.push(...res.data);
      else this.contents = res.data;
      if (res.data.length < 12) this.hasNext = false;
      this.page += 1;
      this.loading = false;
    },
  },
};
</script>

<style scoped></style>
