<script>
export default {
  created() {
    // 调用API从本地缓存中获取数据
  },
  onShow() {
    if (wx.getUpdateManager) {
      const updateManager = wx.getUpdateManager();

      updateManager.onCheckForUpdate(function(res) {
        // 请求完新版本信息的回调
        console.log(res.hasUpdate);
      });

      updateManager.onUpdateReady(function() {
        wx.showModal({
          title: "更新提示",
          content: "新版本已经准备好，是否重启应用？",
          success(res) {
            if (res.confirm) {
              // 新的版本已经下载好，调用 applyUpdate 应用新版本并重启
              updateManager.applyUpdate();
            }
          }
        });
      });
    }
  }
};
</script>

<style>
.container {
  height: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: space-between;
  padding: 200rpx 0;
  box-sizing: border-box;
}
/* this rule will be remove */
* {
  transition: width 2s;
  -moz-transition: width 2s;
  -webkit-transition: width 2s;
  -o-transition: width 2s;
}
</style>
