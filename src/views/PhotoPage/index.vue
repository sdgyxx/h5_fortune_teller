<template>
  <div class="home">
    <div class="bg-img">
      <img src="../../assets/images02/photograph/ic_logo2.png" />
    </div>
    <div class="conetnt">
      <p class="title">
        <i class="icon"></i>
        <span>{{ title }}</span>
        <span>{{ title2 }}</span>
      </p>
      <div class="img-Head">
        <van-image radius="6" :src="photo_img" />
      </div>
      <div class="head-icon">
        <p class="title">{{ isTitle }}</p>
        <p class="icon"></p>
      </div>
      <BaseRouterTransition>
        <p class="title tip" v-show="isConfirm">满足以下要求，结果更准确</p>
      </BaseRouterTransition>
      <div class="need" v-show="isConfirm">
        <div class="item">正面</div>
        <div class="item">五官清晰</div>
        <div class="item">不戴眼镜</div>
        <div class="item">面部呈现完整</div>
        <div class="item">无刘海遮挡</div>
      </div>
      <BaseRouterTransition>
        <div class="need-confirm" v-show="!isConfirm">
          <div class="need-confirm-content">
            <p>
              头部姿势：
              <span>非正面</span>
              <img :src="tip_img.warning" alt />
            </p>
            <p>
              左眼状态：
              <span>睁眼，未戴眼镜</span>
              <img :src="tip_img.correct" alt />
            </p>
            <p>
              右眼状态：
              <span>睁眼，未戴眼镜</span>
              <img :src="tip_img.correct" alt />
            </p>
          </div>
        </div>
      </BaseRouterTransition>
      <div class="photograph-btn" v-show="isConfirm">
        <van-uploader class="button" :after-read="afterRead">
          <van-button class="button-div btn_photo_bg" id="btn_photo" type="primary">
            <van-icon color="#84FF00" size="4vw" name="photograph" />拍照/上传照片
          </van-button>
        </van-uploader>
        <p>HTTP://WWW.MYREAL3D.COM</p>
      </div>

      <BaseRouterTransition>
        <div class="bottom_btn" v-show="!isConfirm">
          <div class="button button-conf">
            <van-button
              class="button-div btn_photo_bg btn_photo_color"
              @click="handleBtnAgain"
              type="primary"
            >重新上传</van-button>
            <van-button class="button-div button-cancel btn_photo_bg" @click="handleBtnConfirm">确认上传</van-button>
          </div>
          <p>HTTP://WWW.MYREAL3D.COM</p>
        </div>
      </BaseRouterTransition>
    </div>
  </div>
