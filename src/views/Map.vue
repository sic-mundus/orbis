<template>
  <div class="wrapper">
    <v-btn @click="changeCenter">Change Center</v-btn>
    <v-btn @click="changeZoom">Change Zoom</v-btn>
    <v-btn @click="fitBounds">Fit to bounds</v-btn>
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
  </div>
</template>

<script>
import { gmapApi } from "vue2-google-maps";
export default {
  m: null,
  data: () => ({
    mapOptions: {
      zoomControl: true,
      mapTypeControl: false,
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
    zoom: 7
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