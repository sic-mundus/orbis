<template>
  <div class="wrapper">
    <v-container fluid class="fill-height">
      <v-row>
        <v-col>
          <v-btn @click="changeCenter">Change Center</v-btn>
        </v-col>
        <v-col>
          <v-btn @click="changeZoom">Change Zoom</v-btn>
        </v-col>
        <v-col>
          <v-btn @click="fitBounds">Fit to bounds</v-btn>
        </v-col>
        <v-col cols="12">
          <gmap-autocomplete
            :value="description"
            placeholder="This is a placeholder text"
            @place_changed="setPlace"
            :select-first-on-enter="true"
          ></gmap-autocomplete>
        </v-col>
      </v-row>

      <v-row style="height:100%">
        <v-col cols="12">
          <GmapMap
            ref="mapRef"
            :options="mapOptions"
            :center="center"
            :zoom="zoom"
            style="width: 100%; height: 100%"
          >
            <gmap-cluster>
              <GmapMarker
                :key="index"
                v-for="(m, index) in markers"
                :position="m.position"
                :clickable="true"
                :draggable="true"
                @click="center=m.position"
              />
            </gmap-cluster>
          </GmapMap>
        </v-col>
      </v-row>
    </v-container>
  </div>
</template>

<script>
import { gmapApi } from "vue2-google-maps";
export default {
  m: null,
  data: () => ({
    mapOptions: {
      zoomControl: true,
      mapTypeControl: true,
      scaleControl: false,
      streetViewControl: false,
      rotateControl: false,
      fullscreenControl: true,
      disableDefaultUI: false
    },
    markers: [
      {
        name: "Pirulino 1",
        position: {
          lat: 42.1,
          lng: 11
        }
      },
      {
        name: "Pirulino 2",
        position: {
          lat: 43.2,
          lng: 10
        }
      },
      {
        name: "Pirulino 3",
        position: {
          lat: 41.2,
          lng: 1.2
        }
      }
    ],
    center: {
      lat: 42,
      lng: 10
    },
    zoom: 7,
    description: ""
  }),
  computed: {
    google: gmapApi
  },
  mounted() {
    // At this point, the child GmapMap has been mounted, but
    // its map has not been initialized.
    // Therefore we need to write mapRef.$mapPromise.then(() => ...)

    this.$refs.mapRef.$mapPromise.then(map => {
      this.m = map;
    });
  },
  methods: {
    fitBounds() {
      var b = new this.google.maps.LatLngBounds();

      this.markers.forEach(mk => {
        b.extend({
          lat: mk.position.lat,
          lng: mk.position.lng
        });
      });

      this.m.fitBounds(b);
    },

    changeCenter() {
      this.center = {
        lat: 44 + Math.random() * 0.3,
        lng: 10 + Math.random() * 0.1
      };
    },

    changeZoom() {
      this.zoom = Math.floor(5 + Math.random() * 10);
    },

    setPlace(place) {
      if (!place) return;

      let p = {
        lat: place.geometry.location.lat(),
        lng: place.geometry.location.lng()
      };

      let mk = {
        name: "Pirulino trovato!",
        position: {
          lat: p.lat,
          lng: p.lng
        }
      };

      this.markers.push(mk);

      this.center = mk.position;

      let zService = new this.google.maps.MaxZoomService();
      zService.getMaxZoomAtLatLng(mk.position, z => {
        console.debug("The maximum zoom available here is", z);
        this.zoom = z.zoom;
      });
    }
  }
};
</script>

<style scoped>
.wrapper {
  width: 100%;
  height: 100%;
}
</style>