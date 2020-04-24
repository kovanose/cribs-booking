<template>
  <div class="booking">
    <div class="row">
      <div id="steps-title" class="col-md-12">
        <h1>{{ stepTitle }}</h1>
      </div>
    </div>
    <div class="row">
      <div id="steps-container" class="col-md-12">
        <ul id="steps" class="nav nav-tabs">
          <li class="nav-item" :class="currentStep >= 1 ? 'highlite-right' : ''">
            <a class="nav-link" href="#step1">
              <span :class="(currentStep >= 0)? 'highlite':''">1</span>Select Crib
            </a>
          </li>
          <li
            class="nav-item"
            :class="currentStep >= 2 ? 'highlite-full' : ( currentStep == 1 ? 'highlite-left' : '' )"
          >
            <a class="nav-link" href="#step2">
              <span :class="(currentStep >= 1)? 'highlite':''">2</span>Select Date
            </a>
          </li>
          <li
            class="nav-item"
            :class="currentStep >= 3 ? 'highlite-full' : ( currentStep == 2 ? 'highlite-left' : '' )"
          >
            <a class="nav-link" href="#step3">
              <span :class="(currentStep >= 2)? 'highlite':''">3</span>Your Details
            </a>
          </li>
          <li
            class="nav-item"
            :class="currentStep >= 3 ? 'highlite-left' : ( currentStep == 3 ? 'highlite-left' : '' )"
          >
            <a class="nav-link" href="#step4">
              <span :class="(currentStep >= 3)? 'highlite':''">4</span>Confirm
            </a>
          </li>
        </ul>
        <div class="tab-content">
          <div id="step1" class="tab-pane" :class="(currentStep == 0)? 'active': ''">
            <div class="row" id="step1-form">
              <div class="col-md-4">
                <select class="form-control" v-model="selectedCity" @change="updateCribsList()">
                  <option value="null" selected>Select City</option>
                  <option
                    v-for="(city, index) in cities"
                    :key="index"
                    :value="city.id"
                  >{{city.name}}</option>
                </select>
              </div>
              <div class="col-md-4">
                <select class="form-control">
                  <option value>No. of bedrooms</option>
                  <option value="1" :disabled="!haveCityBedrooms(1)">1 bedrooms</option>
                  <option value="2" :disabled="!haveCityBedrooms(2)">2 bedrooms</option>
                  <option value="3" :disabled="!haveCityBedrooms(3)">3 bedrooms</option>
                  <option value="4" :disabled="!haveCityBedrooms(4)">4 bedrooms</option>
                  <option value="5" :disabled="!haveCityBedrooms(5)">5 bedrooms</option>
                  <option value="6" :disabled="!haveCityBedrooms(6)">6 bedrooms</option>
                  <option value="7" :disabled="!haveCityBedrooms(7)">7 bedrooms</option>
                  <option value="8" :disabled="!haveCityBedrooms(8)">8 bedrooms</option>
                  <option value="9" :disabled="!haveCityBedrooms(9)">9 bedrooms</option>
                  <option value="10" :disabled="!haveCityBedrooms(10)">10 bedrooms</option>
                </select>
              </div>
              <div class="col-md-4">
                <select class="form-control">
                  <option value="0">Sort by: Lowest price</option>
                  <option value="1">Sort by: Highest price</option>
                </select>
              </div>
            </div>
            <div id="step1-tabs">
              <div class="row">
                <div class="col-md-6">
                  <h3 class="text-center">Houses List</h3>
                  <div id="cribs-listing">
                    <table>
                      <tbody>
                        <house-card
                          @flagBy="markerBounce"
                          @deFlagBy="markerDeBounce"
                          @houseSelected="houseSelected"
                          v-for="(house, index) in houses"
                          :id="index"
                          :key="index"
                          :imageSource="house.photo"
                          :rate="house.rate"
                          :info="house.info"
                          :bill="house.weekly_rent_adv"
                          :title="houseTitle(house)"
                        ></house-card>
                      </tbody>
                    </table>
                  </div>
                </div>
                <div class="col-md-6">
                  <h3 class="text-center">Houses on Map</h3>
                  <div id="cribs-map" style="position: relative;overflow: hidden;">
                    <GmapMap
                      :center="centerPos"
                      :zoom="15"
                      :scaleControl="false"
                      :mapTypeControl="false"
                      map-type-id="terrain"
                      style="height: calc(100vh - 170px);"
                    >
                      <GmapMarker
                        :key="index"
                        v-for="(h, index) in houses"
                        :position="{lat: parseFloat(h.lat), lng: parseFloat(h.lng)}"
                        :clickable="false"
                        :draggable="true"
                        :animation="h.animation"
                        @click="center=h.position"
                      />
                    </GmapMap>
                  </div>
                </div>
              </div>
              <div class="float-right hidden-xs">
                <br />
                <button
                  class="btn btn-wide btn-warning btn-lg"
                  :disabled="(selectedCribs.length == 0)"
                  @click="gotoNextStep"
                >Next</button>
              </div>
            </div>
          </div>
          <div id="step2" class="tab-pane" :class="(currentStep == 1)? 'active': ''">
            <div id="step2-cribs" class="row selected-cribs hidden-xs">
              <div class="col-md-12">
                <h3 class="text-center">Please select a date for each Crib</h3>
              </div>
              <div class="clearfix"></div>
              <div class="col-md-12">
                <ul>
                  <li class="selected-cribs-active">
                    <span>Cirencester, 8 Prospect Place</span>
                    <span>2019-08-27 10:00</span>
                  </li>
                </ul>
              </div>
            </div>
            <div class="clearfix"></div>
            <div id="step2-calendar">
              <div id="step2-weeks" class="col-sm-12 text-right hidden-xs">
                <button class="btn btn-sm selected">this week</button>
                <button class="btn btn-sm">next week</button>
                <button class="btn btn-sm">2 weeks after</button>
              </div>
              <div class="row">
                <div
                  class="col-md-2 calendar-day hidden-xs"
                  v-for="(day, index) in days"
                  :key="index"
                >
                  <h3 class>{{dayTitle(day)}}</h3>
                  <ul>
                    <li v-for="(time, index) in times" :key="index">
                      <a>
                        {{ time }}
                        <span>3 places left</span>
                      </a>
                    </li>
                  </ul>
                </div>
              </div>
            </div>
            <div class="row">
              <div class="col-md-12">
                <div class="pull-left">
                  <br />
                  <button
                    @click="gotoPrevStep()"
                    class="btn btn-warning btn-lg"
                  >Return to Cribs List</button>
                </div>
                <div class="pull-right">
                  <br />
                  <button @click="gotoNextStep()" class="btn btn-warning btn-wide btn-lg">Next</button>
                </div>
              </div>
            </div>
          </div>
          <div id="step3" class="tab-pane" :class="(currentStep == 2)? 'active': ''">
            <div id="step3-form" class="row">
              <div id="step3-viewings" class="col-md-6">
                <h3 class="text-center">Viewings</h3>
                <table>
                  <tbody>
                    <tr v-for="(crib, index) in selectedCribs" :key="index">
                      <td class="ng-binding">Cirencester, 34 Prospect Place</td>
                      <td>
                        <span
                          ng-if="selectedDates[$index].time"
                          class="ng-binding ng-scope"
                        >28/08/2019 10:30</span>
                      </td>
                    </tr>
                  </tbody>
                </table>
              </div>
              <div id="step3-details" class="col-md-6">
                <h3 class="text-center hidden-xs">Details</h3>
                <form class="form-inline">
                  <div class="form-group">
                    <label for="details-name">Your name</label>
                    <input
                      type="name"
                      class="form-control ng-pristine ng-untouched ng-invalid ng-invalid-required"
                      id="details-name"
                      placeholder
                      required
                    />
                  </div>
                  <div class="form-group">
                    <label for="details-email">Email</label>
                    <input
                      ng-model="details.email"
                      type="email"
                      class="form-control ng-pristine ng-untouched ng-valid-email ng-invalid ng-invalid-required"
                      id="details-email"
                      placeholder
                      required
                    />
                  </div>
                  <div class="form-group">
                    <label for="details-phone">Phone</label>
                    <input
                      ng-model="details.phone"
                      type="name"
                      class="form-control ng-pristine ng-untouched ng-valid"
                      id="details-phone"
                      placeholder="Phone number with country prefix"
                    />
                  </div>
                  <div class="form-group">
                    <label for="details-name">Group size</label>
                    <select ng-model="details.group" class="ng-pristine ng-untouched ng-valid">
                      <option value="? string: ?"></option>
                      <option value="1">1</option>
                      <option value="2">2</option>
                      <option value="3">3</option>
                      <option value="4">4</option>
                      <option value="5">5</option>
                      <option value="6">6</option>
                      <option value="7">7</option>
                      <option value="8">8</option>
                      <option value="9">9</option>
                      <option value="10">10</option>
                    </select>
                  </div>
                  <div class="form-group">
                    <label></label>
                    <vue-recaptcha sitekey="6LeIxAcTAAAAAJcZVRqyHh71UMIEGNQ_MXjiZKhI"></vue-recaptcha>
                  </div>
                </form>
              </div>
            </div>
            <div class="col-xs-12 hidden-xs">
              <div class="pull-left">
                <br />
                <button
                  @click="gotoPrevStep()"
                  class="btn btn-warning btn-lg"
                >Return to Select Dates</button>
              </div>
              <div class="pull-right">
                <br />
                <button class="btn btn-warning btn-wide btn-lg" @click="gotoNextStep">Book viewing</button>
              </div>
            </div>
          </div>
          <div id="step4" class="tab-pane" :class="(currentStep == 3)? 'active': ''">
            <div id="step4-confirmation">
              <div class="col-xs-12 text-center">
                We’ve sent you a&nbsp;confirmation email.
                <br />If you have any questions or issues
                <br />
                <p>
                  do give us a&nbsp;call on 0203 758 7000 or contact lettings@student-cribs.com
                  <br />
                  <br />If you don't receive a&nbsp;confirmation email please check your junk email
                </p>
                <br />
                <br />
                <a href="/" title class="btn btn-lg btn-warning btn-wide">HOME</a>
                <br />
                <br />
              </div>
            </div>
          </div>
          <a href="#" id="prev-step" v-show="prevStep()" @click="gotoPrevStep">
            <i class="fa fa-chevron-circle-left" aria-hidden="true"></i>
          </a>
          <a href="#" id="next-step" v-show="nextStep()" @click="gotoNextStep">
            <i class="fa fa-chevron-circle-right" aria-hidden="true"></i>
          </a>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import HouseCard from "../components/ui/card/HouseCard";
