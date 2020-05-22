<template>
  <GmapMap
    ref="mapRef"
    :options="mapOptions"
    :center="center"
    :zoom="7"
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
</template>

<script>
import { gmapApi } from "vue2-google-maps";
export default {
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
          lng: 18
        }
      },
      {
        name: "Pirulino 2",
        position: {
          lat: 60.2,
          lng: 10
        }
      }
    ],
    center: {
      lat: 42,
      lng: 10
    }
  }),
  computed: {
    google: gmapApi
  },
  mounted() {
    // At this point, the child GmapMap has been mounted, but
    // its map has not been initialized.
    // Therefore we need to write mapRef.$mapPromise.then(() => ...)

    this.$refs.mapRef.$mapPromise.then(map => {
      this.panToBounds(map);
    });
  },
  methods: {
    panToBounds(map) {
      //console.log("goole:", this.google.maps.);
      var b = new this.google.maps.LatLngBounds();

      this.markers.forEach(mk => {
        b.extend({
          lat: mk.position.lat,
          lng: mk.position.lng
        });
      });

      map.panToBounds(b);
    }
  }
};
</script>