<template>
  <el-row type="flex" class="poster-container" justify="center">
    <el-col :span="12">
      <div id="poster-preview"
        :style="{'cursor': imageUrl ? 'move' : 'default'}"
        @mousedown="mouseDown($event)"
        @mousemove="mouseMove($event)"
        @mouseup="mouseUp($event)"
      >
        <img class="author-img" v-if="imageUrl" :src="imageUrl" ref="avatar"
          :style="{'width': avatarPos.width + 'vh', 'height': avatarPos.height + 'vh', 'left': avatarPos.left + 'vh', 'top': avatarPos.top + 'vh'}"
        >
        <img class="poster-template" :src="'poster-template-' + lang + '.png'">
        <div class="poster-content">
          <div class="title">{{ title }}</div>
          <div class="name" :style="{'font-size': 3.7 * nameFontSize + 'vh'}">{{ name }}</div>
          <div class="topic" :style="{'font-size': 2.3 * topicFontSize + 'vh'}">{{ topic }}</div>
          <div class="time">{{ time }}</div>
          <img class="keynote" :src="'keynote-' + lang + '.png'" v-if="isKeynote">
        </div>
      </div>
    </el-col>
    <el-col :span="8" class="poster-control">
      <el-row>
        <h1>ApacheCon Asia 2021 海报生成器</h1>
        <el-radio-group size="small" v-model="lang">
          <el-radio-button label="zh"></el-radio-button>
          <el-radio-button label="en"></el-radio-button>
        </el-radio-group>
      </el-row>
      <el-form v-loading="isDownloading"
        element-loading-text="生成海报中"
      >
        <el-form-item label="讲师姓名">
          <el-input v-model="name" />
        </el-form-item>
        <el-form-item label="讲师职位">
          <el-input v-model="title" />
        </el-form-item>
        <el-form-item label="讲师照片">
          <el-col :span="3">
            <input type="file" ref="avatarInput" style="display:none"
              @change="avatarChange"
            />
            <a class="avatar-uploader" href="javascript:;" @click="setAvatar()">
              <i class="el-icon-plus avatar-icon"></i>
            </a>
          </el-col>
          <el-col :span="12" class="icon-panel">
            <a href="javascript:;" @click="zoomIn()">
              <i class="el-icon-zoom-in avatar-icon" v-if="imageUrl"></i>
            </a>
            <a href="javascript:;" @click="zoomOut()">
              <i class="el-icon-zoom-out avatar-icon" v-if="imageUrl"></i>
            </a>
            <a href="javascript:;" @click="zoomReset()">
              <i class="el-icon-refresh-left avatar-icon" v-if="imageUrl"></i>
            </a>
          </el-col>
        </el-form-item>
        <el-form-item label="演讲题目">
          <el-checkbox v-model="isKeynote">主题演讲</el-checkbox>
          <el-input v-model="topic" />
        </el-form-item>
        <el-form-item label="演讲时间">
          <el-input v-model="time" />
        </el-form-item>
        <el-form-item label="字号调整（讲师姓名）">
          <div :span="12">
            <a href="javascript:;" @click="nameFontAdd()">
              <i class="el-icon-zoom-in avatar-icon"></i>
            </a>
            <a href="javascript:;" @click="nameFontMinus()">
              <i class="el-icon-zoom-out avatar-icon"></i>
            </a>
            <a href="javascript:;" @click="nameFontReset()">
              <i class="el-icon-refresh-left avatar-icon"></i>
            </a>
          </div>
        </el-form-item>
        <el-form-item label="字号调整（演讲题目）">
          <div :span="12">
            <a href="javascript:;" @click="topicFontAdd()">
              <i class="el-icon-zoom-in avatar-icon"></i>
            </a>
            <a href="javascript:;" @click="topicFontMinus()">
              <i class="el-icon-zoom-out avatar-icon"></i>
            </a>
            <a href="javascript:;" @click="topicFontReset()">
              <i class="el-icon-refresh-left avatar-icon"></i>
            </a>
          </div>
        </el-form-item>

        <el-form-item>
          <el-button type="primary" @click="download()"
          >
            生成海报
          </el-button>
        </el-form-item>

        <el-form-item class="info">
          <i class="el-icon-service"></i> 本工具由 <a href="http://github.com/Ovilia">@Ovilia</a> 开发，<a href="mailto:oviliazhang@gmail.com">问题反馈</a>
        </el-form-item>
      </el-form>
    </el-col>
  </el-row>
</template>

<script lang="ts">
import Vue from 'vue';
// @ts-ignore
import domtoimage from 'retina-dom-to-image';