var moment = require("moment");
import VueRecaptcha from "vue-recaptcha";
import axios from "axios";

export default {
  components: {
    HouseCard,
    VueRecaptcha
  },
  name: "Booking",
  computed: {
    centerPos: function() {
      var pos = { lat: 0, lng: 0 };
      let sum1 = 0;
      let sum2 = 0;
      if (this.houses) {
        for (let i = 0; i < this.houses.length; i++) {
          sum1 += parseFloat(this.houses[i].lat);
          sum2 += parseFloat(this.houses[i].lng);
        }
        pos = {
          lat: sum1 / this.houses.length,
          lng: sum2 / this.houses.length
        };
      }
      return pos;
    },
    stepTitle: function() {
      var titles = [
        "Selected Cribs",
        "Selected Dates",
        "Your Details",
        "Confirm"
      ];
      return titles[this.currentStep];
    }
  },
  data() {
    return {
      houses: null,
      bedrooms: null,
      cities: null,
      // houses: [
      //   {
      //     city_id: "7",
      //     imageSrc:
      //       "https://s3-eu-west-1.amazonaws.com/studentcribs/cribs/m_c23_3d49beaec6f3dca868d6045a1525f06c.jpg",
      //     title: "34 Prospect Place, Cirencester, 4 Bedrooms",
      //     rate: "£105",
      //     info:
      //       "This amazing 4 bedroom property has a shorter tenancy length, at 40 weeks! Make an enquiry before it's too ...",
      //     bill: "£17",
      //     position: { lat: 52.280738, lng: -1.50051 },
      //     animation: 0
      //   },
      //   {
      //     city_id: "7",
      //     imageSrc:
      //       "https://s3-eu-west-1.amazonaws.com/studentcribs/cribs/m_c25_0b348b6b268b2fee89fc57e01c0e9944.jpg",
      //     title: "8 Prospect Place, Cirencester, 4 Bedrooms",
      //     rate: "£105",
      //     info:
      //       "This amazing 4 bedroom property has a shorter tenancy length, at 40 weeks! Make an enquiry before it's too ...",
      //     bill: "£17",
      //     position: { lat: 52.269629, lng: -1.51051 },
      //     animation: 0
      //   }
      // ],
      selectedCity: null,
      selectedCribs: [],
      currentStep: 0,
      days: [
        "2019-08-26",
        "2019-08-27",
        "2019-08-28",
        "2019-08-29",
        "2019-08-30",
        "2019-08-31"
      ],
      times: [
        "10:00",
        "10:30",
        "11:00",
        "11:30",
        "12:00",
        "12:30",
        "13:00",
        "13:30",
        "14:00",
        "14:30",
        "15:00",
        "15:30",
        "16:00",
        "16:30",
        "17:00",
        "17:30",
        "18:00",
        "18:30",
        "19:00"
      ]
    };
  },
  methods: {
    houseTitle: function(h) {
      var title = "";
      title =
        h.address + ", " + h.city_name + ", " + h.bedrooms_amount + " Bedrooms";
      return title;
    },
    updateCribsList: async function() {
      const city = this.cities.find(x => x.id === this.selectedCity);
      if (city) {
        var res1 = await axios.get(
          `https://api.student-cribs.com/api/cities/slug/${city.slug}/?auth=504a9444668c2a591ebc178c832f028ee6bd14a2e2716a7fbd7b4e94ecb9b09a240af1fe74290c63922b3c5591d565c5a8768bfecea5324e707355c5dee7cf70`
        );
        this.bedrooms = res1.data.data.bedrooms;
        var res2 = await axios.get(
          `https://api.student-cribs.com/api/properties/?city_id=${this.selectedCity}&auth=504a9444668c2a591ebc178c832f028ee6bd14a2e2716a7fbd7b4e94ecb9b09a240af1fe74290c63922b3c5591d565c5a8768bfecea5324e707355c5dee7cf70`
        );
        this.houses = res2.data.data;
      }
    },
    haveCityBedrooms: function(num) {
      if (this.bedrooms) {
        var bedroom = this.bedrooms.find(x => x.bedrooms_amount == num);
        if (bedroom) {
          return true;
        }
      }

      return false;
    },
    markerBounce: function(id) {
      this.houses[id].animation = 1;
    },
    markerDeBounce: function(id) {
      this.houses[id].animation = 0;
    },
    houseSelected: function(id, status) {
      if (status) {
        this.selectedCribs.push(id);
      } else {
        this.selectedCribs.pop(id);
      }
      if (this.selectedCribs.length == 0) {
        this.$toastr.i("You have to select at least 1 crb.");
      } else {
        this.$toastr.i("You can select up to 3 crbs.");
      }
    },
    prevStep: function() {
      if (this.currentStep >= 1 && this.currentStep < 3) {
        return true;
      }
      return false;
    },
    nextStep: function() {
      if (this.selectedCribs.length != 0 && this.currentStep < 2) {
        return true;
      }
      return false;
    },
    gotoPrevStep: function() {
      this.currentStep--;
    },
    gotoNextStep: function() {
      this.currentStep++;
    },
    dayTitle: function(day) {
      var title = moment(day).format("dddd, MMMM D");
      return title;
    }
  },
  mounted() {
    axios
      .get(
        "https://api.student-cribs.com/api/cities/?auth=504a9444668c2a591ebc178c832f028ee6bd14a2e2716a7fbd7b4e94ecb9b09a240af1fe74290c63922b3c5591d565c5a8768bfecea5324e707355c5dee7cf70"
      )
      .then(response => {
        this.cities = response.data.data;
      });
  }
};
</script>
<style>
/* Step 1 */

