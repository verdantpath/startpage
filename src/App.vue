<template>
  <div id="app">
    <div id="main">
      <img :src='myImage'>

      <p>{{ myImage }}</p>
      <h1>{{ msg }}</h1>
      <input v-model="query" type="text" id="search" placeholder="Search Photos">
      <button @click="searchPhotos" id="search-submit">Search</button>
      <p>{{ query }}</p>
      <!-- <h3 class="date">{{ date }}</h3> -->
      <div class="date">
      <h3>{{ hours }}:{{ minutes }}:{{ seconds }}</h3>
      <p>Good {{ timeOfDay }}</p>
      </div>
      <h5>{{ timeTest }}</h5>
      <h2>Essential Links</h2>
      <ul>
        <li><a href="https://vuejs.org" target="_blank">Core Docs</a></li>
        <li><a href="https://forum.vuejs.org" target="_blank">Forum</a></li>
        <li><a href="https://chat.vuejs.org" target="_blank">Community Chat</a></li>
        <li><a href="https://twitter.com/vuejs" target="_blank">Twitter</a></li>
      </ul>
      <h2>Ecosystem</h2>
      <ul>
        <li><a href="http://router.vuejs.org/" target="_blank">vue-router</a></li>
        <li><a href="http://vuex.vuejs.org/" target="_blank">vuex</a></li>
        <li><a href="http://vue-loader.vuejs.org/" target="_blank">vue-loader</a></li>
        <li><a href="https://github.com/vuejs/awesome-vue" target="_blank">awesome-vue</a></li>
      </ul>
    </div><!-- / #main -->
  </div><!-- / #app -->
</template>

<script>

export default {
  name: 'app',
  data () {
    return {
      msg: 'Welcome to Your Vue.js App',
      clientId: 'V8_Yy2IBu90rfZ77cYFLM2MFLS8h09Szdcv6XHaO19c',
      query: 'trees',
      photoUrl: 'asdf',
      date: new Date().toLocaleTimeString('en-US', { hour: 'numeric', minute: 'numeric' , second: 'numeric' }),
      timeTest: '',
      hours: '',
      minutes: '',
      seconds: '',
      timeOfDay: null,
    }
  },
  computed: {
    myImage: function() {
      return ('https://api.unsplash.com/search/photos/?client_id=' + this.clientId + '&query=' + this.query).toString()
    },
    myImageUrl: function() {
      console.log(myImage.urls.small)
    }
  },
  methods: {
    searchPhotos: function() {
      console.log('clicked search photos');
      fetch('https://api.unsplash.com/search/photos/?client_id=' + this.clientId + '&query=' + this.query)
        .then(data => data.json())
        .then(data => {
           console.log(data);
           data.results.forEach(function(item) {
            let result = item.urls.full;
            console.log(result);
            

            
           })
        });
        console.log(this.photoUrl);
    },
    getTime: function() {
      const date = new Date();
      this.timeTest = this.checkSingleDigit(date.getSeconds());
      this.hours = date.getHours() < 12 ? date.getHours() : date.getHours() - 12;
      this.minutes = this.checkSingleDigit(date.getMinutes());
      this.seconds = this.checkSingleDigit(date.getSeconds());
      setTimeout(this.getTime, 1000);
    },
    checkSingleDigit: function(digit) {
      return ('0' + digit).slice(-2)
    },
    checkTimeOfDay: function() {
      const date = new Date();
      if (date.getHours() < 12) {
        this.timeOfDay = 'morning';
      } else if (date.getHours() > 12 && date.getHours() < 18) {
        this.timeOfDay = 'afternoon';
      } else {
        this.timeOfDay = 'evening';
      }
    }
  },
  created: function () {
    // setInterval(function() {
    //   // console.log(new Date().toLocaleTimeString('en-US', { hour: 'numeric', minute: 'numeric' , second: 'numeric' }));
    // }, 1000);
    // fetch('https://api.unsplash.com/photos/random?client_id=' + this.clientId)
    // .then(response => response.json())
    // .then(data => console.log(data))
    
    // console.log('a is: ' + this.msg)
    this.getTime();
    this.checkTimeOfDay();
  },

}

</script>

<style lang="scss">
body {
  margin: 0;
}

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #fff;
  // padding-top: 60px;
  background: #aaabef;
  //background-image: url(https://images.unsplash.com/photo-1441974231531-c6227db76b6e?crop=entropy&cs=srgb&fm=jpg&ixid=MnwyMTE1MTV8MHwxfHNlYXJjaHwzfHx0cmVlc3xlbnwwfHx8fDE2MTU3ODI2OTI&ixlib=rb-1.2.1&q=85);
  background-image: url(https://images.unsplash.com/photo-1477346611705-65d1883cee1e?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1470&q=80);
  // background-image: url(https://api.unsplash.com/search/photos/?client_id=V8_Yy2IBu90rfZ77cYFLM2MFLS8h09Szdcv6XHaO19c&query=trees);
  height: 100vh;
}

#main {
  padding-top: 60px;
}

h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  // color: #42b983;
  color: #fff;
}

.date {
  h3 {
    font-size: 8rem;
    margin-bottom: 0;
  }
  p {
    font-size: 4rem;
    margin-top: 0;
    font-weight: bold;
  }
}
</style>
