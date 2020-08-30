<template>
<l-map style="height: 350px" :zoom="zoom" :center="center">
<l-tile-layer :url="url"></l-tile-layer>
<l-marker :lat-lng="[47.413220, -1.219482]" > </l-marker>

<l-marker :lat-lng="[47.413220, -1.189482]">
<l-icon
          :icon-anchor="staticAnchor"
          class-name="someExtraClass">
<div class="headline">{{ customText }}</div>
<img src="../static/images/baseball-marker.png">
</l-icon>
</l-marker>
</l-map>
</template>

<script>

import { LMap, LTileLayer, LMarker, LIcon } from 'vue2-leaflet'

export default {
  components: {
    LMap,
    LTileLayer,
    LMarker,
    LIcon
  },
  data () {
    return {
      url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
      zoom: 13,
      center: [47.413220, -1.219482],
      staticAnchor: [16, 37],
      customText: 'Foobar'
    }
  }
}
</script>
