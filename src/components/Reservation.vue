<template>
  <v-card
    max-width="100%"
    tile
    elevation="10"
    class="mx-auto"
    color="teal lighten-5"
  >
    <v-card-title>Reservation</v-card-title>
    <v-card-text>
      <v-form ref="reservedForm" v-model="valid" lazy-validation>
        <v-row>
          <v-col cols="12" md="12">
            <!-- form -->
            <v-menu
              ref="menu"
              v-model="menu"
              :close-on-content-click="false"
              :return-value.sync="date"
              transition="scale-transition"
              offset-y
              min-width="290px"
            >
              <template v-slot:activator="{ on, attrs }">
                <v-row>
                  <v-col>
                    <!-- start date -->
                    <v-text-field
                      v-model="minMax[0]"
                      label="Start Date"
                      prepend-icon="mdi-calendar"
                      :rules="[(v) => !!v || 'Start Date is cannot be Empty']"
                      readonly
                      v-bind="attrs"
                      v-on="on"
                    ></v-text-field>
                  </v-col>

                  <v-col>
                    <!-- end date -->
                    <v-text-field
                      v-model="minMax[1]"
                      label="EndDate"
                      prepend-icon="mdi-calendar"
                      :rules="[(v) => !!v || 'End Date is cannot be Empty']"
                      readonly
                      v-bind="attrs"
                    ></v-text-field>
                  </v-col>
                </v-row>
              </template>
              <!-- date picker -->
              <v-date-picker
                v-model="date"
                no-title
                scrollable
                :min="new Date().toISOString().substr(0, 10)"
                range
              >
                <v-spacer></v-spacer>
                <v-btn text color="primary" @click="menu = false">Cancel</v-btn>
                <v-btn text color="primary" @click="$refs.menu.save(date)"
                  >OK</v-btn
                >
              </v-date-picker>
            </v-menu>
          </v-col>
        </v-row>
        <!-- Homestay -->
        <v-select
          v-model="select"
          :items="reservedHomestay"
          :rules="[(v) => !!v || 'Homestay is required']"
          label="Choose HomeStay"
          required
        ></v-select>
        <!-- Price -->
        <v-text-field
          :value="'RM ' + getDay * homestayPrice"
          label="Price"
          class="cyan lighten-5 black--text"
          filled
          persistent-hint
          disabled
          :hint="'Your reserved ' + getDay + ' days.'"
        ></v-text-field>
        <v-btn color="blue lighten-4" class="mr-4" @click="test">Order</v-btn>
      </v-form>
    </v-card-text>
  </v-card>
</template>

<script>
import { mapGetters } from "vuex";
import store from "../store";
const data = {
  date: ["", ""],
  menu: false,
  valid: "",
  Homestay: "",
  select: "",
  validate: "",
};
export default {
  computed: {
    ...mapGetters(["reservedHomestay"]),
    minMax: function () {
      if (
        this.date.length === 2 &&
        Date.parse(this.date[0]) > Date.parse(this.date[1])
      ) {
        [this.date[0], this.date[1]] = [this.date[1], this.date[0]];
      }
      return this.date;
    },
    homestayPrice() {
      if (
        this.date.length === 2 &&
        Date.parse(this.date[1]) > Date.parse(this.date[0]) &&
        this.select
      ) {
        const result = this.reservedHomestay.filter(
          (homestay) => this.select == homestay.value
        );
        return result[0].price;
      } else return "";
    },
    getDay: function () {
      if (
        this.date.length === 2 &&
        Date.parse(this.date[1]) > Date.parse(this.date[0]) &&
        this.select
      ) {
        const day = Date.parse(this.date[1]) - Date.parse(this.date[0]);
        return day / 86400000;
      } else if (
        this.date.length === 2 &&
        Date.parse(this.date[0]) > Date.parse(this.date[1])
      ) {
        [this.date[0], this.date[1]] = [this.date[1], this.date[0]];
        const day = Date.parse(this.date[1]) - Date.parse(this.date[0]);
        return day / 86400000;
      } else {
        return "";
      }
    },
  },
  data: function () {
    return data;
  },
  methods: {
    test: function () {
      //add validation from form
      if (this.$refs.reservedForm.validate()) {
        const data = {
          userID: {
            id: store.state.Users.user._id,
            name:
              store.state.Users.user.fName + " " + store.state.Users.user.lName,
            contact: store.state.Users.user.phone,
          },
          startDate: this.date[0],
          endDate: this.date[1],
          homestay: this.select,
          amount: this.getDay * this.homestayPrice,
        };
        //dispatch to vuex
        store.dispatch("valiationReservation", data);
      }
    },
    order: function () {
      return null;
    },
  },
};
</script>

<style>
</style>