export default Vue.extend({
  data() {
    return {
      imageUrl: null as unknown as string,
      title: 'Apache ECharts VP',
      name: '羡辙',
      topic: 'Dirty-rectangle Rendering with Apache ECharts',
      time: '2021.8.6 14:10 GMT+8',
      isKeynote: false,
      avatarInput: null,

      nameFontSize: 1,
      topicFontSize: 1,

      avatarDefaultPos: {
        width: 0,
        height: 0,
        top: 0,
        left: 0
      },
      avatarPos: {
        width: 0,
        height: 0,
        top: 0,
        left: 0
      },
      avatarZoom: 1,
      isMouseDown: false,
      mouseX: 0,
      mouseY: 0,
      isDownloading: false,
      posterBase64: '',

      lang: 'zh'
    };
  },

  mounted() {
  },

  methods: {
    download() {
      this.isDownloading = true;
      domtoimage.toJpeg(document.getElementById('poster-preview'))
        .then((url: string) => {
          this.posterBase64 = url;
          this.isDownloading = false;
          const link = document.createElement('a');
          link.href = url;
          link.download = this.name + '.jpeg';
          link.click();
        })
    },

    mouseDown(event: MouseEvent) {
      if (!this.imageUrl) {
        return;
      }
      this.isMouseDown = true;
      this.mouseX = event.pageX;
      this.mouseY = event.pageY;
    },

    mouseMove(event: MouseEvent) {
      if (!this.isMouseDown) {
        return;
      }
      const ratio = 100 / window.innerHeight;

      this.avatarPos.left += (event.pageX - this.mouseX) * ratio;
      this.avatarPos.top += (event.pageY - this.mouseY) * ratio;
      this.mouseX = event.pageX;
      this.mouseY = event.pageY;
    },

    mouseUp(event: MouseEvent) {
      this.isMouseDown = false;
    },

    zoomIn() {
      this.avatarZoom += 0.05;
      this.avatarPos.width = this.avatarDefaultPos.width * this.avatarZoom;
      this.avatarPos.height = this.avatarDefaultPos.height * this.avatarZoom;
    },

    zoomOut() {
      this.avatarZoom -= 0.05;
      this.avatarPos.width = this.avatarDefaultPos.width * this.avatarZoom;
      this.avatarPos.height = this.avatarDefaultPos.height * this.avatarZoom;
    },

    zoomReset() {
      this.avatarZoom = 1;
      this.avatarPos.width = this.avatarDefaultPos.width;
      this.avatarPos.height = this.avatarDefaultPos.height;
      this.avatarPos.left = this.avatarDefaultPos.left;
      this.avatarPos.top = this.avatarDefaultPos.top;
    },

    nameFontAdd() {
      this.nameFontSize += 0.1;
    },

    nameFontMinus() {
      this.nameFontSize -= 0.1;
    },

    nameFontReset() {
      this.nameFontSize = 1;
    },

    topicFontAdd() {
      this.topicFontSize += 0.1;
    },

    topicFontMinus() {
      this.topicFontSize -= 0.1;
    },

    topicFontReset() {
      this.topicFontSize = 1;
    },

    setAvatar() {
      if (this.$refs.avatarInput) {
        (this.$refs.avatarInput as HTMLElement).click();
      }
    },

    // @ts-ignore
    avatarChange(event) {
      const file = event.target.files && event.target.files.length ? event.target.files[0] : null;
      if (file) {
        this.imageUrl = window.URL.createObjectURL(file);

        const img = new Image();
        img.onload = () => {
          const h = 417 * 100 / 2208;
          const w = h / img.height * img.width;
          const top = 373 / 2208 * 100;
          const pw = 100 / 2208 * 1242;
          const left = (pw - w) / 2;
          this.avatarDefaultPos.width = this.avatarPos.width = w;
          this.avatarDefaultPos.height = this.avatarPos.height = h;
          this.avatarDefaultPos.left = this.avatarPos.left = left;
          this.avatarDefaultPos.top = this.avatarPos.top = top;
        };
        img.src = this.imageUrl;
      }
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

h1 {
  margin-bottom: 15px;
  font-size: 20px;
  font-family: 'SourceHanSerifSC', 'Open Sans';
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
    overflow: hidden;
    -webkit-user-select: none;
    user-select: none;
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

      .author-img {
        position: absolute;
        z-index: -10;
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

.el-form-item {
  margin-bottom: 5px;
}

.avatar-uploader {
  display: inline-block;
  margin-top: 10px;
  width: 40px;
  height: 40px;
  border: 1px dashed #d9d9d9;
  border-radius: 6px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
}

.avatar-uploader:hover {
  border-color: #409EFF;
}

.avatar-icon {
  font-size: 22px;
  color: #ccc;
  width: 40px;
  height: 40px;
  line-height: 40px;
  text-align: center;
}

.icon-panel {
  margin-top: 12px;
}

.el-upload:hover .avatar-icon, a:hover .avatar-icon {
  color: #409EFF;
}

.avatar {
  width: 40px;
  height: 40px;
  display: block;
}

.info, .info a {
  color: #aaa;
}
</style>
