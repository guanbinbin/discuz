<template>
<view>
<!--pages/activity_detail/activity_detail.wxml-->
<!--<import src="../common_temp/audio_player.wxml"></import>-->
<view class="contain" v-if="datalist.length > 0">
  <image class="banner" mode="aspectFill" :src="special_image?special_image:'https://mc.wpnicer.com/source/plugin/mobile/template/image/xiaochengxu/minimg/couse_bg.png'" v-if="special_image"></image>
  <view class="detail" :style="special_image?'margin-top:375rpx':'margin-top:0rpx'">
    <view class="c-nav">
      <view class="courses-name">{{thread.subject}}<text v-if="special_activity.class && special_activity.class != 'undefined'">{{special_activity.class}}</text></view>
    </view>


    <view class="c-header">
      <view class="info">
        <view class="time_content">
          <image class="small_icon" src="https://mc.wpnicer.com/source/plugin/mobile/template/image/xiaochengxu/minimg/join_time.png"></image>
          <view>报名截止：</view>
          <view class="time">{{special_activity.expiration!=0?special_activity.expiration:'不限'}}</view>
        </view>
        <view class="count_content">
          <image class="small_icon" src="https://mc.wpnicer.com/source/plugin/mobile/template/image/xiaochengxu/minimg/join_count.png"></image>
          <view>已报：</view>
          <view class="count">{{special_activity.applynumber}}/{{special_activity.number}}人</view>
        </view>
      </view>
    </view>

    <view class="teacher-list">
      <view class="mi_top">
        <view class="fl mi_head">
          <image :src="userAvatar + '' + datalist[0].authorid"></image>
        </view>
        <view class="fl mi_msg">
          <view class="mi_nick">{{datalist[0].nickname?datalist[0].nickname:datalist[0].author}}</view>
          <view class="mi_label">
            <text class="mi_ca">2小时前</text>
          </view>
        </view>
      </view>
    </view>

    <view class="d_content">
      <view class="detail_cell">
        <view class="l_title">活动时间</view>
        <rich-text class="l_message" :nodes="special_activity.starttimefrom + ' 至 ' + special_activity.starttimeto"></rich-text>
      </view>
      <view class="detail_cell">
        <view class="l_title">活动地点</view>
        <view class="l_message">{{special_activity.place}}</view>
      </view>
      <view class="detail_cell">
        <view class="l_title">活动介绍</view>
        <rich-text class="l_message" :nodes="datalist[0].message"></rich-text>
      </view>
      <view class="detail_cell">
        <view class="l_title">性别</view>
        <view class="l_message">{{special_activity.gender==1?'男':(special_activity.gender==2?'女':'不限')}}</view>
      </view>
      <view class="detail_cell last-cell">
        <view class="ac-join-c">
          <view class="l_title">已报名（{{special_activity.applynumber}}/{{special_activity.number}}）</view>
          <view class="j-m-c" v-if="member_uid == special_activity.uid || member_uid == 1">
          <view class="join-manage" @tap="activityManage">报名管理</view>
          </view>
          
        </view>
        
        <view class="l_student">
          <block v-for="(item, index) in applylist" :key="index">
            <view class="mi_head head-top">
              <image :src="item.avatar"></image>
            </view>
          </block>
        </view>
      </view>
    </view>
    <view :class="'join-btn ' + (isActivity?'':'join-gray')" @tap="joinActivity">
    <block v-if="special_activity.closed==1">
    已截止
    </block>
    <block v-else>
    {{isActivity?'报名参加':'取消报名'}}
    </block>
    </view>
  </view>

  <view class="grayRow replyInfo">
    <view>
    </view>
    <view class="sortTip" @tap="showSort">{{listSortType}}</view>

    <view class="shortListContain" v-if="sortListShow">
      <image src="https://mc.wpnicer.com/source/plugin/mobile/template/image/xiaochengxu/minimg/sortList.png" mode="top" class="shortList">
      </image>
      <view class="sortType sortTypeF" @tap.stop="sortClick" id="2">
        <view>默认顺序</view>
        <image class="sortCheck" src="https://mc.wpnicer.com/source/plugin/mobile/template/image/xiaochengxu/minimg/check.png" :hidden="!sortdefault"></image>
      </view>
      <view class="sortType" id="1" @tap.stop="sortClick">
        <view>按时间</view>
        <image class="sortCheck" src="https://mc.wpnicer.com/source/plugin/mobile/template/image/xiaochengxu/minimg/check.png" :hidden="sortdefault"></image>
      </view>
    </view>

  </view>

  <view class="reply">
    <block v-for="(item, index) in datalist" :key="index">

      <view class="replyCell" :id="item.pid" v-if="index != 0" @tap="cellClick">
        <view class="replyinfo-c">
          <view class="replyerInfo">
            <image class="avatar" :src="userAvatar + '' + item.authorid"></image>
            <view class="auther_info">
              <view class="username fontWeight">
                {{item.nickname?item.nickname:item.username}}
              </view>
              <rich-text class="retime" :nodes="item.dateline"></rich-text>
            </view>
          </view>

        </view>

        <view class="messageView">
          <rich-text class="message" :nodes="item.message"></rich-text>
          <view class="attachlist" v-if="item.attachments">

            <view :class="'attachementView ' + (item.imageA.length ==0 && item.videoA.length == 1?'singleVideo':'')" v-if="item.attachments">
              <block v-for="(attItem, imgIndex) in item.imageA" :key="imgIndex">
                <image :class="'attachement ' + (item.imageA.length ==1&& item.videoA.length == 0?'signle-image':'' )" :mode="item.imageA.length + item.videoA.length == 1?'widthFix':'aspectFill'" :src="attItem.newUrl" :id="imgIndex" :data-cellindex="index" @tap.stop="lookImage"></image>
              </block>

              <block v-for="(video, aidx) in item.videoA" :key="aidx">
                <image @tap.stop="clickVideo" :id="video.newUrl" class="attachement" src="https://mc.wpnicer.com/source/plugin/mobile/template/image/xiaochengxu/minimg/videoPlace.png"></image>
              </block>
            </view>
            <block v-for="(audio, index2) in item.audioA" :key="index2">
              <block data-type="template" data-is="audio_player" data-attr="...audio.toolUse">
  <view class="audio-playView">
    <image :src="is_play && currentAudio == attachment? 'https://mc.wpnicer.com/source/plugin/mobile/template/image/xiaochengxu/minimg/audio_play.png': 'https://mc.wpnicer.com/source/plugin/mobile/template/image/xiaochengxu/minimg/audio_paused.png'" class="audio-icon audio-play" :data-listindex="listIndex" @tap.stop="audio_play" :id="attachment"></image>
    <view class="control-process">
      <text class="current-process">
        <block v-if="current_process.length > 0 && currentAudio == attachment">
        {{current_process}}
        </block>
        <block v-else>
        00:00
        </block>
      </text>
      
      <text class="total-process">
        <block>
        {{total_process}}
        </block>
      </text>
      <slider class="slider" @change="hanle_slider_change" @touchstart="handle_slider_move_start" @touchend="handle_slider_move_end" min="0" block-size="12" block-color="#6199fd" :max="slider_max" activeColor="#FFD100" backgroundColor="rgb(153,153,153,0.4)" :value="(currentAudio == attachment)?slider_value:0"></slider>

    </view>
  </view>