h1 {
  font-weight: 300;
  color: #fff;
  font-size: 32px;
  text-align: center;
  margin-top: 50px;
}
#steps {
  border: 0;
  margin: 20px auto 0 auto;
  width: 550px;
  text-align: center;
}
#steps li.highlite-right {
  background: url(../assets/img/step_right_bg.gif) no-repeat center 30px;
}
#steps li.highlite-full {
  background: url(../assets/img/step_full_bg.gif) no-repeat center 30px;
}
#steps li.highlite-left {
  background: url(../assets/img/step_left_bg.gif) no-repeat center 30px;
}
#steps li a,
#steps li a:hover,
#steps li.active a {
  background: none;
  pointer-events: none;
  cursor: none;
  border: 0;
  font-size: 21px;
  font-weight: 300;
  color: #fff;
  text-align: center;
}
#steps li span {
  background: #d8d8d8;
  display: block;
  width: 40px;
  height: 40px;
  -webkit-border-radius: 20px;
  border-radius: 20px;
  line-height: 40px;
  color: #aaa;
  margin: 0 auto 10px auto;
}
#steps li.active span,
#steps li span.highlite {
  background: #b3bd27;
  color: #fff;
}
#step1-form {
  padding: 30px 0;
}
#step1-form select {
  border: 0;
  background: #404040 url(../assets/img/select_arrow.png) no-repeat center right;
  color: #fff;
  border-radius: 0;
  font-size: 21px;
  font-weight: 300;
  line-height: 28px;
  height: 40px;
  -webkit-appearance: none;
  -moz-appearance: none;
  appearance: none;
  outline: 0;
  margin-bottom: 5px;
}
#cribs-listing {
  padding: 0;
  height: calc(100vh - 150px);
  overflow: auto;
}
#cribs-listing table {
  border-top: 1px solid #555;
  width: 100%;
}
#cribs-map {
  border: 10px solid #111;
  height: calc(100vh - 150px);
}
.btn-wide {
  padding-left: 80px;
  padding-right: 80px;
}
.btn-warning,
.btn-warning:hover {
  background: #b3bd27 !important;
  border-color: #b3bd27 !important;
}
.btn-warning.disabled,
.btn-warning[disabled],
fieldset[disabled] .btn-warning,
.btn-warning.disabled:hover,
.btn-warning[disabled]:hover,
fieldset[disabled] .btn-warning:hover,
.btn-warning.disabled:focus,
.btn-warning[disabled]:focus,
fieldset[disabled] .btn-warning:focus,
.btn-warning.disabled:active,
.btn-warning[disabled]:active,
fieldset[disabled] .btn-warning:active,
.btn-warning.disabled.active,
.btn-warning.active[disabled],
fieldset[disabled] .btn-warning.active {
  background: #dae35f !important;
  border-color: #dae35f !important;
}
button {
  background: #4c4a4a;
  background-color: #4c4a4a !important;
  font-size: 16px;
  text-transform: uppercase;
  font-weight: 700;
  border: 0;
  padding: 8px 20px;
}
button:hover,
button.selected {
  background: #b3bd27 !important;
}
#prev-step {
  left: 20px;
  -webkit-transition: all 0.3s ease;
  -moz-transition: all 0.3s ease;
  -ms-transition: all 0.3s ease;
  -o-transition: all 0.3s ease;
  transition: all 0.3s ease;
}
#prev-step:hover {
  left: 15px;
  -webkit-transition: all 0.3s ease;
  -moz-transition: all 0.3s ease;
  -ms-transition: all 0.3s ease;
  -o-transition: all 0.3s ease;
  transition: all 0.3s ease;
}
#prev-step,
#next-step {
  position: fixed;
  top: 45%;
}
#next-step {
  right: 20px;
  -webkit-transition: all 0.3s ease;
  -moz-transition: all 0.3s ease;
  -ms-transition: all 0.3s ease;
  -o-transition: all 0.3s ease;
  transition: all 0.3s ease;
}
#next-step:hover {
  right: 15px;
  -webkit-transition: all 0.3s ease;
  -moz-transition: all 0.3s ease;
  -ms-transition: all 0.3s ease;
  -o-transition: all 0.3s ease;
  transition: all 0.3s ease;
}
#prev-step i,
#next-step i {
  font-size: 52px;
  color: #fff;
}
#prev-step:hover i,
#next-step:hover i {
  color: #b3bd27;
  text-decoration: none;
}

