<script>
import axios from "axios";
import { store } from "../../data/store";
import PlateCard from "./PlateCard.vue";

export default {
  data() {
    return {
      plates: [],
      checkRestaurantId: false,
    };
  },

  components: { PlateCard },

  computed: {
    cartItems() {
      return this.$store.state.cart;
    },
    cartTotal() {
      return this.$store.state.cartTotal;
    },
    cartItemCount() {
      return this.$store.state.cart.length;
    },
  },

  methods: {
    detailRestaurant(plate, index) {
      return (
        index === 0 ||
        plate.restaurant.id !== this.plates[index - 1].restaurant.id
      );
    },

    fetchPlates() {
      axios
        .get(
          store.api.baseUrl +
            "plates-by-restaurant/" +
            this.$route.params.restaurantId
        )
        .then((response) => {
          console.log(response.data); // Aggiungi questa riga per vedere cosa restituisce la chiamata API
          this.plates = response.data.data;
        })
        .catch((error) => {
          console.error("Error fetching plates:", error);
        });
    },
  },

  created() {
    this.fetchPlates();
  },
};
</script>

<template>
  <div class="menu-background">
    <div class="menu-container">
      <div v-if="plates.length === 0" class="not-avaiable">
        <h2 class="not-avaiable-title">
          Il menu è in fase di aggiornamento, Al momento non è disponibile.
        </h2>
      </div>
      <div v-else>
        <div v-for="(plate, index) in plates" :key="index">
          <div v-if="detailRestaurant(plate, index)" class="restaurant-detail">
            <h4 class="restaurant-title">
              Grazie per aver scelto {{ plate.restaurant.name }}
            </h4>
            <span class="restaurant-address"
              ><font-awesome-icon :icon="['fas', 'location-dot']" /><span
                class="ms-2"
                >{{ plate.restaurant.address }}</span
              ></span
            >
            <span class="restaurant-phone"
              ><font-awesome-icon :icon="['fas', 'phone']" /><span
                class="ms-2"
                >{{ plate.restaurant.phone }}</span
              ></span
            >
          </div>
        </div>
        <div class="menu-padding">
          <h2 class="menu-title h1">Il nostro menù :</h2>
          <div class="row">
            <PlateCard v-for="plate in plates" :key="plate.id" :plate="plate" />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.menu-background {
  background-image: url("/img/menu-background.jpg");
  background-size: 100%;
  background-size: cover;
  background-position: start;
  background-attachment: fixed;
  background-repeat: no-repeat;
  min-height: 73.5vh;
  height: 100%;
}
.menu-container {
  background-color: rgba($color: #000, $alpha: 0.8);
  width: 100%;
  min-width: 400px;
  min-height: 73.5vh;
  height: 100%;
  padding-top: 1rem;
}
.menu-padding {
  padding: 0 3rem;
}

.menu-title {
  color: #d7d3d3;
  font-weight: 700;
  margin-top: 1.5rem;
}
.restaurant-detail,
.not-avaiable {
  padding-bottom: 20px;
  text-align: center;
  background-color: rgba($color: #000000, $alpha: 0.6);
  padding-top: 7rem;
}
.restaurant-title,
.not-avaiable-title {
  color: #d7d3d3;
  font-weight: 700;
  font-size: 1.5rem;
}
.restaurant-address,
.restaurant-phone,
.restaurant-description {
  color: #d7d3d3;
  font-size: 1rem;
  display: block;
}
</style>
