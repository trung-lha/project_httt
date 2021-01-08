<template>
  <div>
    <v-row class="mt-10" style="opacity: 0.9">
      <v-col cols="12" class="mt-n5">
        <flip-countdown deadline="2021-2-12 00:00:00"></flip-countdown>
      </v-col>
      <v-col cols="12" class="mt-n4">
        <v-card
          class="mx-auto"
          max-width="344"
          style="background-color: white; border-color: rgba(97, 92, 92, 0.27);"
        >
          <v-row>
            <v-col
              cols="12"
              class="d-flex align-end justify-center"
              style="padding-top: 13px"
            >
              <v-icon size="3.75rem" color="black" id="icon-weather">{{weatherDescription}}</v-icon>
              <div class="text-h3 ml-3 font-weight-bold black--text">
                {{ temp }}&deg;C
              </div>
            </v-col>
            <v-col
              cols="12"
              align="center"
              class="pt-0 text-subtitle-1 black--text"
              >{{city2}} , {{ today }}</v-col
            >
            <v-col
              cols="12"
              align="center"
              class="pt-0 text-h4 font-weight-bold black--text"
              >{{ now }}</v-col
            >
          </v-row>
        </v-card>
      </v-col>

      <v-col cols="12" class="d-flex">
        <v-row class="justify-center">
          <v-col cols="3" class="d-flex">
            <v-text-field
              v-model="city"
              label="City"
              solo
              class="mr-1"
              v-on:keyup.enter= "getInfoWeather"
            ></v-text-field>
            <v-btn
              depressed
              color="primary"
              height="62%"
              @click= "getInfoWeather"
            >
              Search
            </v-btn>
          </v-col>
        </v-row>
      </v-col>
      <v-col cols="12" class="d-flex">
        <v-row class="justify-center ">
          <iframe
                v-bind:src = "address"
                width="450"
                height="200"
                frameborder="0"
                style="border: 0"
                allowfullscreen=""
                aria-hidden="false"
                tabindex="0"
              ></iframe
            >
        </v-row>
      </v-col>
    </v-row>
  </div>
</template>

<script>
import Vue from "vue";
import axios from "axios";
import moment from "moment";
import FlipCountdown from "vue2-flip-countdown";
import VueSimpleAlert from "vue-simple-alert";
Vue.use(VueSimpleAlert);
export default {
  name: "HelloWorld",
  components: { FlipCountdown },

  data: () => ({
    city: "hanoi",
    city2: "Hanoi",
    focus: "",
    infomationDate: {},
    type: "month",
    dialog: false,
    typeToLabel: {
      month: "Month",
    },
    weatherDescriptionId: null,
    weatherDescription: null,
    selectedEvent: {},
    selectedElement: null,
    selectedOpen: false,
    events: [],
    weatherInterval: null,
    lat: null,
    lon: null,
    today: moment().format("dddd - DD/MM/YYYY"),
    now: moment().format("hh:mm:ss A"),
    infoWeather: {},
    temp: null,
    address: "https://www.google.com/maps?q=21.0245,105.8412&z=15&output=embed"
  }),
  mounted() {
    // this.$refs.calendar.checkChange();
    this.getInfoWeather();
    this.weatherInterval = setInterval(() => {
      this.getInfoWeather();
    }, 600000);
    setInterval(() => {
      this.now = moment().format("hh:mm:ss A");
      this.today = moment().format("dddd - DD/MM/YYYY");
    }, 1000);
  },
  methods: {
    getInfoWeather() {
      axios
        .get(
          "https://api.openweathermap.org/data/2.5/weather?q=" +
            this.city +
            "&appid=3265874a2c77ae4a04bb96236a642d2f"
        )
        .then((response) => {
          console.log(response.data);
          // console.log(response.data.weather[0]['id']);
          this.infoWeather = response.data;
          this.city2 = response.data.name;
          this.lat = response.data.coord.lat;
          this.lon = response.data.coord.lon;
          this.weatherDescriptionId = response.data.weather[0]['id'];
          this.temp = Math.floor(response.data.main.temp - 273.15);
          this.address = "https://www.google.com/maps?q="+ this.lat + "," + this.lon +"&z=15&output=embed"
          this.changeIcon();
        })
        .catch((error) => {
          if (error.response) {
            /*
             * The request was made and the server responded with a
             * status code that falls out of the range of 2xx
             */this.$alert(
               error.response.data.message, 
               'Warning',
                'warning'
               );
            // console.log(error.response.data.message);
            // console.log(error.response.status);
            // console.log(error.response.headers);
          }
          
        });

        
    },
    changeIcon(){
      console.log(this.weatherDescription);
      switch(this.weatherDescriptionId){
          case 803:
            this.weatherDescription="mdi-weather-partly-cloudy";
            break;
          case 500:
            this.weatherDescription="mdi-weather-rainy";
            break;
          case 800:
            this.weatherDescription="mdi-weather-sunny";
            break;
          case 804:
            this.weatherDescription="mdi-weather-cloudy";
            break;
          default:
            this.weatherDescription="mdi-coolant-temperature";
            break;
        }
    },
    clickday(event) {
      this.dialog = true;
      console.log(event, "Infomation Date");
      this.infomationDate = { ...event };
    },

    setToday() {
      this.focus = "";
    },
    prev() {
      this.$refs.calendar.prev();
    },
    next() {
      this.$refs.calendar.next();
    },
    showEvent({ nativeEvent, event }) {
      const open = () => {
        this.selectedEvent = event;
        this.selectedElement = nativeEvent.target;
        setTimeout(() => {
          this.selectedOpen = true;
        }, 10);
      };

      if (this.selectedOpen) {
        this.selectedOpen = false;
        setTimeout(open, 10);
      } else {
        open();
      }

      nativeEvent.stopPropagation();
    },
  },
};
</script>