/* Step 2 */
.selected-cribs {
  padding: 30px 0 30px 0;
}
#cribs-listing th,
h3 {
  color: #fff;
  font-size: 21px;
  font-weight: 300;
  padding: 7px 0 17px 10px;
}
.selected-cribs li {
  list-style: none;
  font-size: 16px;
  color: #fff;
  background: #111;
  display: inline-block;
  float: left;
  text-align: center;
  padding: 0 10px;
  margin: 0;
  line-height: 50px;
  height: 50px;
  margin: 0 2px 0 0;
  cursor: pointer;
  -webkit-border-radius: 3px;
  border-radius: 3px;
}
.selected-cribs li.selected-cribs-active {
  background: #b3bd27 !important;
}
.selected-cribs li span:nth-child(1) {
  display: inline-block;
  margin-right: 10px;
}
.selected-cribs li span:nth-child(2) {
  border-left: 5px solid #b3bd27;
  padding-left: 10px;
  font-weight: 700;
}
#step2-weeks {
  margin-bottom: 20px;
}
.calendar-day:nth-child(odd) {
  background: #111;
}
.calendar-day:nth-child(even) {
  background: #1b1b1b;
}
#step2-calendar h3 {
  text-align: center;
}
.calendar-day ul {
  margin: 0;
  padding: 0;
}
.calendar-day:nth-child(odd) li:nth-child(odd) {
  background: #1b1b1b;
}
.calendar-day:nth-child(even) li:nth-child(odd) {
  background: #111;
}
.calendar-day li {
  margin-bottom: 1px;
}
.calendar-day li {
  list-style: none;
}
.calendar-day a {
  display: block;
  width: 100%;
  text-align: center;
  font-size: 18px;
  color: #fff !important;
  height: 31px;
  line-height: 31px;
  outline: 0;
  text-decoration: none;
}
.calendar-day a:hover,
.calendar-day a.hovered {
  background: #379d32;
  text-decoration: none;
}
.calendar-day a span {
  font-size: 12px;
  color: #ddd;
}
#step3-form {
  padding: 50px 0 50px 0;
}
#step3-viewings td:nth-child(1) {
  padding-left: 20px;
}
#step3-viewings td:nth-child(2) {
  padding: 0 20px;
}
#step3-viewings td {
  color: #fff;
  font-size: 21px;
  font-weight: 300;
  line-height: 50px;
}
#step3-form .form-group {
  clear: both;
  margin: 10px 0 10px 0;
}
#step3-form label {
  display: inline-block;
  width: 135px;
  font-size: 21px;
  color: #fff;
  font-weight: 500;
  text-align: right;
  padding-right: 15px;
}
#step3-form input,
#step3-form select {
  display: inline-block;
  border-radius: 0;
  border: 0;
  background: #303030;
  color: #fff;
  width: 390px;
}
#step3-form select {
  width: 200px;
  height: 34px;
  background: #404040 url(../assets/img/select_arrow.png) no-repeat center right;
  color: #fff;
  border-radius: 0;
  font-size: 21px;
  font-weight: 300;
  line-height: 28px;
  -webkit-appearance: none;
  -moz-appearance: none;
  appearance: none;
  outline: 0;
}

/* Step 4 */
#step4-confirmation {
  padding: 50px 0;
  font-size: 21px;
  color: #fff;
  font-weight: 700;
}
</style>