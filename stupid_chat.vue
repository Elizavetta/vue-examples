<template>
  <div class="chat-full-wrapper">
    <div class="chat-full-panel-top">
      <div class="chat-full-panel-top-right">
        <div class="chat-full-panel-top-right-input-wrapper">
          <input class="chat-full-panel-top-right-input" type="text" />
          <span class="chat-full-panel-top-right-input-suffix">
            <i class="chat-full-panel-top-right-input-icon">
              <svg
                viewBox="64 64 896 896"
                data-icon="search"
                width="1em"
                height="1em"
                fill="currentColor"
                aria-hidden="true"
                class
              >
                <path
                  d="M909.6 854.5L649.9 594.8C690.2 542.7 712 479 712 412c0-80.2-31.3-155.4-87.9-212.1-56.6-56.7-132-87.9-212.1-87.9s-155.5 31.3-212.1 87.9C143.2 256.5 112 331.8 112 412c0 80.1 31.3 155.5 87.9 212.1C256.5 680.8 331.8 712 412 712c67 0 130.6-21.8 182.7-62l259.7 259.6a8.2 8.2 0 0 0 11.6 0l43.6-43.5a8.2 8.2 0 0 0 0-11.6zM570.4 570.4C528 612.7 471.8 636 412 636s-116-23.3-158.4-65.6C211.3 528 188 471.8 188 412s23.3-116.1 65.6-158.4C296 211.3 352.2 188 412 188s116.1 23.2 158.4 65.6S636 352.2 636 412s-23.3 116.1-65.6 158.4z"
                ></path>
              </svg>
            </i>
          </span>
        </div>
      </div>
      <div class="chat-full-panel-top-left">
        <span v-if="!active_chat_user" class="chat-full-panel-top-left-name"
          >выберите контакт ...</span
        >
        <span v-if="active_chat_user" class="chat-full-panel-top-left-name">{{
          active_chat_user.name
        }}</span>
        <span
          v-if="active_chat_user"
          class="chat-full-contacts-item-online chat-full-panel-top-left-online"
          :class="{ active: active_chat_user.online }"
        ></span>
      </div>
    </div>
    <div class="chat-full-contacts">
      <!--
        <div class="chat-full-contacts-title">Контакты {{ myTeam.length }}</div>
      -->
      <div class="chat-full-contacts-list">
        <div
          class="chat-full-contacts-item"
          :class="{ active: active_chat_id === u.id }"
          v-for="u in myteamLastMsgSort"
          :key="u.id"
          @click="selectContact(u)"
        >
          <div
            class="chat-full-contacts-item-avatar"
            :style="{
              'background-image': 'url(' + serverUrl + u.avatar + ')'
            }"
          ></div>
          <div class="chat-full-contacts-item-name">
            <span class="chat-full-contacts-item-name-txt">{{ u.name }}</span>
            <span
              class="chat-full-contacts-item-online"
              :class="{ active: u.online }"
            ></span>
            <div class="chat-full-contacts-item-name-last">
              <span v-if="newMessagesInChat(u.id)"
                >({{ newMessagesInChat(u.id) }})</span
              >
              {{ lastShortMsg(u.id) }}
            </div>
          </div>
        </div>
      </div>
    </div>

    <div class="chat-full-panel">
      <div class="chat-full-panel-messages" v-if="active_chat_user">
        <div
          class="chat-full-panel-messages-old"
          @click="loadHistory()"
          v-if="!chat_history_loaded[active_chat_id]"
        >
          Загрузить историю
        </div>
        <div
          class="chat-full-panel-messages-line"
          :class="{ mine: msg.from === userData.id }"
          v-for="msg in chat_messages[active_chat_user.id]"
          :key="msg.created + ''"
        >
          <div
            class="chat-full-panel-messages-item-avatar"
            :class="{ mine: msg.from === userData.id }"
            :style="{
              'background-image':
                'url(' +
                serverUrl +
                (msg.from === userData.id
                  ? userData.avatar
                  : active_chat_user.avatar) +
                ')'
            }"
          ></div>
          <div
            class="chat-full-panel-messages-item"
            :class="{ mine: msg.from === userData.id }"
          >
            <span class="chat-full-panel-messages-item-txt">{{
              msg.message
            }}</span>
            <span class="chat-full-panel-messages-item-time">{{
              formatDate(msg.created)
            }}</span>
          </div>
        </div>
      </div>

      <div class="chat-full-panel-input" v-if="active_chat_user && !swapped">
        <div class="chat-full-panel-input-wrapper">
          <textarea
            class="chat-full-panel-input-txt"
            type="text"
            v-model="new_msg"
            v-on:keyup.enter="submitNew()"
          ></textarea>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Vue from "vue";
