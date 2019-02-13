<template>
  <div class="message-entry" v-bind:class="featureclass">
     <span v-if="!show">{{encodedMsg}}</span>
    <div class="message-avatar" v-bind:class="alignmentclass" v-if="show">{{encodedName}}</div>
    <div class="message-content" v-bind:class="alignmentclass"  v-if="show">{{encodedMsg}}</div>
  </div>
</template>

<script>
export default {
  name: "MessageEntry",
  props: {
    encodedMsg: String,
    encodedName: String,
    user:String,
  },
  created: function() {
    console.log('user',this.user);
    if (this.encodedName === "_SYSTEM_") {
      this.featureclass="text-center system-message";
      this.show=false;
    } else if (this.encodedName === "_BROADCAST_") {
       this.featureclass="text-center";
    } else if (this.encodedName === this.user) {
      this.alignmentclass = "pull-right";
    } else {
      this.alignmentclass = "pull-left";
    }
  },
  data: function() {
    return {
      featureclass: '',
      messageInput: "",
      alignmentclass: "",
      show:true
    };
  }
};
</script>

<style scoped>
html,
body {
  padding: 0;
  height: 100%;
  background-color: yellow;
}

#messages {
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
