<template>
  <div>
    <v-container>
      <v-row no-gutters>
        <v-col 
          cols="12"
          class="pa-2"
          >
          <router-link :to="'/user/' + receiverId"> <h1 class="head">{{receiverName}}</h1> </router-link>
          <main id="ap">
            <section ref="chatArea" class="chat-area">

              <v-container>
      <v-row no-gutters v-for="(message, index) in messages" :key="message._id">
      <template v-if="message.author !== 'you'">
        <v-row 
          
          >
          <p :style="{'word-wrap': 'break-word'}" class="message"
              :class="{ 'message-out': message.author === 'you',
               'message-in': message.author !== 'you','message-out-delete': message.deletearray.length > 0 ? true : false }"
              @click="toggleVisibilityButtons(index)">
              {{ message.body}}
              </p>
        
          
          
          <h1 v-if="visibleButtons[index]" @click="deleteRestoreMessage(message._id, index)">
            <font-awesome-icon icon="redo" style="color: blue"
                   v-if="message.deletearray.includes(userID)"></font-awesome-icon>
             <font-awesome-icon
             v-if="!message.deletearray.includes(userID)" icon="trash" class='fa fa-trash'
             :style="{'color': message.deletearray.length > 0 ? 'red' : 'blue'}"></font-awesome-icon>
          </h1>
        </v-row>
      </template>
      <template v-else>
       <v-row  style="background: white; "  :style="{'color': 'black', 'margin-left': '40%'}">
        <!-- <h1 :style="{'color': 'black', 'margin-left': '40%'}" >{{windowWidth2}}</h1> -->
        <!-- <h1
         v-show="visibleButtons[index]" @click="deleteRestoreMessage(message._id, index)">55</h1> -->
         <h1 v-if="visibleButtons[index]" @click="deleteRestoreMessage(message._id, index)">
            <font-awesome-icon icon="redo" style="color: blue"
                   v-if="message.deletearray.includes(userID)"></font-awesome-icon>
             <font-awesome-icon
             :style="{'color': message.deletearray.length > 0 ? 'red' : 'blue'}"
             v-if="!message.deletearray.includes(userID)" icon="trash" class='fa fa-trash'></font-awesome-icon>
          </h1>
        <p :style="{'word-wrap': 'break-word', 'float': 'right',}" class="message"
        @click="toggleVisibilityButtons(index)"
              :class="{ 'message-out': message.author === 'you', 'message-in': message.author !== 'you', 
              'message-out-delete': message.deletearray.length > 0 ? true : false
            }">
              {{ message.body}}
              </p>
       </v-row>
          
       
        
         
      </template>
        
      </v-row>
              </v-container>

             
             
            </section>

            <section class="chat-inputs">
              <form @submit.prevent="sendMessage()" id="person1-form">
                <label for="person1-input"></label>
                <input v-model="bobMessage" id="person1-input" type="text" placeholder="Type your message" required>
                <button type="submit">
                  <!-- <i class="material-icons" style="font-size:16px;color:blue">navigate_next</i> -->
                  <font-awesome-icon icon="fas fa-paper-plane" style="color: #407FFF; margin-left: 5px;" />
                </button>
              </form>
            </section>
          </main>
        </v-col>
      </v-row>
    </v-container>
     

    <router-link :to="'/encr-private-chat/'+ $route.params.receiverID">
    E-E encryption
    </router-link>
  </div>
</template>

<script>
import io from "socket.io-client";
import { mapActions, mapGetters } from "vuex";
import * as types from "../../store/types";
import axios from "axios";

