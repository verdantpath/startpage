<template>
  <div id="app" :class="{ 'dark-mode': darkMode }" :style="{ backgroundImage: 'url(' + photoUrl + '&w=2000)' }">
    <div id="main">
    <div id="theme">
      <span>Light Mode</span>
      <div class="ui toggle checkbox">
        <input type="checkbox" v-model="darkMode" name="choose-theme">
        <label for="choose-theme">Dark Mode</label>
      </div>
    </div>
      <div id="weather">
        <h3>{{ weather.temp }} &deg;</h3>
        <h4 @click="editWeatherCity" v-if="!showEditWeatherCity">{{ weather.city }}</h4>
        <div v-else class="ui input">
          <input type="text" v-model="weather.city" />
          <button @click="getWeather">Go</button>
        </div>
      </div>
      <!-- <img :src='photoUrl'> -->

      <!-- <p>{{ myImage }}</p> -->
      <h1>{{ msg }}</h1>

      <div class="gcse-search"></div>
      
      <!-- <h3 class="date">{{ date }}</h3> -->
      <div @mouseover="showSecondsToggle = true" @mouseleave="showSecondsToggle = false" class="date">
        <div class="clock">
          <h3 v-if="showSeconds">{{ hours }}:{{ minutes }}:{{ seconds }}</h3>
          <h3 v-if="!showSeconds">{{ hours }}:{{ minutes }}</h3>
          <div v-if="showSecondsToggle" class="ui toggle checkbox">
            <input type="checkbox" v-model="showSeconds" name="show-seconds">
            <label for="show-seconds">Seconds</label>
          </div>
        </div>
      <p>Good {{ timeOfDay }}</p>
      </div>
      <div id="background-photo-search">
        <div class="ui input">
          <input v-model="query" type="text" id="search" placeholder="Background Photo">
          <button @click="searchPhotos" id="search-submit" class="ui primary button">Change Photo</button>
        </div>
        <p>Search to change the background image</p>
      </div>
      <div id="favs-wrapper" @mouseover="showEditFavsUi = true" @mouseleave="showEditFavsUi = false">
        <div id="favs">
          <ul>
            <li v-for="fav in favs" :key="fav.id" class="fav-item">
              <a :href="'https://' + fav.site" target="_blank"><img :src="fav.image + fav.site "></a>
            </li>
          </ul>
          <div id="edit-favs" v-if="showEditFavs">
            <p>Edit Favs</p>
            <form>
              <ul>
                <li v-for="fav in favs" :key="fav.id">
                  <label>{{ fav.id }}</label><div class="ui input">
                  <input type="text" v-model="fav.editSite" /></div>
                </li>
              </ul>
              <button @click="updateFavs">Done</button>
            </form>
          </div>
          <transition name="fade">
            <div @click="editFavs" class="edit-favs" v-if="showEditFavsUi">...</div>
          </transition>
        </div>
      </div>
      <div id="quote">
        <p>"{{ quote.content }}" - {{ quote.author }}</p>
      </div>
    </div><!-- / #main -->
  </div><!-- / #app -->
</template>

<script>
import secure from './secure'

