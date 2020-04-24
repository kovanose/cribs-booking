<template>
  <div class="house-card">
    <tr :class="row_checked? 'selected' : '' ">
      <td>
        <input
          type="checkbox"
          @click="rowClicked(id)"
          v-model="row_checked"
          @mouseenter="flagBy(id)"
          @mouseleave="deFlagBy(id)"
        />
      </td>
      <td></td>
      <td @mouseenter="flagBy(id)" @mouseleave="deFlagBy(id)">
        <a title="Cirencester - 34 Prospect Place">
          <img width="150" :src="imageSource" alt />
        </a>
      </td>
      <td class="hidden-xs">
        <div class="house-card-rate">
          {{ rate }}
          <br />pppw
          <br />
          <span class>+{{ bill }} bills</span>
        </div>
        <a
          href="#"
          @click.prevent="rowClicked(id)"
          @mouseenter="flagBy(id)"
          @mouseleave="deFlagBy(id)"
          class="house-card-title"
          title="More info: Cirencester - 34 Prospect Place"
        >{{ title }}</a>
        <div class="clearfix"></div>
        <div class="house-card-info">{{ info }}</div>
      </td>
      <td></td>
    </tr>
  </div>
</template>
<script>
export default {
  name: "HouseCard",
  props: ["imageSource", "rate", "title", "info", "bill", "id"],
  data() {
    return {
      selectedCribs: [],
      row_checked: false
    };
  },
  methods: {
    rowClicked: function(id) {
      this.row_checked = !this.row_checked;
      this.$emit("houseSelected", id, this.row_checked);
    },
    flagBy: function(id) {
      this.$emit("flagBy", id);
    },
    deFlagBy: function(id) {
      this.$emit("deFlagBy", id);
    }
  }
};
</script>
<style>
tr {
  border-bottom: 1px solid #555;
}
.house-card td:nth-child(1) {
  vertical-align: middle;
}
.house-card td {
  font-size: 18px;
  font-weight: 300;
  color: #fff;
  padding: 7px 0 7px 10px;
  vertical-align: top;
}
.house-card tr.selected {
  background: #b3bd27;
}
.house-card-rate {
  color: #b3bd27;
  float: right;
  padding-top: 10px;
  text-align: center;
  line-height: 18px;
}
.house-card tr.selected .house-card-rate {
  color: #fff;
}
.house-card-rate span {
  color: #fff;
  font-size: 11px;
}
.house-card-title {
  text-align: left !important;
  color: #fff;
  padding-top: 5px;
  display: inline-block;
  max-width: 200px;
}
.house-card-title:hover,
.house-card-title:visited,
.house-card-title:link,
.house-card-title:active {
  text-decoration: none;
  color: #fff;
}
.house-card-info {
  font-size: 12px;
  padding-top: 10px;
}
@media screen and (max-width: 991px) {
  .cribs-listing-title {
    float: left;
    width: 70%;
  }
}
</style>