</block>
            </block>
          </view>
        </view>

        <view class="flowerAndDiscuss">
           <view class="support" @tap.stop="sendFlower" :id="index">
            <image src="/static/image/support.png"></image>
            <view class="support-count">{{item.postreview.support?item.postreview.support:0}}</view>
          </view>
          <image src="https://mc.wpnicer.com/source/plugin/mobile/template/image/xiaochengxu/minimg/work_discuss.png" class="workButton" @tap.stop="replyWork" :id="item.pid"></image>
        </view>

        <view class="workReply" v-if="item.comments">
          <block v-for="(wreply, index2) in item.comments" :key="index2">
            <view class="workReplyText">
              <text class="workReplyAuthor">{{wreply.nickname?wreply.nickname:wreply.author}}：</text>{{wreply.comment}}</view>
          </block>
          <view @tap="commentMore" class="commentMore" :data-index="index" :data-pid="item.pid" v-if="commentMoreLock[item.pid] !== false && datalist[index].comments.length >= 5">查看更多></view>
        </view>

      </view>

    </block>
  </view>

  <view class="noMoreView">
    没有更多了~
  </view>

</view>

<!-- 底部栏 -->
<view class="toolbar">
  <view class="input-area" @tap.stop="replyText"> 请输入内容</view>
  <button open-type="share" plain="true" @tap="share">
    <image class="shareIcon" src="https://mc.wpnicer.com/source/plugin/mobile/template/image/xiaochengxu/minimg/course_share.png"></image>
    <text class="shareText">分享</text>
  </button>
