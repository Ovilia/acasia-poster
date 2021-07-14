<template>
  <el-row type="flex" class="poster-container" justify="center">
    <el-col :span="12">
      <div id="poster-preview">
        <img class="poster-template" src="poster-template.png">
        <div class="poster-content">
          <div class="title">{{ title }}</div>
          <div class="name" :style="{'font-size': 3.7 * nameFontSize + 'vh'}">{{ name }}</div>
          <div class="topic" :style="{'font-size': 2.3 * topicFontSize + 'vh'}">{{ topic }}</div>
          <div class="time">{{ time }}</div>
          <img class="keynote" src="keynote.png" v-if="isKeynote">
        </div>
      </div>
    </el-col>
    <el-col :span="8" class="poster-control">
      <el-form>
        <el-form-item label="讲师姓名">
          <el-input v-model="name" />
        </el-form-item>
        <el-form-item label="讲师职位">
          <el-input v-model="title" />
        </el-form-item>
        <el-form-item label="演讲题目">
          <el-checkbox v-model="isKeynote">主题演讲</el-checkbox>
          <el-input v-model="topic" />
        </el-form-item>
        <el-form-item label="演讲时间">
          <el-input v-model="time" />
        </el-form-item>
        <el-form-item label="字号调整（讲师姓名）">
          <el-input-number size="medium" v-model="nameFontSize" :min="0.5" :max="2" :step="0.1" label="倍率"></el-input-number>
        </el-form-item>
        <el-form-item label="字号调整（演讲题目）">
          <el-input-number size="medium" v-model="topicFontSize" :min="0.5" :max="2" :step="0.1" label="倍率"></el-input-number>
        </el-form-item>

        <el-form-item>
          <el-button type="primary" @click="download()">生成海报</el-button>
        </el-form-item>

        <el-form-item class="info">
          <i class="el-icon-service"></i> 本工具由 <a href="http://github.com/Ovilia">@Ovilia</a> 开发，<a href="mailto:oviliazhang@gmail.com">问题反馈</a>
        </el-form-item>
      </el-form>
    </el-col>
  </el-row>
</template>

<script lang="ts">
import Vue from 'vue'

export default Vue.extend({
  data() {
    return {
      title: 'Apache ECharts VP',
      name: '羡辙',
      topic: 'Dirty-rectangle Rendering with Apache ECharts',
      time: '2021.8.6 14:10 GMT+8',
      isKeynote: false,

      nameFontSize: 1,
      topicFontSize: 1
    };
  },

  mounted() {
  },

  methods: {
    download() {
      alert('开发中！');
    }
  }
})
</script>

<style>
@font-face {
  font-family: 'Open Sans';
  font-style: normal;
  font-weight: bold;
  src: url(~assets/OpenSans-Bold.ttf) format('truetype');
}
@font-face {
  font-family: 'Open Sans';
  font-style: normal;
  font-weight: normal;
  src: url(~assets/OpenSans-Light.ttf) format('truetype');
}
@font-face {
  font-family: 'SourceHanSerifSC';
  font-style: normal;
  font-weight: 300;
  src: url(~assets/SourceHanSerifSC-Heavy.otf) format('opentype');
}

.poster-container {
  position: absolute;
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
  font-family: 'Open Sans';
}

  #poster-preview {
    position: relative;
    width: max-content;
  }

    .poster-template {
      height: 100vh;
    }

    .poster-content {
      position: absolute;
      left: 0;
      right: 0;
      top: 0;
      bottom: 0;
      padding: 37vh 3vh 0 3vh;
      text-align: center;
      color: #fff;
      font-size: 1vh;
    }

      .title {
        font-weight: normal;
        color: #FFE342;
      }

      .name {
        margin: 0 0 0.5vh 0;
        color: #FFE342;
        font-family: 'SourceHanSerifSC', 'Open Sans';
        font-size: 3.7vh;
      }

      .topic {
        margin-bottom: 0.5vh;
        font-size: 2.3vh;
        font-weight: bold;
      }

      .time {
        font-size: 0.9vh;
        color: #ccc;
      }

      .keynote {
        display: block;
        margin: 0.5vh auto;
        height: 5vh;
      }

.info, .info a {
  color: #aaa;
}
</style>
