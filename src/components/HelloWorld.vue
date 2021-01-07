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
          style="background-color: rgba(18 115 214 / 27%); border-color: rgba(97, 92, 92, 0.27);"
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
            ></v-text-field>
            <v-btn
              depressed
              color="primary"
              height="62%"
              @click="getInfoWeather"
            >
              Search
            </v-btn>
          </v-col>
        </v-row>
      </v-col>

      <!-- <v-col class="mr-10" cols="9">
        <v-sheet height="64">
          <v-toolbar flat>
            <v-btn
              outlined
              class="mr-4"
              color="grey darken-2"
              @click="setToday"
            >
              Today
            </v-btn>
            <v-btn fab text small color="grey darken-2" @click="prev">
              <v-icon small>
                mdi-chevron-left
              </v-icon>
            </v-btn>
            <v-btn fab text small color="grey darken-2" @click="next">
              <v-icon small>
                mdi-chevron-right
              </v-icon>
            </v-btn>
            <v-toolbar-title v-if="$refs.calendar">
              {{ $refs.calendar.title }}
            </v-toolbar-title>
            <v-spacer></v-spacer>
            <v-menu bottom right>
              <template v-slot:activator="{ on, attrs }">
                <v-btn outlined color="grey darken-2" v-bind="attrs" v-on="on">
                  <span>{{ typeToLabel[type] }}</span>
                  <v-icon right>
                    mdi-menu-down
                  </v-icon>
                </v-btn>
              </template>
              <v-list>
                <v-list-item @click="type = 'month'">
                  <v-list-item-title>Month</v-list-item-title>
                </v-list-item>
              </v-list>
            </v-menu>
          </v-toolbar>
        </v-sheet>
        <v-sheet height="600">
          <v-calendar
            ref="calendar"
            v-model="focus"
            color="primary"
            :type="type"
            @click:date="clickday($event)"
          ></v-calendar>
        </v-sheet>
      </v-col> -->
    </v-row>

    <!-- <v-dialog v-model="dialog" width="500">
      <v-card>
        <v-card-title class="headline grey lighten-2 d-flex justify-center">
          Information
        </v-card-title>

        <v-card-text>
          {{ infomationDate }}
        </v-card-text>

        <v-divider></v-divider>

        <v-card-actions class="d-flex justify-center">
          <v-btn color="primary" text @click="dialog = false">
            Close
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog> -->
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
    today: moment().format("dddd - DD/MM/YYYY"),
    now: moment().format("hh:mm:ss A"),
    infoWeather: {},
    temp: null,
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
          this.weatherDescriptionId = response.data.weather[0]['id'];
          this.temp = Math.floor(response.data.main.temp - 273.15);
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
