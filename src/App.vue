<template>
  <div id="app">
    <h2>Multi-Player Pong Hackathon</h2>
    <svg
      xmlns="http://www.w3.org/2000/svg"
      baseProfile="tiny"
      version="1.2"
      viewBox="0 0 600 400"
      width="100%"
    >
      <rect width="100%" height="100%" fill="white" />
      <rect :x="x" :y="y" width="10" height="10" fill="red"></rect>
      <rect x="10" :y="a" width="10" height="100" fill="black"></rect>
      <rect x="580" :y="b" width="10" height="100" fill="black"></rect>
    </svg>
    <!-- <button v-on:click="sendMessage('hello')">Send Message</button> -->
  </div>
</template>

<script>
export default {
  name: "App",
  data: function() {
    return {
      connection: null,
      pos: null,
      x: 300,
      y: 200,
      a: 0,
      b: 0
    };
  },
  created: function() {
    console.log("Starting connection to WebSocket Server");
    //this.connection = new WebSocket("wss://echo.websocket.org")
    // this.connection = new WebSocket("ws://localhost:8080/ws");
    this.connection = new WebSocket("ws://"  + location.host + "/ws");

    let self = this;
    this.connection.onmessage = function(event) {
      //   console.log(event);
      if (event.type == "message" && event.data) {
        let data = JSON.parse(event.data);
        console.log(data);
        if (data.type == 2) {
          let body = JSON.parse(data.body);
          self.x = body.x;
          self.y = body.y;
          self.a = body.a;
          self.b = body.b;
        } /* else if (data.type == 1) {
            let body = JSON.parse(data.body);
            //   console.log("Got ", body.key);
            if (body.key == "q") {
                self.a -= 5
            } else if (body.key == "a") {
                self.a += 5
            } else if (body.key == "p") {
                self.b -= 5
            } else if (body.key == ";") {
                self.b += 5
            }
        }*/
      }
    };
    window.addEventListener("keydown", this.doCommand);
  },

  destroyed() {
    window.removeEventListener("keydown", this.doCommand);
  },
  methods: {
    sendMessage: function(message) {
      //   console.log(this.connection);
      this.connection.send(message);
    },
    doCommand(e) {
      let cmd = String.fromCharCode(e.keyCode).toLowerCase();
      // do stuff
      this.sendMessage(JSON.stringify({ key: cmd }));
    }
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  background-color: #f7f7f7;
}
</style>