</template>
<script>
import BaseRouterTransition from '../../components/BaseRouterTransition'
import comm_fun from '../../utils/CommonFunction'
var COS = require('cos-js-sdk-v5')
import { imgCosupload, ImgUrlBeauty } from '../../api/app.js'
import { mapActions, mapState } from 'vuex'
export default {
  data() {
    return {
      title: '测测你颜值',
      title2: '属于哪一种类型?',
      isTitle: '',
      photo_img: require('../../assets/images02/photograph/touxiang.png'),
      isConfirm: true,
      tip_img: {
        correct: 'data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADkAAAA4CAMAAABwmqASAAAAk1BMVEUAAAADxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwUDxwWPcL6XAAAAMHRSTlMA8QX6o03dzunlJh8QCPbWsZQ3KgvtvbacfnJqXllBPjMWx4tvY0guHfOpgnlmU7/6TN/xAAABlUlEQVRIx53W15qCMBQE4NARCypYsGJbuzvv/3T7yeKaclaS/PdDAswJMDtZ3y7X3XmxVXAYApFNsPAATMxznQmepsbBdopKYRo8tvDrYBhcOagFZsF1Dy+lWfCMP4nRa3TwdjIIJjE4Hf3gJgRvox10xxC0tZNTiIa6wQMkS81g2YOk0Gx5CJnmZPtQOK5OcAHCl85gOSD4GskxCKNBc/AOVbzXuM0TsdepVmv76kbph9NlogCyiK5sEAbihZQOXLrk8+/LR/AVkh05SLeqnQ/+UnJfZ+S5lqKSfqjdnBp5nxigIXj0ITvn3lrqUu2hVywj8Bav/UOwV6dvAFFYL7oF76rOUAzZnSiB2vACqlb1toVb6BMVz3wPsrm85Danfxcm6qLiXYb/HsxBS+nKGm9O8uF0k9oS59w+vM9nzsMRh5csKy2JQLuwJrkPSpqzZjeozhnTsfRg++lZO4BUOl2l2OLIZdoSvhS9jDG76N7wD22E2lgvoP74jE7M1MqrTxVzwTP6zWzMAO/IrAywY3Zcv6noP1SEq/OgNc3TAAAAAElFTkSuQmCC',
        warning: 'data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAACwAAAAsCAMAAAApWqozAAAAM1BMVEUAAAD7ZAD6ZAD7ZAD9YwD7ZAD8ZAD5YQD7ZAD7ZAD7ZAD6ZAD7ZAD7ZAD8ZAD7ZAD7ZADc5bCPAAAAEHRSTlMA6gzDN5dwGNC0ot6BWCRIgBgKRwAAAQpJREFUOMu9lNt2hCAMRUmUi4BO/v9rOx2HniG6CH1o91v0cMkWcf8KeyLPk9kkT9JcOsuLPJNd5c3qbFILJzu7yw+72R0hTFaPVT6o42yUjmhrA3lC2xLCYutbMJ8/h5na+KXF0MelzYZVCg+06bDUsTY6Sxrq851dbpW/19Z49OV6q60Rvssgje2afUgDnrGS4tQGHRvqorNBAKnBEpQ2UoeNP2uKd9rQ/4FK6VOvZH/3Cw4HNumoMaIH6IM2A+hLYpKgrYeee96hp9PH+vmKs3G5F6pe8X5vFdpU5zAEDjzUCzJdJ8HegMc3Vb1kUeDe0GRHMg05+QX4m2wWNDgCJ9cXmaD4w/0dXyVhOsjXZsVVAAAAAElFTkSuQmCC',
      },
      file: null
    }
  },
  components: {
    BaseRouterTransition
  },
  computed: {
    ...mapState({
      parmes_url: state => state.app.save_url
    })
  },
  methods: {
    ...mapActions([
      'set_app'
    ]),
    afterRead(file) {
      // 此时可以自行将文件上传至服务器
      // alert(JSON.stringify(file))
      // console.log(file)
      // 在这里应该调用上传接口  做一些用户操作提示性的tip
      this.file = file
      this.ConfirmUpload(file)
    },
    ConfirmUpload(file) {
      console.log(file)
      this.photo_img = file.content
      this.isTitle = '确认照片'
      this.isConfirm = false
    },
    // 确认 上传
    handleBtnConfirm() {
      console.log('上传')
      // this.$router.push({ name: 'analysisnew' })
      // console.log(imgCosupload)
      imgCosupload({ version: 1 }).then(res => {
        const data = res.data
        console.log(data)
        let cos = new COS({
          getAuthorization: function (options, callback) {
            // 异步获取临时密钥
            callback({
              TmpSecretId: data.credentials.tmpSecretId,
              TmpSecretKey: data.credentials.tmpSecretKey,
              XCosSecurityToken: data.credentials.sessionToken,
              ExpiredTime: data.expiredTime
            })
          }
        })
        var file_data = this.file
        var this_ = this
        if (!file_data) return
        cos.putObject({
          Bucket: data.bucket,
          Region: data.region,
          Key: 'h5/ai/beauty/images/' + file_data.file.name,
          Body: file_data.file
        }, function (err, data) {
          if (data) {
            this_.img_success(data)
          }
          console.log(err || data)
        })
      })
    },
    // 上传成功后 通知url到后台
    img_success(data) {
      const image = comm_fun.img_location(data)
      // "version": 1, "data": {"image": "http://domain.jpg"}
      const { channel_id, instance_id, client } = this.parmes_url
      var parmes_data = {
        version: 1,
        data: {
          channel_id, // 渠道id
          instance_id,  // 配置的实例
          image,
          client
        }
      }
      ImgUrlBeauty(parmes_data).then(res => {
        const { instance, original_id, result } = res.data
        this.set_app({
          parmes_data: parmes_data.data,
          instance,
          original_id,
          result
        })
        this.$router.push({ name: 'analysisnew' })
      })
    },
    // 重新上传
    handleBtnAgain() {
      this.isConfirm = true
      this.photo_img = 'http://m3d-storage-dev-1251693531.cos.ap-shanghai.myqcloud.com/h5/ai/beauty/images/touxiang.png'
    }
  }
}
</script>
<style lang="scss" scoped>
.home {
  background: url("../../assets/images02/photograph/ic_bg.jpg") no-repeat;
  background-size: 100%;
  height: 100%;
  overflow: hidden;
  width: 100%;
  color: #fff;
  background-color: #001037;
}
.bg-img {
  z-index: 1;
  padding: 50px 20px;
  text-align: right;
  img {
    height: 80px;
  }
}

