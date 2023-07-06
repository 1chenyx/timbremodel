<template>
  <div class="article">
    <div class="article-list">
      <div class="article-item"
        v-for="(item, index) in dataSource"
        v-bind:key="item.id" :style="{'background-image': `url('http://file.timbremodel.com/config/${item.gender}头像.jpg')`}" @click="jump(index)">
        <div class="article-list-item-extra-wrap">
          <div class="article-detail">
            <!-- <div class="info-row">
              <ul class="meta-list">
                <li class="item clickable">
                  <el-tag
                    effect="light"
                    v-if="item.article_type == 0">原</el-tag>
                  <el-tag type="success"
                    effect="light"
                    v-else-if="item.article_type == 1">转</el-tag>
                  <el-tag type="info"
                    effect="light"
                    v-else-if="item.article_type == 2">译</el-tag>
                </li>
                <li class="item username clickable"
                  v-if="item.user_info != undefined">
                  <router-link :to="{
                    path: `/user/${item.user_info.id}/article`
                  }"
                    target="_blank">{{ item.user_info.nickname }}</router-link>
                </li>
                <li class="item">{{ item.time_span }}</li>
                <li class="item"
                  v-for="(tag, index) in item.tags"
                  v-bind:key="index">
                  <router-link :to="{ path: '/tag/' + `${tag.id}` }"
                    class="tag"
                    target="_blank">{{ tag.tag_name }}</router-link>
                </li>
              </ul>
            </div> -->
            <div class="info-row model-row">
              <div class="model-type">
                <span>模型类型：</span> <span>{{ item.channelname }}</span>
              </div>
            </div>
            <div class="info-row title-row">
              <router-link target="_blank"
                class="article-detail-title"
                :to="{ name: 'p', params: { id: item.id } }">{{ item.title }}</router-link>
            </div>
            <div class="info-row audio-row">
              <!-- <div class="audio-type">
                <span>模型类型：</span> <span>{{ item.channelname }}</span>
              </div> -->
              <!-- <audio controls>
                <source :src="item.audio_path" type="audio/mpeg">
              </audio> -->
              <div class="audio-play-btn" title="点击播放/暂停效果音" :class="{'audio-pause-btn': isCurrentIndex === index && !isPause, 'audio-pause-animation': isPause, 'audio-play-animation': isCurrentIndex === index}" @click="playAudio(item, index)">
                <div class="animation-div"></div>
                <span class="audio-play-icon"></span>
              </div>
            </div>
            <!-- <span class="info-row article-detail-content article-detail-ellpisis3">{{ item.excerpt }}</span> -->
            <div class="info-row  info-row-bottom">
              <div class="article-tool">
                <ul class="article-about">
                  <li :ref="`jump_${index}`">
                    <router-link class="article-detail-title"
                      :to="{ name: 'p', params: { id: item.id } }"
                      target="_blank">
                      <l-icon name="like-fill"
                        v-if="item.is_liked == true"
                        color="var(--el-color-list) "></l-icon>
                      <l-icon name="like"
                        v-else
                        color="var(--el-color-list) "></l-icon>
                      <!-- <i class="el-icon-star-off"></i> -->
                      <span class="count">{{ item.likes_quantity }}</span>
                    </router-link>
                  </li>
                  <li>
                    <router-link class="article-detail-title"
                      target="_blank"
                      :to="{ path: '/p/' + item.id + '#comment-list' }">
                      <l-icon name="comment"
                        color="var(--el-color-list) "></l-icon>
                      <!-- <i class="el-icon-s-comment"></i> -->
                      <span class="count">{{ item.comment_quantity }}</span>
                    </router-link>
                  </li>
                  <li>
                    <router-link class="article-detail-title"
                      target="_blank"
                      :to="{ name: 'p', params: { id: item.id } }">
                      <l-icon name="eye"
                        color="var(--el-color-list) "></l-icon>
                      <!-- <i class="el-icon-view"></i> -->
                      <span class="count">{{ item.view_hits }}</span>
                    </router-link>
                  </li>
                </ul>
              </div>
            </div>
          </div>
          <router-link target="_blank"
            :to="{ path: '/p/' + item.id }"
            v-if="item.thumbnail_display != ''">
            <img class="
            article-thumb"
              :src="item.thumbnail_display" />
          </router-link>
        </div>
      </div>
    </div>
    <audio @ended="endedHandel" autoplay ref="audio_model" v-show="false">
      <source  type="audio/mpeg">
    </audio>
  </div>