export default {
  name: 'app',
  data () {
    return {
      secretTest: secure.apiKeyTest,
      msg: 'Welcome to Your Vue.js Start Page',
      clientId: secure.unsplashApi,
      query: 'mountains',
      photoUrl: 'https://images.unsplash.com/photo-1477346611705-65d1883cee1e?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1470&q=80',
      date: new Date().toLocaleTimeString('en-US', { hour: 'numeric', minute: 'numeric' , second: 'numeric' }),
      timeTest: '',
      hours: '',
      minutes: '',
      seconds: '',
      timeOfDay: null,
      bgContainer: document.querySelector('#app'),
      showEditFavs: false,
      showEditFavsUi: false,
      showSeconds: false,
      showSecondsToggle: false,
      favs: {
        one: {
          id: 1,
          editSite: 'stackoverflow.com',
          site: 'stackoverflow.com',
          image: '//logo.clearbit.com/'
        },
        two: {
          id: 2,
          editSite: 'spotify.com',
          site: 'spotify.com',
          image: '//logo.clearbit.com/'
        },
        three: {
          id: 3,
          editSite: 'discogs.com',
          site: 'discogs.com',
          image: '//logo.clearbit.com/'
        },
        four: {
          id: 4,
          editSite: 'trello.com',
          site: 'trello.com',
          image: '//logo.clearbit.com/'
        },
      },
      weather: {
        apiKey: secure.openWeatherMapApi,
        city: 'Portland',
        units: 'imperial',
        temp: null,
        // current: {
        //   temp: '',
        //   city: '',
        // }
      },
      showEditWeatherCity: false,
      quote: {
       content: '',
       author: ''
      },
      darkMode: false,
    }
  },
  computed: {
    myImage: function() {
      return ('https://api.unsplash.com/search/photos/?client_id=' + this.clientId + '&query=' + this.query).toString()
    },
    myImageUrl: function() {
      console.log(myImage.urls.small)
    },
    
  },
  methods: {
    searchPhotos: function() {
      console.log('clicked search photos');
      fetch('https://api.unsplash.com/search/photos/?client_id=' + this.clientId + '&query=' + this.query + '&orientation=landscape&per_page=30')
        .then(data => data.json())
        .then(data => {
          let r = Math.floor(Math.random() * 30);
          console.log("%% r", r, typeof(r));

           // console.log(data);
           this.photoUrl = data.results[r].urls.full;
           console.log(this.photoUrl); 
           data.results.forEach(function(item) {
             // let result = item.urls.full;
             // console.log("result: ", result);
           

            
           })
        });
    },
    getTime: function() {
      const date = new Date();
      this.timeTest = this.checkSingleDigit(date.getSeconds());
      this.hours = date.getHours() <= 12 ? date.getHours() : date.getHours() - 12;
      this.minutes = this.checkSingleDigit(date.getMinutes());
      this.seconds = this.checkSingleDigit(date.getSeconds());
      setTimeout(this.getTime, 1000);
    },
    checkSingleDigit: function(digit) {
      return ('0' + digit).slice(-2)
    },
    hoverShowSeconds() {
      this.showSecondsToggle = true;
    },
    checkTimeOfDay: function() {
      const date = new Date();
      if (date.getHours() < 12) {
        this.timeOfDay = 'morning';
      } else if (date.getHours() >= 12 && date.getHours() < 18 ) {
        this.timeOfDay = 'afternoon';
      } else {
        this.timeOfDay = 'evening';
      }
      setTimeout(this.checkTimeOfDay, 1000)
    },
    bgPhoto() {
      // var el = document.querySelector('#app')
      this.bgContainer.classList.add('test');
      // el.style.backgroundImage = 'url(this.photoUrl)';
      console.log('$$$ bgPhoto', this.photoUrl);
    },
    loadGoogleSearch() {
      // if (window.google-search) {
      //   // The script is already loaded, so just reload the embeds
      //   window.google-search.widgets.load();
      // } else if (!document.getElementById('google-search-widgets')) {
      //   const embed = document.createElement('script');
      //   embed.id = 'google-search-widgets'
      //   embed.src = 'https://cse.google.com/cse.js?cx=a93997be9c4611ba4';
      //   document.body.appendChild(embed);
      //   // And when the script loads, the embeds will load too
      //
      const embed = document.createElement('script');
      embed.id = 'google-search-widgets'
      embed.src = 'https://cse.google.com/cse.js?cx=a93997be9c4611ba4';
      document.body.appendChild(embed);
      //}
    },
    editFavs() {
      this.showEditFavs = !this.showEditFavs;
    },
    editFavsUi() {
      this.showEditFavsUi = true;
    },
    updateFavs(event) {
      event.preventDefault;
      // console.log(this.favs);
      // this.favs.forEach(fav => {
      //   fav.site = fav.editSite;
      // });
      // for(const fav in this.favs) {
      //  fav.site = fav.editSite;
      //  console.log(fav);
      // }
      this.favs.one.site = this.favs.one.editSite;
      this.favs.two.site = this.favs.two.editSite;
      this.favs.three.site = this.favs.three.editSite;
      this.favs.four.site = this.favs.four.editSite;
      
      this.showEditFavs = false;
    },
    getWeather() {
      fetch("https://api.openweathermap.org/data/2.5/weather?q=" 
        + this.weather.city + "&units=" 
        + this.weather.units + "&appid=" 
        + this.weather.apiKey)
      .then(response => response.json())
      .then(data => {
        console.log(data.name, parseInt(data.main.temp));
        this.weather.temp = parseInt(data.main.temp);
        this.weather.city = data.name;
        this.showEditWeatherCity = false;
      });
    },
    editWeatherCity() {
      this.showEditWeatherCity = true;
    },
    getQuote() {
      fetch("https://api.quotable.io/random")
      .then(response => response.json())
      .then(data => {
        console.log(data);
        this.quote.content = data.content;
        this.quote.author = data.author;
      })
      .catch(err => {
        console.error(err);
      });
    },
    checkDarkMode() {
     console.log("%^&", this.darkMode);
    },
  },
  created: function () {
    // setInterval(function() {
    //   // console.log(new Date().toLocaleTimeString('en-US', { hour: 'numeric', minute: 'numeric' , second: 'numeric' }));
    // }, 1000);
    // fetch('https://api.unsplash.com/photos/random?client_id=' + this.clientId)
    // .then(response => response.json())
    // .then(data => console.log(data))
    
    // console.log('a is: ' + this.msg);
    console.log(process.env.NODE_ENV);
    console.log(process.env.VUE_APP_T);
    this.getTime();
    this.checkTimeOfDay();
    this.getWeather();
    this.getQuote();
    this.checkDarkMode();
    // this.searchPhotos();
    // this.bgPhoto();
    window.addEventListener('keydown', (e) => {
      if (e.key == 'Enter') {
        console.log('pressed enter');
        this.searchPhotos(e);
      }
    });

    this.loadGoogleSearch();
  },
  mounted: function() {
    console.log("!!! mounted");
  }
}

