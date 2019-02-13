<template>
  <div>
    <h2 class="text-center" style="margin-top: 0; padding-top: 30px;  ">Hi {{username}}</h2>
    <h3>Welcome to the Bidding Flowers @Martin Place</h3>
    <div class="container" style="height: calc(100% - 110px);margin-bottom:50px;">
      <div id="allMessages" style="background-color: whitesmoke; ">
        <message-entry
          v-for="entry in entries"
          :encodedMsg="entry.encodedMsg"
          :encodedName="entry.encodedName"
          :user="username"
        />
      </div>
      <div
        class="messageWrapper"
        style="width: 100%; border-left-style: ridge; border-right-style: ridge;"
      >
        <textarea
          id="message"
          v-model="messageInput"
          v-on:keypress="keymonitor($event)"
          style="width: 100%; padding: 5px 10px; border-style: hidden;"
          placeholder="How much you like to bid..."
        ></textarea>
      </div>
      <div style="overflow: auto; border-style: ridge; border-top-style: hidden;">
        <button class="btn-warning pull-right" id="echo" v-on:click="echo()">Echo</button>
        <button class="btn-success pull-right" id="sendmessage" v-on:click="sendMessage()">Send</button>
      </div>
    </div>
    <div
      class="modal alert alert-danger fade"
      id="myModal"
      tabindex="-1"
      role="dialog"
      aria-labelledby="myModalLabel"
    >
      <div class="modal-dialog" role="document">
        <div class="modal-content">
          <div class="modal-header">
            <div>Connection Error...</div>
            <div>
              <strong style="font-size: 1.5em;">Hit Refresh/F5</strong> to rejoin. ;)
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { HubConnection, HubConnectionBuilder } from "@aspnet/signalr";
import * as signalR from "@aspnet/signalr";
import MessageEntry from "@/components/MessageEntry.vue";

export default {
  name: "ChatRoom",
  components: {
    MessageEntry
  },
  data: function() {
    return {
      username: "",
      messageInput: "",
      promptMessage: "Enter your name:",
      hubConnection: HubConnection,
      entries: []
    };
  },
  created: function() {
    do {
      var username = prompt(this.promptMessage, this.username);
      if (
        !username ||
        username.startsWith("_") ||
        username.indexOf("<") > -1 ||
        username.indexOf(">") > -1
      ) {
        this.username = "Anonymous";
        this.promptMessage = "Invalid input. Enter your name:";
      } else {
        this.username = username;
      }
    } while (!username);
    this.hubConnection = new signalR.HubConnectionBuilder()
      .withUrl("http://localhost:5000/chat", {
        withCredentials: true
      })
      .build();
    this.hubConnection.on("broadcastMessage", this.broadcastCallBack);
    this.hubConnection.on("echo", this.broadcastCallBack);
    this.hubConnection
      .start()
      .then(() => {
        console.log("hub started");
        this.hubConnection.invoke(
          "broadcastMessage",
          "_SYSTEM_",
          this.username + " JOINED"
        );
      })
      .catch(e => {
        console.error(
          `An error occurred while connecting to operations hub. Details ${e}`
        );
      });
  },
  methods: {
    sendMessage: function() {
      // called when button sendmessage is invoked
      // it triggers the hub method broadcastMessage
      // Then it invokes addMessage since it is listening
      this.hubConnection.send(
        "broadcastMessage",
        this.username,
        this.messageInput
      );
    },
    broadcastCallBack: function(name, message) {
      if (!message) return;
      this.messageInput = message
        .replace(/&/g, "&amp;")
        .replace(/</g, "&lt;")
        .replace(/>/g, "&gt;");
      this.addMessage(name, this.messageInput);
    },
    addMessage: function(name, encodedMsg) {
      if (!message) return;
      let encodedName = !name ? this.username : name;
      this.entries.push({ encodedName, encodedMsg });
      this.messageInput = "";
    },
    keymonitor: function(event) {
      if (event.keyCode === 13) {
        event.preventDefault();
        this.sendMessage();
        return false;
      }
    },
    echo: function() {
      this.hubConnection.send("echo", this.username, this.messageInput);
    },
    onConnected: function(connection) {
      console.log("connection started");
      connection.send(
        "broadcastMessage",
        "_SYSTEM_",
        this.username + " JOINED"
      );
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->

<style scoped>
html,
body {
  padding: 0;
  height: 100%;
  background-color: yellow;
}

#allMessages {
  width: 100%;
  border: 1px solid #ccc;
  height: calc(100% - 120px);
  float: none;
  margin: 0px auto;
  padding-left: 0px;
  overflow-y: auto;
}

.message-entry {
  overflow: auto;
  margin: 8px 0;
}

textarea:focus {
  outline: none !important;
}

.system-message {
  background: #87cefa;
}

.broadcast-message {
  display: inline-block;
  background: yellow;
  margin: auto;
  padding: 5px 10px;
}

.message-avatar {
  display: inline-block;
  padding: 10px;
  max-width: 8em;
  word-wrap: break-word;
}

.message-content {
  display: inline-block;
  background-color: #b2e281;
  padding: 10px;
  margin: 0 0.5em;
  max-width: calc(60%);
  word-wrap: break-word;
}

.message-content.pull-left:before {
  width: 0;
  height: 0;
  display: inline-block;
  float: left;
  border-top: 10px solid transparent;
  border-bottom: 10px solid transparent;
  border-right: 10px solid #b2e281;
  margin: 15px 0;
}

.message-content.pull-right:after {
  width: 0;
  height: 0;
  display: inline-block;
  float: right;
  border-top: 10px solid transparent;
  border-bottom: 10px solid transparent;
  border-left: 10px solid #b2e281;
  margin: 15px 0;
}
</style>