</template>

<script>
export default {
  name: 'ArticleList',
  data() {
    return {
      isCurrentIndex: '',
      currentAudio_path: '',
      isPause: false      //暂停状态
    };
  },
  props: {
    dataSource: {
      type: Array,
      default: () => {
        return [];
      },
    },
  },
  mounted() {},
  methods: {
    toArticle(item) {
      this.$router.push({ name: 'p', params: { id: item.id } });
    },
    jump(index) {
      // console.log(this, this.$refs[`jump_${index}`], `jump_${index}`)
      // this.$refs[`jump_${index}`] && this.$refs[`jump_${index}`][0].click()
    },
    playAudio(item, index) {
      let audio_model = this.$refs.audio_model
      this.currentAudio_path = item.audio_path
      if(this.isCurrentIndex === index) {
        this.isPause = !this.isPause
        this.isPause ? audio_model.pause() : audio_model.play()
      }else {
        this.isPause = false
        audio_model.src = item.audio_path
        audio_model.play()
        this.isCurrentIndex = index
      }
    },
    // 播放结束
    endedHandel() {
      this.isPause = true
      this.currentAudio_path = ''
    }
  },
};
</script>

<style scoped lang="scss">
.article {
  flex: 1;
  // padding: 20px 5px;
  // background: rgba(255, 255, 255, 1);
  // box-shadow: 0px 2px 14px 0px rgba(243, 243, 243, 1);
  border-radius: 8px;
  .article-list {
    // cursor: pointer;
    display: grid;
    grid-column-gap: 10px;
    grid-row-gap: 10px;
    grid-template-columns: repeat(3, minmax(0, 1fr));

    .article-item {
      display: flex;
      flex-direction: row;
      justify-content: flex-start;
      border-bottom: 1px solid rgba(178, 186, 194, 0.15);
      padding: 1.5rem 2rem;
      background: rgba(255, 255, 255, 1);
      box-shadow: 0px 2px 14px 0px rgba(243, 243, 243, 1);
      box-sizing: border-box;
      width: 100%;
      height: 450px;
      border-radius: 5px;
      background-size: 100% 100%;
      position: relative;
      .article-list-item-extra-wrap {
        display: flex;
        align-items: center;
        width: 100%;
        .article-thumb {
          flex: 0 0 auto;
          margin-left: 2rem;
          background-color: #fff;
          cursor: pointer;
          width: 150px;
          height: 100px;
          border-radius: 8px;
          margin-right: 30px;
          object-fit: cover;
        }
        .article-detail {
          flex: 1 1 auto;
          flex-direction: column;
          justify-content: center;
          min-width: 0;
          .article-detail-title {
            cursor: pointer;
            height: 22px;
            font-size: 18px;
            font-weight: 600;
            color: var(--el-color-list);
            line-height: 22px;
          }
          .article-detail-title:hover {
            text-decoration: underline;
          }
          .article-detail-content {
            margin-bottom: 10px;
            font-size: 14px;
            font-weight: 400;
            color: rgba(140, 152, 174, 1);
            line-height: 22px;
          }
          .article-detail-ellpisis3 {
            display: -webkit-box;
            overflow: hidden;
            white-space: normal !important;
            text-overflow: ellipsis;
            word-wrap: break-word;
            -webkit-line-clamp: 3;
            -webkit-box-orient: vertical;
          }
        }
        .info-row {
          display: flex;
          flex-direction: row;
          justify-content: space-between;
          font-size: 12px;
          line-height: 17px;
          color: var(--el-color-list);
          .meta-list {
            display: flex;
            align-items: baseline;
            white-space: nowrap;
            .item .el-tag {
              border-radius: 4px;
              padding: 0 5px;
            }
            .item.clickable:hover {
              color: #007fff;
            }
            .item.username {
              display: flex;
              align-items: baseline;
            }
            .item:not(:last-child)::after {
              content: '·';
              color: rgb(178, 186, 194);
              margin: 0px 0.4em;
            }
            .item a.tag:not(:last-child):after {
              content: '/';
              margin: 0 0.2em;
              color: #b2bac2;
            }
            .item .tag:hover {
              color: #007fff;
            }
          }

          .article-tool {
            ul.article-about {
              display: inline-flex;
              white-space: nowrap;
              li {
                height: 1.8rem;
                font-size: 1.083rem;
                line-height: 1;
                white-space: nowrap;
                border-radius: 1px;
                cursor: pointer;
                margin-left: 10px;
                background: #686868;
                border-radius: 5px;
                opacity: 0.8;
                color: var(--el-color-list);
                a {
                  display: flex;
                  align-items: center;
                  padding: 0 0.8rem;
                  height: 100%;
                  color: inherit;
                  .count {
                    color: var(--el-color-list);
                    margin-left: 0.2em;
                  }
                }
              }
              li:hover {
                background-color: #f7f8fa;
              }
              .iconfont {
                margin-right: 0.2em;
              }
            }
          }
        }
        .title-row {
          white-space: nowrap;
          overflow: hidden;
          text-overflow: ellipsis;
          position: absolute;
          bottom: calc(1.8rem + 10px);
          left: 5px;
          .article-detail-title {
            font-size: 15px;
          }

        }
        .model-row {
          position: absolute;
          top: 5px;
          left: 5px;
          font-size: 15px;
        }
        .audio-row {
          position: absolute;
          bottom: calc(1.8rem + 32px);
          right: 5px;
          // width: 96%;
          // text-align: end;
          .audio-play-btn {
            width: 40px;
            height: 40px;
            border-radius: 50%;
            background: rgba(255,255,255);
            background-size: 100%;
            position: relative;
            cursor: pointer;
            .audio-play-icon {
              display: none;
              border-left: 16px solid #9c998c;
              border-top: 10px solid transparent;
              border-bottom: 10px solid transparent;
              position: absolute;
              top: 50%;
              left: calc(50% + 3px);
              line-height: 0;
              transform: translate(-50%, -50%);
            }
            .animation-div {
              width: 100%;
              height: 100%;
              position: absolute;
              top: 0;
              right: 0;
              background-image: url("../../../public/icons/player.jpg");
              background-size: 100%;
              border-radius: 50%;
            }
          }
          .audio-pause-btn {
            .audio-play-icon {
              display: none;
              border: unset !important;
              top: 50%;
              left: 50%;
              line-height: 0;
              transform: translate(-50%, -50%);
            }
            // .animation-div {
            //   animation-play-state: running;
            //   animation: rotational linear 4s infinite;
            // }
          }
          .audio-play-btn:hover {
            .audio-play-icon {
              display: inline-block;
            }
          }
          .audio-pause-btn:hover {
            .audio-play-icon::before {
              content: "";
              display: inline-block;
              width: 5px;
              height: 20px;
              background-color: transparent;
              border-left: 4px solid #9c998c;
              border-right: 4px solid #9c998c;
            }
          }
          .audio-play-animation {
            .animation-div { 
              animation-play-state: running;
              animation: rotational linear 4s infinite;
            }
          }
          .audio-pause-animation {
            .animation-div { 
              animation-play-state: paused;
            }
          }
        }
        .info-row-bottom {
          position: absolute;
          bottom: 0px;
          right: 5px;
        }
      }
    }
    .article-item:nth-child(3n) {
      margin-right: 0px;
    }
  }
}
@keyframes rotational
{
	0% {
    transform: rotate(0deg);
  }
  50% {
    transform: rotate(180deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
@media (max-width: 600px) {
  .article-thumb {
    display: none;
  }
}

@media screen and (max-width: 1200px) {
  .container .quantity-statistics .quantity-item {
    width: 32%;
    &:last-child {
      display: none;
    }
  }
  .container .information .personal {
    display: none;
  }
}

@media screen and (max-width: 1200px) {
  .container .lin-info .lin-info-left {
    width: 100%;
  }
}
</style>
