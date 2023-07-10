<template>
  <div class="batch-upload-container">
      <!-- <div > -->
        <el-upload
          class="upload-batch"
          ref="batchUpload"
          :show-file-list="false"
          :on-change="submitFile"
          :on-success="uploadSuccess"
          :on-error="uploadError"
          :before-upload="beforeUpload"
          drag
          :limit="1"
          :action="uploadUrl"
          :auto-upload="false"
          :http-request="uploadCatch"
        >
          <i class="el-icon-plus" style="font-size: 3em"></i>
          <div class="el-upload__text">将语音文件拖到此处，或<em>点击上传</em></div>
          <div class="el-upload__tip" slot="tip">文件大小 ≤ 10Mb</div>
        </el-upload>
      <!-- </div> -->
  </div>
</template>
<script>
// import api from "../../../comm/api/bpmapi";
import { post } from '@/lin/plugin/axios'
export default {
  name: "batchUploads",
  props: {
    type: {
      type: String,
      default: "",
    },
    url: {
      type: String,
      default: "",
    },

    date: {
      type: String,
      dafault: true,
    },
  },
  data() {
    return {
      isExitFile: false,
      loading: false,
      dloading: false,
      file: undefined
    };
  },
  computed: {
    uploadUrl() {
      return 'http://101.34.40.125:5000/'
    },
  },
  mounted() {
  },
  methods: {
    /**
     * 上传缓存中的图片
     * @param {Array} uploadList 需要上传的缓存集合, 集合中包含回调函数
     */
    uploadCatch() {
      const uploadList = this.file
      const data = {}
      uploadList.forEach((item, index) => {
        data[`file_${index}`] = item.raw
      })
      return post('cms/file', data)
        .then(res => {
          if (!Array.isArray(res) || res.length === 0) {
            throw new Error('音频上传失败')
          }

         this.$emit('successHandel', res[0])
        })
        .catch(err => {
          let msg = '音频上传失败, 请重试'
          this.$emit('errorHandel')
          if (err.message) {
            // eslint-disable-next-line
            msg = err.message
          }
          console.error(err)
          this.$message.error(msg)
        })
    },
    close() {
      this.$emit("close");
    },
    submitFile(file, fileList) {
      this.file = fileList
      this.$refs.batchUpload.submit()
    },
    uploadSuccess(res) {
      this.$refs.batchUpload && this.$refs.batchUpload.clearFiles();
      if (Array.isArray(res) && res.length) {
        this.$message({
          message: `上传文件成功`,
          type: "success",
        });
        // this.close();
        // this.$emit("sucess");
      } else {
        this.$message({
          message: res.info,
          type: "error",
        });
      }
      this.dloading = false;
    },
    uploadError() {
      this.dloading = false;
      this.$refs.batchUpload && this.$refs.batchUpload.clearFiles();
      this.$message({
        message: "上传失败,服务异常",
        type: "error",
      });
    },
    changeMonthTwo(value) {
      this.$emit("loadingTrue", true); //向外传loading
      if (value) {
        const data = {}
        uploadList.forEach((item, index) => {
          data[`file_${index}`] = item.img.file
        })
        return post('cms/file', data)
        .then(res => {
          if (!Array.isArray(res) || res.length === 0) {
            throw new Error('图像上传失败')
          }

          const resObj = res.reduce((acc, item) => {
            acc[item.key] = item
            return acc
          }, {})

          uploadList.forEach((item, index) => {
            const remoteData = resObj[`file_${index}`]
            item.cb(remoteData)
          })
        })
        .catch(err => {
          uploadList.forEach(item => {
            item.cb(false)
          })
          let msg = '图像上传失败, 请重试'
          if (err.message) {
            // eslint-disable-next-line
            msg = err.message
          }
          console.error(err)
          this.$message.error(msg)
        })
      }
      this.$emit("loadingTrue", false); //向外传loading
    },
    beforeUpload(file) {
      var maxSize = 10 * 1024 * 1024
      // if (!/\.(xls|xlsx|xlsb|xlsm|xlst)$/.test(file.name)) {
      //   this.$message.error("上传文件只能是 excel 格式!");
      //   return false;
      // }
      if(file.size > maxSize) {
        this.$message.error("上传文件 ≥ 10Mb!");
        return false;
      }
      this.dloading = true;
      return true;
    },
  },
};
</script>

<style lang="scss" scoped>
.batch-upload-container::v-deep {
  .el-dialog {
    height: auto !important;
    .title-box {
      .title {
        font-size: 16px;
        display: flex;
        align-items: center;
        justify-content: space-between;
        padding-right: 30px;
        a {
          text-decoration: underline;
          color: var(--active-color);
          cursor: pointer;
          font-size: 12px;
        }
        .el-date-editor {
          width: 110px;
        }
      }
    }
  }
  .el-upload-dragger {
    background-color: var(--box-bg) !important;
    width: 160px;
    height: 160px;
    cursor: pointer;
    line-height: 1.3;
    font-size: 12px;
    position: relative;
    overflow: hidden;
    text-align: center;
    border-radius: 3px;
    border: 1px dashed #d9d9d9;
    display: flex;
    align-items: center;
    flex-direction: column;
    justify-content: center;
    -webkit-transition: all 0.1s;
    transition: all 0.1s;
    color: #666666;
    margin-right: 1em;
    margin-bottom: 1em;
  }
}
</style>