<template>
  <div>
    <div class="box-area" v-loading="loadMinImage" element-loading-text="图片压缩中……">
      <div class="box-title" v-show="!hasImg"></div>
      <img ref="imageBox" class="box-title" v-show="hasImg"/>
      <input type="file" accept="image/png, image/jpeg, image/jpg" @change="fileClick"/>
    </div>
  </div>
</template>

<script>

import {MyImagemin} from "@/assets/js/Common";

export default {
  name: "UploadImage",
  computed: {},
  data() {
    return {
      hasImg: false,
      src: "",
      blob: null,
      loadMinImage: false
    }
  },
  methods: {
    async fileClick(el) {
      let that = this;
      let file = el.target.files[0];
      let img = this.$refs.imageBox;
      this.loadMinImage = true;
      let minImage = await MyImagemin(file);
      if (minImage.code) {
        file = minImage.file
      }
      this.loadMinImage = false;
      img.src = URL.createObjectURL(file);
      img.onload = function () {
        that.hasImg = true;
        let blob = new Blob([file], {type: file.type});
        that.$prompt('可以是图片的网站，可以是作者，可以是图片的链接', '请输入这张图片的来源', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          inputValidator(data) {
            if (data && data.length > 0) {
              return true
            }
            return false
          },
        }).then(({value}) => {
          that.$emit('selectedImage', {
            src: value,
            file: blob
          });
        }).catch(() => {
          that.msgError("取消本次上传")
          that.hasImg = false;
        });
      }
    }
  }
}
</script>

<style lang="less">
.box-area {
  position: relative;
  width: 100px;
  height: 100px;
  margin: auto;

  .box-title, input[type='file'], img {
    position: absolute;
    width: 100%;
    height: 100px;
    left: 0;
    top: 0;
  }

  .box-title {
    border: 1px solid #DCDFE6;
    box-sizing: border-box;
    text-align: center;
    line-height: 100px;

    &::before {
      content: '+';
      color: #DCDFE6;
      font-size: 50px;
    }
  }

  input[type='file'] {
    opacity: 0;
  }
}
</style>
