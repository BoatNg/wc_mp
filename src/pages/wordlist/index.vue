<template>
<div>
  <div class="list_wrap" v-if="word_info.length>0">
    <div v-for="(item,index) in word_info" :key="index" class="item_wrap" >
      <div class="word">{{item.value}}</div>
      <!-- 发音 -->
      <div class="pronounce_wrap">
        <div v-for="(pr, pr_ind) in item.pronounce" :key="pr_ind">{{pr.type}} {{pr.value}}</div>
      </div>
      <!-- 翻译 -->
      <div class="tl_wrap">
        <div v-for="(tl,tl_ind) in item.translation" :key="tl_ind">{{tl.value}}</div>
      </div>
    </div>
    <div v-if="!isMore" class="nomore">没有更多啦</div>
  </div>
  <div v-else class="nomore">您没有收藏单词哟</div>
</div>
  
</template>

<script>
import { formatTime } from "@/utils/index";
import card from "@/components/card";
import Flyio from "flyio/dist/npm/wx";
const flyio = new Flyio();
function isJson(str) {
  try {
    JSON.parse(str);
  } catch (e) {
    return false;
  }
  return true;
}

export default {
  components: {
    card
  },

  data() {
    return {
      page_info: {
        count: 0,
        limit: 8,
        offset: 0
      },
      word_info: []
    };
  },

  computed: {
    isMore() {
      let num = this.page_info.limit * this.page_info.offset;
      return this.page_info.count > num;
    }
  },
  onReachBottom() {
    if (this.isMore) {
      this.page_info.offset = +this.page_info.offset + 1;
      wx.showLoading({
        title: "获取中...",
        mask: true
      });
      this.getWordList(true);
    }
  },
  onShow() {
    let user_info = wx.getStorageSync("user_info");
    console.log('onShow ====')
    console.log(user_info)
    flyio.interceptors.request.use(async req => {
      req.headers["Authorization"] = `Bearer ${user_info.token}`;
      return req;
    });
    this.page_info.offset = 0
    this.getWordList();
  },
  methods: {
    handleScoll() {
      console.log("bottom");
    },
    getWordList(isScroll = false) {
      let limit = this.page_info.limit;
      let offset = this.page_info.offset;
      return flyio
        .get(`${HOST}/api/v1/word`, {
          limit,
          offset
        })
        .then(res => {
          console.log(res.data)
          let { data: result } = res;
          // 转JSON
          let word_info = result.data.word_info.map(item => {
            // cognate example_sentence pos pronounce synonyms translation word_group
            let keys = [
              "cognate",
              "example_sentence",
              "pos",
              "pronounce",
              "synonyms",
              "translation",
              "word_group"
            ];
            keys.forEach(key => {
              if (isJson(item[key])) {
                item[key] = JSON.parse(item[key]);
              } else {
                item[key] = [];
              }
            });
            return item;
          });
          this.page_info = result.data.page_info;
          if (isScroll) {
            this.word_info = this.word_info.concat(word_info);
          } else {
            this.word_info = word_info;
          }
          wx.hideLoading();
        });
    }
  },
  created() {}
};
</script>

<style>
.list_wrap {
  background: #fefefe;
  padding: 0 20px 50px;
}

.item_wrap {
  font-size: 14px;
  color: #3b3b3b;
  padding-bottom: 10px;
  padding-top: 15px;
  border-bottom: 1px solid #ddd;
}

.word {
  padding-bottom: 5px;
  font-size: 18px;
  font-weight: 500;
  letter-spacing: 1px;
}

.pronounce_wrap {
  padding-left: 10px;
  display: flex;
  font-size: 12px;
  padding-bottom: 5px;
}

.pronounce_wrap > div {
  margin-right: 10px;
  color: #93b5cf;
}

.tl_wrap {
  padding-left: 10px;
  color: #7b7b7b;
}

.list_content {
  height: 100vh;
}

.nomore {
  text-align: center;
  padding-top: 10px;
  font-size: 12px;
  color: #93b5cf;
}
</style>