export default {
    data() {
        return {
            socket: io("http://localhost:3000", {
             query: { token: localStorage.getItem("token") },
             }),
        bobMessage: '',
        youMessage: '',
        receiverName: '',
        receiverId: this.$route.params.receiverID,
        messages: [
        // {
        //     body: 'Welcome to the chat, I\'m Bob!',
        //     author: 'bob'
        // },
        // {
        //     body: 'Thank you Bob',
        //     author: 'you'
        // },
        // {
        //     body: 'You\'re most welcome',
        //     author: 'bob'
        // }
        ],
        windowWidth2: window.innerWidth,
        visibleButtons: [],
        userID: localStorage.getItem('userID')
  }
},
methods: {
  //   sendMessage(direction) {
  //     if (!this.youMessage && !this.bobMessage) {
  //       return
  //     }
  //     if (direction === 'out') {
  //       this.messages.push({body: this.youMessage, author: 'you'})
  //       this.youMessage = ''
  //     } else if (direction === 'in') {
  //       this.messages.push({body: this.bobMessage, author: 'bob'})
  //       this.bobMessage = ''
  //     } else {
  //       alert('something went wrong')
  //     }
  //     this.nextTick(() => {
  //       let messageDisplay = this.$refs.chatArea
  //       messageDisplay.scrollTop = messageDisplay.scrollHeight
  //     })
  // },
    sendMessage() {
      // if (!this.youMessage && !this.bobMessage) {
      //   return
      // }
      // if (direction === 'out') {
      //   this.messages.push({body: this.youMessage, author: 'you'})
      //   this.youMessage = ''
      // } else if (direction === 'in') {
      //   this.messages.push({body: this.bobMessage, author: 'bob'})
      //   this.bobMessage = ''
      // } else {
      //   alert('something went wrong')
      // }
      try {
        // this.messages.push({body: this.bobMessage, author: 'bob', deletearray: []})
      
      this.$store.dispatch('socketOnConnection', {msg: this.bobMessage, id: this.$route.params.receiverID})
      } catch (error) {
        console.log('the error heree')
      }
      // this.nextTick(() => {
      //   let messageDisplay = this.$refs.chatArea
      //   messageDisplay.scrollTop = messageDisplay.scrollHeight
      // })
      this.$nextTick(() => {
     document.getElementsByClassName('chat-area')[0].scrollTop = '11111';                      
  })
  },
  onResize() {
      console.log("App.vue-onResize()", this.windowWidth2);
      this.windowWidth2 = window.innerWidth;
       
    },
    toggleVisibilityButtons(index) {
      
      this.$set(this.visibleButtons, index, !this.visibleButtons[index])
      console.log(this.visibleButtons)
      console.log(this.messages[index])
      // console.log(this.messages[index])
    },
    deleteRestoreMessage(id, index) {
    console.log('message id is: ', id)
    axios.post("http://localhost:3000/chat-message/delete-msg",
    {id: id ,receiverID: this.$route.params.receiverID},
       {
        headers: {
          authorization: localStorage.getItem("token"),
        },
      }).then((res) => {
       
        if (res.data.success) {
          console.log('eee', res)
          if (res.data.message == "message confirm delete") {
            this.messages.splice(index, 1)
            this.visibleButtons.splice(index, 1)
          }
          if (res.data.message == 'message delete request') {
            const messageToUpdate = this.messages.find(m => m._id === id);
            if (messageToUpdate) {
              messageToUpdate.deletearray.push(localStorage.getItem('userID'));
            }
          }
          if (res.data.message == 'message restore') {
            const messageToUpdate = this.messages.find(m => m._id === id);
            if (messageToUpdate) {
              messageToUpdate.deletearray = [];
            }
          }
        }
        console.log(res)
      })
  },
  ...mapActions({
    getChatMessage: types.GET_CHAT_MESSAGES
  })
},
computed: {
  ...mapGetters({
    getMessages: types.GET_CHAT_MESSAGES
  })
},
watch: {
    windowWidth2(oldWidth, newWidth) {
      var  txt = `it changed to ${newWidth} from ${oldWidth}`;
      console.log('sxxx', txt)
    },
  },
created() {
  // this.getChatMessage(this.$route.params.receiverID).then(() => {
  //   const msgs = this.getMessages;
  //   console.log('222', msgs)
  // })
  

      axios.get("http://localhost:3000/chat-message/" + this.$route.params.receiverID,
       {
        headers: {
          authorization: localStorage.getItem("token"),
        },
      }).then((res) => {
        console.log('msgd22', res.data)
        var msgs = res.data;
        msgs.map((m) => {
          if (m.from == localStorage.getItem('userID')) {
            console.log('0000', localStorage.getItem('userID'), '0000', m.from )
            this.messages.push({body: m.text, author: 'bob', _id: m._id, deletearray: m.delete})
            this.$nextTick(() => {
              document.getElementsByClassName('chat-area')[0].scrollTop = '11111';                      
              }) 
            return {
              body: m.text,
              author: 'bob'
            }
          } else {
             this.messages.push({body: m.text, author: 'you', _id: m._id, deletearray: m.delete})
            
           return {
              body: m.text,
              author: 'you'
            }
          }
        })
        console.log('444', this.messages)
      })
  
  this.socket.on('msg', (msg) => {
    if (msg.senderId ==  localStorage.getItem('userID')) {
       console.log('ChatOneToOne new message 2026', msg)
       this.messages.push({body: msg.msg, author: 'bob', deletearray: msg.delete, _id: msg._id})
       this.$nextTick(() => {
              document.getElementsByClassName('chat-area')[0].scrollTop = '11111';                      
        })
    }
   
    else if (msg.senderId ==  this.$route.params.receiverID ) {
      
       {
        console.log('ChatOneToOne new message 2025', msg)
      this.messages.push({body: msg.msg, author: 'you', deletearray: msg.delete, _id: msg._id})
       this.$nextTick(() => {
              document.getElementsByClassName('chat-area')[0].scrollTop = '11111';                      
        }) 
      }
    } 
   
  })

  // socket for delete mesage request
  this.socket.on('message_deleted_request', (msg) => {
    console.log('message_deleted_request', msg)
    const messageToUpdate = this.messages.find(m => m._id === msg.messageId);
    if (messageToUpdate) {
      messageToUpdate.deletearray.push(this.$route.params.receiverID);
    }
  })
  //
  // socket for delete mesage permantly
this.socket.on('message_restore', (msg) => {
    console.log('message_restore', msg)
    const messageToUpdate = this.messages.find(m => m._id === msg.messageId);
    if (messageToUpdate) {
      messageToUpdate.deletearray = [];
    }
  })
  //
// socket for delete mesage permantly
this.socket.on('message_delete_permenant', (msg) => {
    console.log('message_deleted_permanent', msg)
    this.messages = this.messages.filter(function (item) {
      return item._id !== msg.messageId
    })
  })
  //


  axios.post('http://localhost:3000/users/get-name-chat', 
  {id: this.$route.params.receiverID },
  {
        headers: {
          authorization: localStorage.getItem("token"),
        }
  }).then(res => {
    this.receiverName = res.data.name
  })

  if (this.$route.params.receiverID == localStorage.getItem('userID')) {
      this.$router.push('/')
  }

  axios.post("http://localhost:3000/get-all-messages/read-message", 
          {receiverId: this.$route.params.receiverID }, {
            headers: { authorization: localStorage.getItem("token") },
          })
          .then((res) => {
            
            this.socket.emit("new-post", res);
          });

          this.socket.emit('some', 'some')
}, 
mounted() {
    this.$nextTick(() => {
      window.addEventListener("resize", this.onResize);
    });
  },
destroyed() {
 this.messages = []
 this.$store.dispatch(types.RESET_CHAT_MESSAGES)
 window.removeEventListener("resize", this.onResize);
}
}
</script>

<style scoped>
.headline {
  text-align: center;
  font-weight: 100;
  color: white;
}
.chat-area {
/*   border: 1px solid #ccc; */
  background: white;
  height: 50vh;
  padding: 1em;
  overflow: auto;
  max-width: 100%;
  margin: 0 auto 2em auto;
  box-shadow: 2px 2px 5px 2px rgba(0, 0, 0, 0.3)
}
.message {
  width: 45%;
  border-radius: 10px;
  padding: .5em;
/*   margin-bottom: .5em; */
  font-size: .8em;
}
.message-out {
  background: #407FFF;
  color: white;
  /* margin-left: 0%; */
  /* float: right; */
}
.message-out-delete {
  background: red !important;
  color: white !important;
  /* margin-left: 0%; */
  /* float: right; */
}
.message-in {
  background: #F1F0F0;
  color: black;
}
.chat-inputs {
  display: flex;
  justify-content: space-between;
}
#person1-input {
  padding: .5em;
}
#person2-input {
  padding: .5em;  
}

#person1-form {
  margin: auto;
  padding: 10px;
}
.head {
  margin: auto;
  padding: 10px;
}
/* .msg-content {
  display: inline;
} */
</style>