.conetnt {
  z-index: 2;
  line-height: 100%;
  // 宽度 计算 710 -减去两个padding值
  width: 568px;
  height: 100%;
  background-color: #000018cc;
  border-radius: 30px 30px 0px 0px;
  margin: 0 auto;
  padding: 46px 53px 0 53px;
  .title {
    font-size: 30px;
    font-weight: 400;
    color: rgba(255, 255, 255, 1);
    line-height: 30px;
    text-align: center;
    margin-bottom: 36px;
    .icon {
      display: inline-block;
      width: 52px;
      height: 56px;
      background: url("../../assets/images02/photograph/ic_bg3.png") no-repeat
        center;
      background-size: 100%;
      float: left;
      position: relative;
      bottom: 10px;
    }
    span {
      font-size: 36px;
      display: inline-block;
      line-height: 36px;
    }
    span:nth-child(3) {
      color: #ff71c1;
    }
    span:nth-child(3) {
      color: #4eb0ff;
    }
  }
  .tip {
    font-size: 30px;
    font-weight: 400;
    line-height: 24px;
    margin: 30px 0 27px 0;
    color: #0096ff;
  }
  .need {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    width: 480px;
    margin: 0 auto;
    .item {
      // height: 62px;
      // line-height: 62px;
      // background: url("../../assets/images/photograph/juxing.png") no-repeat;
      background-size: 100% 100%;
      margin-bottom: 30px;
      font-size: 24px;
      font-weight: 400;
      // color: #008DFF;
    }
    .item:nth-child(1) {
      width: 160px;
    }
    .item:nth-child(2) {
      width: 160px;
    }
    .item:nth-child(3) {
      width: 160px;
    }
    .item:nth-child(4) {
      width: 160px;
    }
    .item:nth-child(5) {
      width: 160px;
    }
  }
  .need-confirm {
    width: 585px;
    height: 253px !important;
    // background: url("../../assets/images/photograph/confirmjuxing.png")
    //   no-repeat 100%;
    background-size: 100% 100%;
    margin-top: -43px;
    .need-confirm-content {
      padding: 46px 124px 45px 91px;
      p {
        text-align: left;
        font-size: 24px;
        font-weight: 400;
        line-height: 24px;
        color: #008dff;
        margin-bottom: 45px;
        span {
          color: #fff;
        }
        img {
          width: 22px;
          height: 22px;
          float: right;
        }
      }
    }
  }
  .button {
    .button-div {
      width: 310px;
      height: 108px;
      border-radius: 16px;
      background: #3e97ff;
      border: 2px solid rgba(62, 151, 255, 1);
      margin-top: 94px;
    }
    .button-cancel {
      margin-top: 30px;
      background: #0f293e;
      color: #c0c0c0;
      border: 2px solid #ff976a;
    }
    .btn_photo_bg {
      border: none;
      margin-top: 60px;
      background: url("../../assets/images02/photograph/tijiaoanniu.png")
        no-repeat center;
      background-size: 100%;
      color: #e6eeff;
    }
  }
  .bottom_btn {
    p {
      font-size: 12px;
      text-align: center;
      color: #71738c;
      font-weight: 400;
      margin-top: 42px;
    }
    .button-conf {
      display: flex;
      justify-content: space-around;
      .button-div {
        margin-top: 32px;
        width: 250px;
      }
      .btn_photo_color {
        color: #7bd5ff;
      }
    }
  }
}
.img-Head {
  margin-bottom: 26px;
}
.head-icon {
  .icon {
    width: 91px;
    height: 6px;
    border-radius: 7px;
    background: #007acf;
    text-align: center;
    margin: 0 auto;
    margin-bottom: 46px;
  }
}
.photograph-btn {
  p {
    font-size: 12px;
    text-align: center;
    color: #71738c;
    font-weight: 400;
    margin-top: 42px;
  }
}
.img-Head .van-image {
  width: 310px;
  height: 310px;
}
</style>