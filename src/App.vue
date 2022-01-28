<template>
  <main>
    <div>
      <ul class="msglist">
        <li
          v-for="(message, index) in messages.slice(0, next)"
          v-bind:key="index"
          :class="message.owner"
        >
          {{ message.text }}
        </li>
      </ul>
    </div>
    <div class="input-area">
      <textarea placeholder="Type your message" v-model="input" @keyup.enter="send" :disabled="next === 0 || prevStep === (messages.length+1)"></textarea>
      <button
        class="sendButton"
        :disabled="prevStep === (messages.length+1)"
        @click="send"
      >
        {{ next === 0 ? "Let's chat" : 'Send Message' }}
      </button>
    </div>
  </main>
</template>

<script>
import axios from 'axios'
export default {
  name: "App",
  components: {},
  data: () => ({
    prevStep: 0,
    next: 0,
    input: "",
    messages: [],
  }),
  mounted () {
    axios.get("messages.json")
    .then((res) => this.messages = res.data)
    .catch((err) => console.log(err))
  },
  updated(){
    var chatHistory = document.getElementsByClassName("msglist");
    chatHistory[0].scrollTop = chatHistory[0].clientHeight;
  },
  methods: {
    send() {
      let active = true;
      while (active) {
        if (this.messages[this.next] && typeof this.messages[this.next].ask === "undefined") {
            this.next += 1;
        } else {
          if (this.prevStep > 0) {
            this.next += 1;
            this.messages.splice(this.prevStep, 0, {
              text: this.input,
              owner: "me",
            });
          this.input = ''
          }
          this.next += 1;
          this.prevStep = this.next;
          active = false;
        }
      }
    }
  },
};
</script>

<style>
main {
  display: flex;
  flex-direction: column;
}

ul li {
  display: inline-block;
  padding: 20px;
  border-radius: 30px;
  margin-bottom: 2px;
  font-family: Helvetica, Arial, sans-serif;
  align-self: center;
  flex-shrink: 0;
}
.him {
  background: #eee;
  align-self: flex-start;
}
.me {
  float: right;
  background: #0084ff;
  align-self: flex-end;
}
.him + .me {
  border-bottom-right-radius: 5px;
}
.me + .me {
  border-top-right-radius: 5px;
  border-bottom-right-radius: 5px;
}
.me:last-of-type {
  border-bottom-right-radius: 30px;
}
.input-area {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  padding: 10px;
  z-index: 100;
  background: #ffffff;
  display: flex;
  align-items: flex-end;
}
textarea {
  width: 100%;
  resize: none;
  padding: 10px;
  margin-right: 24px;
  outline: none;
  height: 100px;
  font-size: 16px;
  border-radius: 10px;
}
.sendButton { 
  width: 150px;
  height: 50px;
  cursor: pointer;
  background-color: #56c8d8;
  outline: none;
  border: none;
  font-size: 16px;
  cursor: pointer;
  border-radius: 10px;
  }
  .msglist {
    list-style: none;
    margin: 0;
    padding: 0;
    overflow-y: scroll;
    height: 568px;
    display: flex;
    flex-direction: column;
    z-index: 0;
  }
</style>