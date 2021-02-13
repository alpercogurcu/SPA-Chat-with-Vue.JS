<template>
  <div id="App">
    <input type="checkbox" id="mobilescreen" class="hiddencb" />
    <p v-show="isLoading" class="loadscreen">Yükleniyor</p>
    <div class="grid-container" id="gridcontainer">
      <div class="contenthead" id="contenthead">
        <div class="search">
          <i class="fas fa-search"></i>
          <input type="text" name="ara" id="ara" placeholder="Ara" />
        </div>
      </div>
      <div class="messageheader">
        <div class="backpress">
          <label for="mobilescreen">
            <i class="fas fa-arrow-circle-left fa-2x"></i
          ></label>
        </div>
        <div class="messageheaderUserInfo">
        
          <div class="profilephoto">
            <img :src="this.activeprofile.image" />
          </div>
          <div class="username">{{ this.activeprofile.alias }}</div>
          <div class="lastseen">{{ this.activeprofile.lastseen }}</div>
        </div>
        <div class="actions">
          <!---  <a href='#' class="screendesktop" id="screendesktop"><i class="fas fa-desktop fa-2x"></i></a>-->
          <a-icon type="sync" spin />
          
     
          <a href="#"> <i class="fas fa-info-circle fa-2x"></i></a>
          <a href="#"> <i class="fas fa-ellipsis-v fa-2x"></i> </a>
        </div>
      </div>
      <div class="contentlist" id="contentlist">
        <label for="mobilescreen">
          <!-- eslint-disable -->

          <div
            v-if="whoisyou != item.username"
            class="blockdiv"
            v-for="item in user"
            :key="item.id"
          >
            <!-- eslint-enable -->

            <div
              class="contentUserInfo"
              v-on:click="contentuserclick(item.username)"
            >
              <div class="profilephoto"><img v-bind:src="item.image" /></div>
              <div class="username">{{ item.alias }}</div>
              <div class="lastmessage">{{ item.lastseen }}</div>
             <div class="notification" v-if="item.unseen > 0">
                {{ item.unseen }}
              </div> 
              
            </div>
          </div></label
        >
      </div>

      <div class="messagearea" id="messagearea">
        <div
          v-for="item in activemessage"
          :key="item.id"
          :class="{
            message: item.owner != whoisyou,
            yourmessage: item.owner == whoisyou,
          }"
        >
          <div class="text">{{ item.message }}</div>
          <div class="time">{{ item.time }}</div>
          <div class="bubble"></div>
          <div v-if="item.isSeen && item.owner == whoisyou" class="seen">
            &#x2713;&#x2713;
          </div>
        </div>
      </div>

      <div class="messagefooter" id="messagefooter">
        <div class="writemessage">
          <input
            v-model="sentmessagedata.message"
            v-on:keyup.enter="sentmessage()"
            type="text"
            name="inputmessage"
            id="inputmessage"
            placeholder="Mesaj Yaz"
          />
        </div>
        <i class="fas fa-chevron-right fa-2x"></i>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
       value: 2,
      isLoading: true,
      lastid: 0,
      user: [],
      message: [],
      activemessage: [],
      activeprofile: [],
      whoisyou: "",
      sentmessagedata: {
        owner: "",
        sentto: "",
        id: "",
        message: "",
        time: "",
        isSeen: "",
      },
    };
  },
  methods: {
    changeunseenmessage() {
      const item2 = this.activemessage.filter(
        (am) => am.owner != this.whoisyou && am.isSeen == false
      );
      for(const i in item2){
        item2[i].isSeen = true;
      }
      //console.log(item2)
      // item2.forEach((element) => {
      //   console.log("Görüldü olmayan mesaj: " + element.message);
      //   this.activemessage[item2].isSeen = true;
      //   this.message.find((m) => m.id == element.id).isSeen = true;
      // });
    },

    countunseenmessage() {
      for (let i = 0; i < this.user.length; i++) {
        var currentDateWithFormat = new Date();
        console.log(currentDateWithFormat);
        this.user[i].unseen = 0;
        for (let j = 0; j < this.message.length; j++) {
          if (
            this.message[j].isSeen == false &&
            this.message[j].sentto == this.whoisyou &&
            this.message[j].owner == this.user[i].username
          ) {
            this.user[i].unseen += 1;
          }
        }
      }
    },

    sentmessage() {
      if (
        this.sentmessagedata.message != "" ||
        this.sentmessagedata.message != null
      ) {
        this.sentmessagedata.owner = this.whoisyou;
        this.sentmessagedata.sentto = this.activeprofile.username;
        this.sentmessagedata.id = this.lastid + 1;
        this.lastid++;
        var date = new Date();
        this.sentmessagedata.time = date;
        this.sentmessagedata.isSeen = false;
        this.message.push(JSON.parse(JSON.stringify(this.sentmessagedata)));

        this.activemessage.push(
          JSON.parse(JSON.stringify(this.sentmessagedata))
        );

        this.sentmessagedata.message = "";
      }
    },
    contentuserclick(username) {
      const item = this.message.filter(
        (m) =>
          (m.sentto == username && m.owner == this.whoisyou) ||
          (m.sentto == this.whoisyou && m.owner == username)
      );
      this.activemessage = item;

      const item2 = this.user.find((u) => u.username == username);
      this.activeprofile = item2;

      this.changeunseenmessage();
      this.countunseenmessage();
    },

    messageScrollToEnd() {
      var messagearea = document.querySelector("#messagearea");
      var scrollHeight = messagearea.scrollHeight;
      messagearea.scrollTop = scrollHeight;
    },
  },

  created() {
    console.log("oluştu created");
    // setTimeout(() => {
    //fetch("https://api.jsonbin.io/b/5fcce32c65c249127ba3e060")
    fetch("data.json")
      .then((res) => {
        return res.json();
      })
      .then((res) => {
        this.isLoading = false;
        this.user = res.user;
        this.message = res.message;
        this.whoisyou = res.whoisyou;
        this.activeprofile =
          res.user[0].username == res.whoisyou ? res.user[1] : res.user[0];
        this.contentuserclick(
          res.user[0].username == res.whoisyou
            ? res.user[1].username
            : res.user[0].username
        );
        this.lastid = res.lastid;
        this.countunseenmessage();
      });

    //contentuserclick(res.user[0].username);
    //}, 1000)
  },

  updated() {
    if (this.activeprofile.username == this.whoisyou) {
      const activeuser = this.user.find(
        (u) =>
          u.username ==
          (this.whoisyou == this.activemessage[0].owner
            ? this.activemessage[0].sentto
            : this.activemessage[0].owner)
      );
      this.activeprofile = activeuser;
    }

    this.messageScrollToEnd();
  },
};
</script>

<style src="@/assets/style.css">
</style>



