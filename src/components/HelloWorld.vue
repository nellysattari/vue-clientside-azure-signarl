<template>
<div>
    <h2 class="text-center" style="margin-top: 0; padding-top: 30px; padding-bottom: 30px;">Asp.net Core SignalR Group Chat</h2>
    <div class="container" style="height: calc(100% - 110px);">
        <div id="messages" style="background-color: whitesmoke; "></div>
        <div style="width: 100%; border-left-style: ridge; border-right-style: ridge;">
            <textarea id="message"
                          style="width: 100%; padding: 5px 10px; border-style: hidden;"
                          placeholder="Type message and press Enter to send..."></textarea>
        </div>
        <div style="overflow: auto; border-style: ridge; border-top-style: hidden;">
            <button class="btn-warning pull-right" id="echo">Echo</button>
            <button class="btn-success pull-right" id="sendmessage">Send</button>
        </div>
    </div>
    <div class="modal alert alert-danger fade" id="myModal" tabindex="-1" role="dialog" aria-labelledby="myModalLabel">
        <div class="modal-dialog" role="document">
            <div class="modal-content">
                <div class="modal-header">
                    <div>Connection Error...</div>
                    <div><strong style="font-size: 1.5em;">Hit Refresh/F5</strong> to rejoin. ;)</div>
                </div>
            </div>
        </div>
    </div>
     </div>
</template>

<script>
import { HubConnection, HubConnectionBuilder } from '@aspnet/signalr'
export default {
  name: 'HelloWorld',
  props: {
    msg: String,
    hubConnection: HubConnection
  },
  created: function () {
    // `this` points to the vm instance
    console.log('a is: ')
    let builder = new HubConnectionBuilder()
    this.hubConnection = builder.withUrl('https://nellysignalr20190128105854.azurewebsites.net/chat').build()
    this.hubConnection.on('broadcastMessage', (message) => {
    // this.messages.push(message);
      console.log('message,', message)
    })

    // starting the connection
    this.hubConnection.start()
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

html, body {
    padding: 0;
    height: 100%;
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

textarea:focus {
    outline: none !important;
}

.system-message {
    background: #87CEFA;
}

.broadcast-message {
    display: inline-block;
    background: yellow;
    margin: auto;
    padding: 5px 10px;
}

.message-entry {
    overflow: auto;
    margin: 8px 0;
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
