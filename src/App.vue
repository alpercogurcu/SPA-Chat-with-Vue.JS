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
        <div class="messageheader" >
            <div class="backpress"><label for="mobilescreen"> <i class="fas fa-arrow-circle-left fa-2x"></i></label>
                </div>
             <div class="messageheaderUserInfo">
                <div class="profilephoto"> <img src="https://upload.wikimedia.org/wikipedia/commons/4/49/Magnolia_wieseneri.jpg" /></div>
                <div class="username">{{activeprofile.alias}}</div>
                <div class="lastseen">{{activeprofile.lastseen}}</div>
            </div>
            <div class="actions">
              <!---  <a href='#' class="screendesktop" id="screendesktop"><i class="fas fa-desktop fa-2x"></i></a>-->
                <a href="#"> <i class="fas fa-info-circle fa-2x"></i></a>
                <a href="#"> <i class="fas fa-ellipsis-v fa-2x"></i> </a>
            </div>
        </div>
        <div class="contentlist" id ="contentlist">

           
            <label for="mobilescreen">
            <div class="blockdiv" v-for="item in user" :key="item.id">
                <div class="contentUserInfo"  v-on:click="contentuserclick(item.username)">
                    <div class="profilephoto"> <img
                           v-bind:src="item.image" /></div>
                    <div class="username">{{item.alias}}</div>
                    <div class="lastmessage">{{item.lastseen}}</div>
                    <div class="notification" v-if="item.unseen > 0">{{item.unseen}}</div>
                </div>
            </div></label>

         
        </div>

        <div class="messagearea" id="messagearea">


            <div v-for="item in activemessage" :key="item.id"  :class="{message: (item.owner != whoisyou), yourmessage: (item.owner == whoisyou)}">
                <div class="text">{{item.message}}</div>
                <div class="time">{{item.time}}</div>
                <div class="bubble"></div>
                <div v-if="item.isSeen" class="seen">&#x2713;&#x2713;</div>
              </div>
          
               </div>
         
          

        <div class="messagefooter" id ="messagefooter">messagefooter</div>
    </div>

  </div>
</template>

<script>


export default {
  name: 'App',
  data() { return{
     isLoading : true,
            user: [],
            message: [],
            activemessage:[],
            activeprofile: [],
            whoisyou: ""
  }
  },
        methods: {
            contentuserclick(username){
                const item = this.message.filter(m=> (m.sentto == username && m.owner == this.whoisyou ) || (m.sentto == this.whoisyou && m.owner == username ));
               this.activemessage = item;

               const item2 = this.user.find(u=> u.username == username);
              this.activeprofile = item2;
            },

            messageScrollToEnd(){
              var messagearea = document.querySelector('#messagearea');
              var scrollHeight = messagearea.scrollHeight;
              messagearea.scrollTop = scrollHeight;
            }
           
        },
        
        created(){
           // setTimeout(() => {
                //fetch("https://api.jsonbin.io/b/5fcce32c65c249127ba3e060")
                fetch("data.json")
                .then((res) => {return res.json() })
                .then((res) => {
                    this.isLoading = false;
                    this.user = res.user;
                    this.message = res.message;
                    this.whoisyou = res.whoisyou;
                    this.activeprofile = res.user[0];

                    for(let i=0; i<this.user.length; i++)
                    {
                      console.log(this.user[i].username);
                      for (let j = 0; j < this.message.length; j++) {
                        if(this.message[j].isSeen == false && this.message[j].sentto == this.whoisyou && this.message[j].owner == this.user[i].username)
                        {
                          console.log(this.message[j].message);
                          this.user[i].unseen += 1;
                      
                        }
                        
                      }
                     
                     
                    }
                }
                )

            
                
                
                 //contentuserclick(res.user[0].username);
            //}, 1000)
           
        },
        updated(){
          this.messageScrollToEnd();
        }

}


</script>

<style src="@/assets/style.css">

</style>>