import { sendMessage } from "../../../channel.js";
import { serverUrl } from "../../../utils/helper";
export default {
  name: "ChatFull",
  data() {
    return {
      myTeam: this.$store.state.myTeam,
      active_chat_user: null,
      active_chat_id: 0,
      swapped: false,
      new_msg: "",
      chat_messages: this.$store.state.chat_messages,
      serverUrl: serverUrl,
      chat_history_loaded: {},
      default_history_length: 60
    };
  },
  created() {
    let that = this;
    const is_all_calcs = this.$store.getters.is_all_calcs;

    if (that.$route.params.chatId && that.myTeam.length) {
      let user = that.myTeam.filter(el => {
        return el.id === parseInt(that.$route.params.chatId);
      })[0];
      if (user) {
        that.selectContact(user);
        setTimeout(function() {
          that.$forceUpdate();
        }, 1500);
      }
    }

    if (
      is_all_calcs === 15 ||
      is_all_calcs === 12 ||
      is_all_calcs === 28 ||
      is_all_calcs === 26
    ) {
      this.$store.dispatch("GET_TEAM_ALL");
    } else {
      this.$store.dispatch("GET_TEAM");
    }

    this.$store.dispatch("GET_CHAT_HISTORY");

    this.$store.watch(
      function(state) {
        return state.myTeam;
      },
      function(val) {
        that.myTeam = that.$store.state.myTeam;
        if (
          that.$route.params.chatId &&
          !that.active_chat_id &&
          that.myTeam.length
        ) {
          let user = that.myTeam.filter(el => {
            return el.id === parseInt(that.$route.params.chatId);
          })[0];
          if (user) that.selectContact(user);
        }
      },
      {
        deep: true
      }
    );
    this.$store.watch(
      function(state) {
        return state.chat_messages;
      },
      function() {
        that.chat_messages = that.$store.state.chat_messages;
      },
      {
        deep: true
      }
    );

    this.$store.subscribe((mutation, state) => {
      if (mutation.type === "ADD_NEW_CHAT_MSG") {
        that.$forceUpdate();
        this.$store.commit("SET_READ_CHAT", this.active_chat_id);
      }
    });
  },
  mounted() {
    this.$nextTick(function() {});
    let scroll_container = document.querySelector(".chat-full-panel-messages");
    if (scroll_container) {
      scroll_container.scrollTop = scroll_container.scrollHeight;
    }
  },
  watch: {
    myteam: {
      handler: function(val) {
        //console.log("chat team upd");
      }
    },
    chat_messages: {
      handler: function(val) {
        //console.log("chat msg upd");
      },
      deep: true
    }
  },
  computed: {
    userData() {
      return this.$store.getters.user;
    },
    myTeamOnlineSort() {
      return this.myTeam.sort(function(a, b) {
        if (a.online && !b.online) return -1;
        else if (!a.online && b.online) return 1;
        else return 0;
      });
    },
    myteamLastMsgSort() {
      let that = this;
      return this.myTeam.sort(function(a, b) {
        if (
          that.chat_messages[a.id] &&
          that.chat_messages[a.id].length &&
          that.chat_messages[b.id] &&
          that.chat_messages[b.id].length
        ) {
          if (
            that.chat_messages[a.id][that.chat_messages[a.id].length - 1]
              .created >
            that.chat_messages[b.id][that.chat_messages[b.id].length - 1]
              .created
          )
            return -1;
          else return 1;
        } else if (
          that.chat_messages[a.id] &&
          that.chat_messages[a.id].length &&
          (!that.chat_messages[b.id] || !that.chat_messages[b.id].length)
        ) {
          return -1;
        } else if (
          that.chat_messages[b.id] &&
          that.chat_messages[b.id].length &&
          (!that.chat_messages[a.id] || !that.chat_messages[a.id].length)
        ) {
          return 1;
        } else if (a.online && !b.online) return -1;
        else if (!a.online && b.online) return 1;
        else return 0;
      });
    },
    newMessagesCount() {
      return function() {
        let count = 0;
        for (let k in this.chat_messages) {
          if (this.chat_messages[k] && this.chat_messages[k].length) {
            count += this.chat_messages[k].filter(el => {
              return el.is_new;
            }).length;
          }
        }
        return count;
      };
    },

    newMessagesInChat() {
      return function(chat_id) {
        if (!this.chat_messages[chat_id] || !this.chat_messages[chat_id].length)
          return 0;
        return this.chat_messages[chat_id].filter(el => {
          return el.is_new;
        }).length;
      };
    },

    lastShortMsg() {
      return function(chat_id) {
        if (!this.chat_messages[chat_id] || !this.chat_messages[chat_id].length)
          return "";
        return this.chat_messages[chat_id][
          this.chat_messages[chat_id].length - 1
        ].message;
      };
    },

    formatDate() {
      return function(date) {
        let d = new Date(date);
        let d_now = new Date();
        let res = "";
        let day =
          d.getDate().toString().length === 1
            ? "0" + d.getDate().toString()
            : d.getDate().toString();
        let month =
          d.getMonth().toString().length === 1
            ? "0" + d.getMonth().toString()
            : d.getMonth().toString();
        let hour =
          d.getHours().toString().length === 1
            ? "0" + d.getHours().toString()
            : d.getHours().toString();
        let min =
          d.getMinutes().toString().length === 1
            ? "0" + d.getMinutes().toString()
            : d.getMinutes().toString();
        if (d.getYear() !== d_now.getYear()) res += d.toLocaleDateString();
        else if (
          d.getDate() !== d_now.getDate() ||
          d.getMonth() !== d_now.getMonth()
        ) {
          res += day + "." + month;
        }
        res += " " + hour + ":" + min;
        return res;
      };
    }
  },
  methods: {
    selectContact: function(user) {
      console.log("select contact");
      let that = this;
      this.active_chat_user = user;
      this.active_chat_id = user.id;
      if (this.newMessagesInChat(this.active_chat_id) !== 0)
        this.$store.commit("SET_READ_CHAT", this.active_chat_id);

      this.$forceUpdate();
      setTimeout(function() {
        that.$forceUpdate();
      }, 50);
      setTimeout(function() {
        let scroll_container = document.querySelector(
          ".chat-full-panel-messages"
        );
        scroll_container.scrollTop = scroll_container.scrollHeight;
      }, 50);
    },
    submitNew() {
      if (!this.new_msg || !this.new_msg.trim()) return;
      let new_msg = {
        type: "chat_msg",
        from: this.$store.state.user.id,
        to: this.active_chat_user.id,
        message: this.new_msg,
        created: new Date()
      };
      sendMessage(new_msg);
      this.new_msg = "";
      this.$store.commit("ADD_NEW_CHAT_MSG", new_msg);
    },

    loadHistory() {
      this.$store.dispatch("GET_CHAT_HISTORY_DIALOG", {
        chat_id: this.active_chat_user.id,
        period: this.default_history_length
      });
      Vue.set(this.chat_history_loaded, this.active_chat_user.id, true);
    }
  }
};
</script>

<style lang="scss">
@import "./chat_full.scss";
</style>
