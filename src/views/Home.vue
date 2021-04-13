<template>
  <div class="home">
    <h1>{{ message }}</h1>
    <h1>Add a Place</h1>
    Name:
    <input type="text" v-model="newPlaceName" />
    <br />
    Address:
    <input type="text" v-model="newPlaceAddress" />
    <br />
    <button v-on:click="createPlace()">Add Place</button>
    <div v-for="place in places" v-bind:key="place.id">
      <h3>{{ place.name }}</h3>
      <p>{{ place.address }}</p>
      <button v-on:click="showPlace(place)">More Info</button>
    </div>
    <dialog id="place-details">
      <form method="dialog">
        <h1>Place Info</h1>
        <p>
          Name:
          <input type="text" v-model="currentPlace.name" />
        </p>
        <p>
          Address:
          <input type="text" v-model="currentPlace.address" />
        </p>
        <button>Close</button>
        <button v-on:click="updatePlace(currentPlace)">Update</button>
        <button v-on:click="destroyPlace(currentPlace)">Destory</button>
      </form>
    </dialog>
    <ul>
      <li v-for="error in errors" v-bind:key="error">{{ error }}</li>
    </ul>
  </div>
</template>

<style></style>

<script>
import axios from "axios";
export default {
  data: function () {
    return {
      message: "Restaurants In NYC",
      places: [],
      newPlaceName: "",
      newPlaceAddress: "",
      currentPlace: {},
      errors: [],
    };
  },
  created: function () {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function () {
      axios.get("/api/places").then((response) => {
        this.places = response.data;
        console.log("all places", this.places);
      });
    },
    createPlace: function () {
      let params = {
        name: this.newPlaceName,
        address: this.newPlaceAddress,
      };
      axios
        .post("/api/places", params)
        .then((response) => {
          console.log("Success", this.places);
          this.places.push(response.data);
          this.newPlaceName = "";
          this.newPlaceAddress = "";
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
    showPlace: function (place) {
      console.log(place.response);
      this.currentPlace = place;
      document.querySelector("#place-details").showModal();
    },
    updatePlace: function (place) {
      let params = {
        name: place.name,
        address: place.address,
      };
      axios
        .patch("/api/places/" + place.id, params)
        .then((response) => {
          console.log("Success!", response.data);
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
    destroyPlace: function (place) {
      axios
        .delete("/api/places/" + place.id)
        .then((response) => {
          console.log("Success!", response.data);
          let index = this.places.indexof(place);
          this.places.splice(index, 1);
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
  },
};
</script>