</view>

<activity_joinMask @joinSucceed="joinSucceed" :acPostData="acPostData" :joinShow="joinShow" :specialActivity="special_activity"></activity_joinMask>
</view>
</template>

<script>
// pages/activity_detail/activity_detail.js
const detailUrl = require("../../config").detailUrl;
const baseUrl = require("../../config").baseUrl;
const pollvoteUrl = require("../../config").pollvoteUrl;
const collectUrl = require("../../config").collectUrl;
const sendFlowerUrl = require("../../config").sendFlowerUrl;
const joinClassUrl = require("../../config").joinClassUrl;
const bestanswerUrl = require("../../config").bestanswerUrl;
const commentMoreUrl = require("../../config").commentMoreUrl;
const activityAppliesUrl = require("../../config").activityAppliesUrl;
const userAvatar = require("../../config").userAvatar;
const loginmanager = require("../../utils/loginManager");
const util = require("../../utils/util.js");
const app = getApp();
var self;
import activity_joinMask from "../../component/activity_joinMask/activity_joinMask";

export default {
  data() {
    return {
      userAvatar: userAvatar,
      baseUrl: baseUrl,
      nickname: '',
      datalist: [],
      thread: {},
      tid: "",
      fid: "",
      loading: false,
      pagenum: 1,
      ppp: 10,
      favoriteinfo: '',
      collecttext: "收藏",
      favid: 0,
      sortdefault: false,
      listSortType: '按时间',
      sortListShow: false,
      workinfo: null,
      isShare: false,
      isJoin: false,
      isExpirations: false,
      commentMoreLock: {},
      special_activity: {},
      hasMore: false,
      special_image: "",
      applylist: "",
      isActivity: "",
      pid: "",
      member_uid: "",
      currentVideo: "",
      is_moving_slider: false,
      currentAudio: "",
      shareHidden: false,
      joinShow: false,
      acPostData: ""
    };
  },

  components: {
    activity_joinMask
  },
  props: {},
  onShow: function () {
    this.setData({
      commentMoreLock: {}
    });
  },

  onReady() {
    this.videoContext = wx.createVideoContext('detailVideo');
  },

  /**
   * 生命周期函数--监听页面加载
   */
  onLoad: function (options) {
    self = this;
    var tid = options.tid;
    self.setData({
      tid: tid,
      pagenum: 1
    });

    if (options.type == 'share') {
      self.isShare = true;
    }

    wx.showLoading();
    self.refreshRequest();
    self.setupAudioPlayer();
  },

  /**
   * 分享
   */
  onShareAppMessage: function (res) {
    self.setData({
      shareHidden: true
    });
    var title = self.thread.subject ? self.thread.subject : self.nickname + "邀请你参与活动，快快加入吧";
    var path = "/pages/discovery/discovery?shareid=" + self.tid + '&special=' + self.thread.special;
    var imagePath = "https://mc.wpnicer.com/source/plugin/mobile/template/image/xiaochengxu/minimg/kehoushare.png";

    if (self.datalist[0].imageA.length > 0) {
      imagePath = self.datalist[0].imageA[0].newUrl;
    }

    return {
      title: title,
      path: path,
      imageUrl: imagePath
    };
  },

  /**
   * 生命周期函数--监听页面卸载
   */
  onUnload: function () {
    self.innerAudioContext.destroy();
  },
  onPullDownRefresh: function () {
    self.refreshRequest();
  },
  onReachBottom: function () {
    if (self.hasMore) {
      self.requestMore(true);
    }
  },
  methods: {
    commentMore(e) {
      var pid = e.currentTarget.dataset.pid;
      var page = this.commentMoreLock[pid];
      var index = e.currentTarget.dataset.index;

      if (typeof page == "undefined") {
        page = 2;
      }

      var data = {
        pid: pid,
        tid: this.tid,
        page: page
      };
      app.globalData.apimanager.getRequest(commentMoreUrl, data).then(res => {
        var comments = res.Variables.comments[pid];
        var usernicknames = res.Variables.usernicknames;

        if (comments.length > 0) {
          page++;
          self.commentMoreLock[pid] = page;

          for (let key in comments) {
            let item = comments[key];
            item.nickname = usernicknames[item.authorid];
          }

          self.datalist[index].comments = self.datalist[index].comments.concat(comments);
          self.setData({
            commentMoreLock: self.commentMoreLock,
            datalist: self.datalist
          });
        } else {
          self.commentMoreLock[pid] = false;
          self.setData({
            commentMoreLock: self.commentMoreLock
          });
        }
      }).catch(res => {
        wx.showToast({
          title: '出错了！',
          icon: 'none'
        });
      });
    },

    makeRequest() {
      self.setData({
        loading: true
      });
      let ordertype = 2;

      if (self.sortdefault) {
        self.setData({
          listSortType: '默认顺序'
        });
        ordertype = 1;
      }

      let getDic = {
        tid: self.tid,
        page: self.pagenum,
        ppp: self.ppp,
        ordertype: ordertype
      };
      app.globalData.apimanager.getRequest(detailUrl, getDic).then(res => {
        wx.hideLoading();
        wx.stopPullDownRefresh();
        let threads = res.Variables.thread;
        self.fid = threads.fid;
        let nickname = res.Variables.member_nickname ? res.Variables.member_nickname : res.Variables.member_username;
        self.setData({
          nickname: nickname
        });

        if (res.Message) {
          let messageval = res.Message.messageval;

          if (messageval.indexOf('nonexistence') != -1 || messageval.indexOf('nopermission') != -1 || messageval.indexOf('beoverdue') != -1 || messageval.indexOf('nomedal') != -1 || messageval == 'forum_group_noallowed') {
            wx.showModal({
              title: "提示",
              content: res.Message.messagestr,
              showCancel: false,
              confirmText: "知道了",
              success: function (res) {
                if (res.confirm) {
                  wx.navigateBack();
                }
              }
            });
            return;
          }
        }

        if (threads.favoriteinfo && threads.favoriteinfo != '') {
          console.log(threads.favoriteinfo);
          self.setData({
            favoriteinfo: threads.favoriteinfo,
            favid: threads.favoriteinfo.favid
          });
          self.resetCollectState();
        }

        var usernicknames = res.Variables.usernicknames;
        let arr1 = self.datalist;
        let listArray = res.Variables.postlist;

        for (let i = 0; i < listArray.length; i++) {
          let postItem = listArray[i];

          if (usernicknames) {
            if (usernicknames[postItem.authorid]) {
              postItem.nickname = usernicknames[postItem.authorid];
            }
          }

          postItem.message = postItem.message.replace(/\<img/gi, '<img class="rich-img"');
          postItem.message = postItem.message.replace(/<font (.*?)>/gi, "");
          postItem.message = postItem.message.replace(/<\/font>/gi, ""); // 作业评论处理

          let comments = res.Variables.comments;

          if (comments && comments.length != 0) {
            if (comments[postItem.pid]) {
              let comment = comments[postItem.pid];

              for (let key in comment) {
                let item = comment[key];

                if (usernicknames) {
                  item.nickname = usernicknames[item.authorid];
                }
              }

              postItem.comments = comment;
            }
          }

          var listindex = i + self.datalist.length; // 附件处理

          let attachments = postItem.attachments;
          let imageA = [];
          let audioA = [];
          let videoA = [];

          for (let aidKey in attachments) {
            let attItem = attachments[aidKey];
            let newUrl = baseUrl + '/' + attItem.url + attItem.attachment;
            attItem['newUrl'] = newUrl;

            if (attItem.isimage != 0) {
              imageA.push(attItem);
            } else if (attItem.ext == 'mp3') {
              let total_process = '00:00';

              if (attItem.description) {
                total_process = util.formatTime(parseInt(attItem.description));
              }

              attItem['toolUse'] = {
                attachment: newUrl,
                listIndex: listindex,
                total_process: total_process
              };
              audioA.push(attItem);
            } else if (attItem.ext == 'mp4') {
              videoA.push(attItem);
            }
          }

          postItem['imageA'] = imageA;
          postItem['audioA'] = audioA;
          postItem['videoA'] = videoA;
        }

        if (self.pagenum > 1) {
          arr1 = arr1.concat(listArray);
        } else {
          arr1 = listArray;
        }

        if (arr1.length - 1 >= threads.replies) {
          self.setData({
            hasMore: false
          });
        } else {
          self.setData({
            hasMore: true
          });
        }

        if (res.Variables.special_activity) {
          self.setData({
            special_activity: res.Variables.special_activity
          });

          if (self.special_activity.attachurl) {
            var special_image = baseUrl + '/' + self.special_activity.attachurl;
            self.setData({
              special_image: special_image
            });
          } else if (arr1[0].attachments) {
            var attachments = arr1[0].attachments;

            for (let aidKey in attachments) {
              let attItem = attachments[aidKey];

              if (!self.special_image) {
                self.setData({
                  special_image: baseUrl + '/' + attItem.url + attItem.attachment
                });
              }
            }
          }

          if (res.Variables.applylist) {
            self.setData({
              applylist: res.Variables.applylist
            });
          }

          var strbutton = self.special_activity.button;
          var strclosed = self.special_activity.closed;
          var isActivity = false;

          if (strbutton == 'join' && strclosed == 0) {
            isActivity = true;
          }

          self.setData({
            isActivity: isActivity
          });

          if (arr1[0].pid) {
            self.setData({
              pid: arr1[0].pid
            });
          }
        }

        self.setData({
          loading: false,
          datalist: arr1,
          thread: threads,
          member_uid: res.Variables.member_uid
        });
      }).catch(res => {
        wx.stopPullDownRefresh();
        wx.hideLoading();
        self.setData({
          loading: false
        });
        wx.showToast({
          title: '出错了！',
          icon: 'none'
        });
      });
    },

    showSort() {
      self.setData({
        sortListShow: !self.sortListShow
      });
    },

    sortClick(e) {
      let id = e.currentTarget.id;
      let sortdefault = true;
      let listSortType = '默认顺序';

      if (id == 1) {
        sortdefault = false;
        listSortType = '按时间';
      }

      self.pagenum = 1;
      self.setData({
        sortdefault: sortdefault,
        listSortType: listSortType,
        sortListShow: false
      });
      self.makeRequest();
    },

    resetCollectState() {
      let collecttext = self.collecttext;

      if (self.favid != 0) {
        collecttext = "已收藏";
      } else {
        collecttext = "收藏";
      }

      console.log(collecttext);
      self.setData({
        collecttext: collecttext
      });
    },

    collectThread() {
      if (self.favid != 0) {
        // 已收藏 取消收藏
        let url = collectUrl + "&op=delete&deletesubmit=yes";
        let formhash = app.globalData.formhash;
        let postData = {
          formhash: formhash,
          favid: self.favid
        };
        app.globalData.apimanager.postRequest(url, postData).then(res => {
          if (res.Message.messageval == "do_success") {
            self.setData({
              favoriteinfo: '',
              favid: 0
            });
            self.resetCollectState();
          } else {
            wx.showToast({
              title: res.Message.messagestr,
              icon: 'none'
            });
          }
        }).catch(res => {
          wx.showToast({
            title: '出错了！',
            icon: 'none'
          });
        });
      } else {
        // 未收藏 去收藏
        let formhash = app.globalData.formhash;
        let data = {
          formhash: formhash,
          id: self.tid
        };
        app.globalData.apimanager.getRequest(collectUrl, data).then(res => {
          if (res.Message.messageval == "favorite_do_success") {
            self.setData({
              favid: res.Variables.favid
            });
            self.resetCollectState();
          } else {
            wx.showToast({
              title: res.Message.messagestr,
              icon: 'none'
            });
          }
        }).catch(res => {
          wx.showToast({
            title: '出错了！',
            icon: 'none'
          });
        });
      }
    },

    postEnter() {
      if (!loginmanager.isLogin()) {
        return;
      }

      wx.navigateTo({
        url: '../post_thread/post_thread?isreply=true'
      });
    },

    replyWork(e) {
      if (!loginmanager.isLogin()) {
        return;
      }

      wx.navigateTo({
        url: '../post_thread/post_thread?isevaluate=true&pid=' + e.currentTarget.id
      });
    },

    sendFlower(e) {
      let index = parseInt(e.currentTarget.id);
      let replyItem = self.datalist[index];
      let formhash = app.globalData.formhash;
      let data = {
        tid: self.tid,
        pid: replyItem.pid,
        hash: formhash
      };
      app.globalData.apimanager.getRequest(sendFlowerUrl, data).then(res => {
        if (res.Message.messageval == "thread_poll_succeed") {
          if (replyItem.postreview && replyItem.postreview.support) {
            replyItem.postreview.support = parseInt(replyItem.postreview.support) + 1;
          } else {
            var postreview = {
              support: 1
            };
            replyItem['postreview'] = postreview;
          }

          let param = {};
          let str = 'datalist[' + index + ']';
          param[str] = replyItem;
          self.setData(param);
        } else {
          wx.showToast({
            title: res.Message.messagestr,
            icon: 'none'
          });
        }
      }).catch(res => {
        wx.showToast({
          title: '出错了',
          icon: 'none'
        });
      });
    },

    bestanswer(e) {
      var data = {
        tid: self.tid,
        pid: e.currentTarget.id,
        formhash: app.globalData.formhash
      };
      wx.showLoading({
        title: '操作中'
      });
      app.globalData.apimanager.postRequest(bestanswerUrl, data).then(res => {
        wx.hideLoading();

        if (res.Message.messageval == 'reward_completion') {
          self.refreshRequest();
        }

        wx.showToast({
          title: res.Message.messagestr,
          icon: 'none'
        });
      }).catch(res => {
        wx.hideLoading();
      });
    },

    // 播放视频 -------------------
    clickVideo(e) {
      self.setData({
        currentVideo: e.currentTarget.id
      });
      self.videoContext.requestFullScreen({
        direction: 0
      });
    },

    fullScreenChange(e) {
      if (!e.detail.fullScreen) {
        self.videoContext.stop();
      }
    },

    /* *********************** 语音相关start *********** */
    setupAudioPlayer() {
      this.innerAudioContext = wx.createInnerAudioContext();
      this.innerAudioContext.obeyMuteSwitch = false;
      this.innerAudioContext.onEnded(res => {
        self.currentAudio.toolUse.is_ended = true;
        self.stopVoice();
      });
      this.innerAudioContext.onTimeUpdate(() => {
        console.log("ontimeupdate");

        if (!self.is_moving_slider) {
          // 播放中
          self.currentAudio = self.datalist[self.currentAudio.toolUse.listIndex].audioA[0];
          let param = {};
          let toolUsestr = "datalist[" + self.currentAudio.toolUse.listIndex + "].audioA[0].toolUse";
          let toolUse = self.currentAudio.toolUse;
          toolUse['current_process'] = util.formatTime(this.innerAudioContext.currentTime);
          toolUse['slider_value'] = Math.floor(this.innerAudioContext.currentTime);
          toolUse['slider_max'] = Math.floor(this.innerAudioContext.duration);
          param[toolUsestr] = toolUse;
          self.setData(param);
        }
      });
    },

    // 拖动进度条，到指定位置
    hanle_slider_change(e) {
      const position = e.detail.value;
      this.seekCurrentAudio(position);
    },

    // 拖动进度条控件
    seekCurrentAudio(position) {
      this.innerAudioContext.seek(position);
    },

    // 进度条滑动
    handle_slider_move_start() {
      this.setData({
        is_moving_slider: true
      });
    },

    handle_slider_move_end() {
      this.setData({
        is_moving_slider: false
      });
    },

    // 点击播放暂停
    audio_play(e) {
      let listIndex = e.currentTarget.dataset.listindex;
      let postItem = self.datalist[listIndex];
      let currentAudio = postItem.audioA[0];
      var param = {};
      let audioset = "datalist[" + listIndex + "].audioA[0].toolUse.currentAudio";
      param[audioset] = currentAudio.newUrl;
      self.setData(param);
      self.setData({
        currentAudio: currentAudio
      });
      let isplay = self.currentAudio.toolUse.is_play;
      let playstr = "datalist[" + listIndex + "].audioA[0].toolUse.is_play";
      param[playstr] = !isplay;
      self.setData(param);

      if (isplay) {
        self.innerAudioContext.pause();
        isplay = false;
      } else if (!isplay && !self.currentAudio.toolUse.is_ended) {
        self.playVoice();
      } else {
        self.innerAudioContext.play();
      }
    },

    playVoice() {
      var src = self.currentAudio.newUrl;
      this.innerAudioContext.src = src;
      this.innerAudioContext.play();
    },

    stopVoice() {
      var param = {};
      let toolUsestr = "datalist[" + self.currentAudio.toolUse.listIndex + "].audioA[0].toolUse";
      let toolUse = self.currentAudio.toolUse;
      toolUse['is_play'] = false;
      toolUse['slider_value'] = 0;
      toolUse['current_process'] = util.formatTime(0);
      param[toolUsestr] = toolUse;
      self.setData(param);
      self.innerAudioContext.stop();
      self.currentAudio = self.datalist[self.currentAudio.toolUse.listIndex].audioA[0];
    },

    /* *********************** 语音end *********** */
    lookImage(e) {
      let cellItem = self.datalist[e.currentTarget.dataset.cellindex];
      let imageA = cellItem.imageA;
      var imageSrcArray = [];

      for (let i = 0; i < imageA.length; i++) {
        let item = imageA[i];
        imageSrcArray.push(item.newUrl);
      }

      console.log(imageSrcArray);
      wx.previewImage({
        current: imageSrcArray[e.currentTarget.id],
        urls: imageSrcArray
      });
    },

    requestMore(isMore) {
      let pagenum = self.pagenum;

      if (isMore) {
        pagenum += 1;
      } else {
        pagenum = 1;
      }

      self.setData({
        pagenum: pagenum // 更新当前页数

      });
      self.makeRequest(); // 重新调用请求获取下一页数据 或者刷新数据
    },

    refreshRequest() {
      self.requestMore(false);
    },

    replyText() {
      if (!loginmanager.isLogin()) {
        return;
      }

      wx.navigateTo({
        url: '../post_thread/post_thread?isreply=true&type=text'
      });
    },

    joinActivity() {
      if (self.special_activity.closed == 1) {
        return;
      }

      var acPostData = {
        fid: self.fid,
        tid: self.tid,
        pid: self.pid,
        formhash: app.globalData.formhash
      };

      if (self.isActivity) {
        self.setData({
          joinShow: true,
          acPostData: acPostData
        });
      } else {
        acPostData['activitycancel'] = true;
        app.globalData.apimanager.postRequest(activityAppliesUrl, acPostData).then(res => {
          if (res.Message.messageval.indexOf('_success') != -1) {
            self.refreshRequest();
          }

          wx.showModal({
            content: res.Message.messagestr,
            showCancel: false,
            confirmText: '知道了'
          });
        }).catch(res => {});
      }
    },

    joinSucceed() {
      self.refreshRequest();
    },

    activityManage() {
      wx.navigateTo({
        url: '../activity_signup/activity_signup?tid=' + self.tid + '&pid=' + self.pid
      });
    }

  }
};
</script>
<style>
@import "./activity_detail.css";
</style>