</script>

<style lang="scss">
@mixin text-shadow {
  text-shadow: 0 0 5px rgba(0, 0, 0, 0.2);
}
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
  // background: #aaabef;
  background: rgba(170,171,239,0.5);
  // background: linear-gradient(180deg, rgba(170,171,239,1) 0%, rgba(170,171,239,1) 35%, rgba(243,243,255,1) 100%); 
  //background-image: url(https://images.unsplash.com/photo-1441974231531-c6227db76b6e?crop=entropy&cs=srgb&fm=jpg&ixid=MnwyMTE1MTV8MHwxfHNlYXJjaHwzfHx0cmVlc3xlbnwwfHx8fDE2MTU3ODI2OTI&ixlib=rb-1.2.1&q=85);
  background-image: url(https://images.unsplash.com/photo-1477346611705-65d1883cee1e?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1470&q=80);
  // background-image: url(https://api.unsplash.com/search/photos/?client_id=V8_Yy2IBu90rfZ77cYFLM2MFLS8h09Szdcv6XHaO19c&query=trees);
  // background-image: url(this.photoUrl);
  height: 100vh;
  background-repeat: no-repeat;
  background-size: cover;
  background-position: center;
}

.test {
  margin-top:5px;
}

#main {
  padding: 1.5em;
  height: 100%;
  display: flex;
  flex-direction: column;
}

h1, h2 {
  @include text-shadow;
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
  @include text-shadow;
  margin: 50px 0;
  h3 {
    font-size: 8rem;
    margin-bottom: 0;
    display: inline-block;
  }
  p {
    font-size: 4rem;
    margin-top: 0;
    font-weight: bold;
  }
  .clock {
    position: relative;
    display: inline-block;
  }
  .ui.toggle.checkbox {
    transform: scale(0.5);
    position: absolute;
    right: -10em;
    bottom: 1.5em;
  }
  .ui.toggle.checkbox label,
  .ui.toggle.checkbox input:checked~label  {
    color: white !important;
    font-size: 2em;
    font-weight: bold;
  }
}

.gsc-control-cse {
  background-color: transparent !important;
  border: none !important;
}

#___gcse_0 {
  margin: 0 auto;
  width: 45%;
}



.gsc-search-button-v2 {
  padding-top: 8px !important;
  padding-bottom: 9px !important;
}

.gsc-thumbnail-inside {
  text-align: left;
}
#background-photo-search {
  margin-bottom: 3em;
  button {
    margin: 0 0 0 3px;
  }
}
#favs-wrapper {
  padding: 0 3em 2em;
  display: inline-block;
  margin-bottom: auto;
}
#favs {
  display: inline-block;
  position: relative
}
.fav-item, .fav-item img {
  width: 64px;
  height: 64px;
}
#edit-favs {
  label {
    margin-right: 0.125em;
    font-size: 1.5em;
    font-weight: bold;
    @include text-shadow;
  }
  input {
    padding: 0.125em;
  }
}
.edit-favs {
  transition: 0.3s;
  position: relative;
  // right: 0;
  top: -0.25em;
  text-align: right;
  line-height: 0;
  cursor: pointer;
  font-size: 2.5em;
  font-weight: bold;
  color: rgba(255,255,255,0.5);
    &:hover {
    color: rgba(255,255,255,1);
    transition: 0.2s;
  }
}
.fade-enter-active, .fade-leave-active {
  transition: opacity .2s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}
#weather {
  @include text-shadow;
  text-align: right;
  margin: 0 0 3em;
  h3 {
    margin: 0;
    font-size: 2.25em;
  }
  h4 {
    margin: 0;
    cursor: pointer;
    display: inline-block;
  }
}
#quote {
  margin-top: auto;
  font-size: 1.25em;
  @include text-shadow;
}
#theme {
  position: absolute;
  top: 2.5em;
  left: 2.5em;
  span {
    display: inline-block;
    margin-right: 1em;
  }
  label {
    color: #fff
  }
  .ui.toggle.checkbox .box:before, .ui.toggle.checkbox label:before {
    background: rgba(0,0,0,0.3);
  }
}


#app.dark-mode {
  color: #222;
  input[type="text"], textarea {
    background-color : #555; 
    color: #fff;
  }
  .gsc-input-box {
    background-color: #555;
    color: #fff
  }
  .gsc-input {
    background: none !important;
    color: #fff;
  }
  .ui.toggle.checkbox label,
  .ui.toggle.checkbox input:checked~label  {
    color: #222 !important;
  }
  .clock input[type="checkbox"] {
    background-color: #222
  }
  .ui.toggle.checkbox .box:after, .ui.toggle.checkbox label:after {
    background: #222;
  }
  .ui.toggle.checkbox input:checked~.box:before, .ui.toggle.checkbox input:checked~label:before {
    background-color: rgba(209,209,209,0.3) !important;
  }
}


</style>