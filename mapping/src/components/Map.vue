<template>
  <div id="map-container" ref="mapContainer" />
</template>

<script>
import * as mapboxgl from "mapbox-gl/esm";
import "mapbox-gl/dist/mapbox-gl.css";

export default {
  props: ["modelValue"],

  mounted() {
    const { center, zoom } = this.modelValue;

    const map = new mapboxgl.Map({
      accessToken: import.meta.env.VITE_MAPBOX_TOKEN,
      container: this.$refs.mapContainer,
      style: "mapbox://styles/mapbox/standard",
      center,
      zoom,
    });

    //Updates modelValue with current map location
    const updateLocation = () =>
      this.$emit("update:modelValue", this.getLocation());

    // add event listeners to update the location on map move and zoom
    map.on("move", updateLocation);
    map.on("zoom", updateLocation);

    this.map = map;
  },

  unmounted() {
    this.map?.remove();
    this.map = null;
  },

  watch: {
    modelValue(next) {
      const curr = this.getLocation();

      // Only flyTo if any of the values have changed
      if (
        curr.center.lng !== next.center.lng ||
        curr.center.lat !== next.center.lat ||
        curr.zoom !== next.zoom
      ) {
        this.map.flyTo({
          center: next.center,
          zoom: next.zoom,
        });
      }
    },
  },

  methods: {
    getLocation() {
      return {
        center: this.map.getCenter(),
        zoom: this.map.getZoom(),
      };
    },
  },
};
</script>

<style>
#map-container {
  width: 100%;
  height: 100vh;
}
</style>
