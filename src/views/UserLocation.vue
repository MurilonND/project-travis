<template>
  <div>
    <section class="" style="position: relative; z-index: 1">
      <div v-show="errorMensage" style="background-color: red; color: white">
        {{ errorMensage }}
      </div>

      <div id="form" class="form-style">
        <div style="padding: 15px 0px 0px 10px">
          <input
            id="start_input"
            type="text"
            placeholder="Digite o local de partida"
            v-model="start_point"
          />
          <button @click="UserLocation()">Minha localização</button>
        </div>
        <div style="padding: 15px 0px 0px 10px">
          <input
            id="end_input"
            type="text"
            placeholder="Digite seu destino"
            v-model="end_point"
          />
          <div style="padding: 15px 0px 15px 10px">
            <button id="search" style="margin: 0px 0px 0px 50%">
              Procurar
            </button>
            <button id="reset" style="margin: 0px 0px 0px 15px">Resetar</button>
          </div>
        </div>
        <!-- <div v-show="results" style="color: white">{{ results }}</div> -->
      </div>

      <div class="menu-buttons-padding">
        <button
          id="button-open"
          class="Hide menu-buttons-style"
          style="background-color: #34a853"
          @click="OpenButton()"
        ></button>
        <button
          id="button-close"
          class="menu-buttons-style"
          style="background-color: #ee675c"
          @click="CloseButton()"
        ></button>
      </div>
    </section>

    <section id="map" ref="map"></section>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      start_point: "",
      end_point: "",
      errorMensage: "",
      // results: "",
    };
  },

  mounted() {
    const google = window.google;
    new google.maps.places.Autocomplete(
      document.getElementById("start_input"),
      {
        bounds: new google.maps.LatLngBounds(
          new google.maps.LatLng(-23.50167, -47.45806)
        ),
      }
    );
    new google.maps.places.Autocomplete(document.getElementById("end_input"), {
      bounds: new google.maps.LatLngBounds(
        new google.maps.LatLng(-23.50167, -47.45806)
      ),
    });

    const map = new google.maps.Map(document.getElementById("map"), {
      zoom: 15,
      center: { lat: -23.50167, lng: -47.45806 },
      mapTypeId: google.maps.MapTypeId.ROADMAP,
      disableDefaultUI: true,
    });
    const directionsService = new google.maps.DirectionsService();
    const directionsRenderer = new google.maps.DirectionsRenderer();
    directionsRenderer.setMap(map);

    document.getElementById("search").onclick = () => {
      var request = {
        origin:
          "FACENS, Rodovia Senador José Ermírio de Moraes, 1425 - Jardim Constantino Matucci, Sorocaba - SP, 18085-784",
        destination:
          "Terminal Santo Antônio, Av. Dr. Afonso Vergueiro, 733 - Centro, Sorocaba - SP, 18040-000",
        travelMode: google.maps.TravelMode.DRIVING,
        unitSystem: google.maps.UnitSystem.IMPERIAL,
      };

      directionsService.route(request, function (response, status) {
        if (status === "OK") {
          directionsRenderer.setDirections(response);
        } else {
          directionsRenderer.setDirections({ routes: [] });
          map.setCenter({ lat: -23.50167, lng: -47.45806 });

          this.errorMensage = "Could not retrieve driving distance.";
        }
      });
    };
    document.getElementById("reset").onclick = () => {
      this.start_point = "";
      this.end_point = "";

      directionsRenderer.setDirections({ routes: [] });
      map.setCenter({ lat: -23.50167, lng: -47.45806 });
    };
  },

  methods: {
    OpenButton() {
      document.getElementById("button-open").classList.add("Hide");
      document.getElementById("button-close").classList.remove("Hide");
      document.getElementById("form").classList.remove("Hide");
    },
    CloseButton() {
      document.getElementById("button-close").classList.add("Hide");
      document.getElementById("button-open").classList.remove("Hide");
      document.getElementById("form").classList.add("Hide");
    },
    UserLocation() {
      this.errorMensage = "";
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(
          (position) => {
            this.getAdressFrom(
              position.coords.latitude,
              position.coords.longitude
            );
          },
          (error) => {
            this.errorMensage = error.message;
          }
        );
      } else {
        this.errorMensage = "Seu navegador não é compativel com essa função";
      }
    },
    getAdressFrom(lat, lng) {
      axios
        .get(
          "https://maps.googleapis.com/maps/api/geocode/json?latlng=" +
            lat +
            "," +
            lng +
            "&key=AIzaSyDjz4S100FJGxG8uVVR_30WESHUfsyzVdg"
        )
        .then((response) => {
          if (response.data.error_message) {
            this.errorMensage = response.data.error_message;
          } else {
            this.start_point = response.data.results[0].formatted_address;
          }
        })
        .catch((error) => {
          this.errorMensage = error.message;
        });
    },
  },
};
</script>

<style>
#map {
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  background: red;
}
.Hide {
  display: none;
}

.pac-icon {
  display: none;
}

.form-style {
  padding: 5px 0px 0px 5px;
  height: auto;
  border-bottom-left-radius: 10px;
  border-bottom-right-radius: 10px;

  background-color: rgb(44, 44, 44);
}
.menu-buttons-padding {
  padding: 5px;
}
.menu-buttons-style {
  border-radius: 50%;
  border-style: solid;
  border-color: rgb(44, 44, 44);
  border-width: 3px;

  color: rgb(44, 44, 44);

  height: 50px;
  width: 50px;
}
</style>
