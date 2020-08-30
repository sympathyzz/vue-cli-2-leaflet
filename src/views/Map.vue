<template>
  <l-map style="height: 750px" :zoom="zoom" :center="center">
    <l-tile-layer :url="url"></l-tile-layer>
    <l-layer-group ref="features">
      <l-popup>
        <span>Yay I was opened by {{caller}}</span>
      </l-popup>
    </l-layer-group>
    <template v-for="circle in data.circleData">
      <l-circle
        :key="circle.key"
        :lat-lng="[circle.lat,circle.lng]"
        :radius="circle.radius"
        :color="circle.color"
        @click="openPopUp([circle.lat,circle.lng], 'circle')"
      />
    </template>
    <template v-for="port in data.portData">
      <l-marker
        :key="port.key"
        :lat-lng="[port.lat,port.lng]"
        @click="openPopUp([port.lat,port.lng], port.type)"
        ref="portMarker"
        :duration="2000"
      >
        <l-tooltip :options="{ permanent: true, interactive: true }" v-if="port.type==='tooltip'">
          <div @click="innerClick">I am a tooltip</div>
        </l-tooltip>
        <l-icon
          :icon-anchor="staticAnchor"
          class-name="someExtraClass"
          :popup-anchor="popupAnchor"
          v-if="port.type==='destination'"
        >
          <!-- <div class="headline">{{ customText }}</div> -->
          <img src="../static/images/map_marker.png" width="25px" height="41px" />
        </l-icon>
      </l-marker>
    </template>
    <template v-for="polygonData in data.polygonData">
      <l-polygon :key="polygonData.key" :lat-lngs="polygonData.latlngs" :color="polygonData.color"></l-polygon>
    </template>
    <template v-for="ais in data.aisData">
      <l-polyline :key="ais.key" :lat-lngs="ais.latlngs" color="green"></l-polyline>
    </template>
    <template v-for="arrow in data.arrowsLatLngs">
      <l-marker :key="arrow.key" :lat-lng="arrow.latlng" :duration="2000">
        <l-icon :icon-anchor="staticAnchor2" :popup-anchor="popupAnchor">
          <!-- <div class="headline">{{ customText }}</div> -->
          <img src="../static/images/action_arrow.png" width="30px" height="36px" style="transform:rotate(30deg);" />
        </l-icon>
      </l-marker>
    </template>
  </l-map>
</template>

<script>
import {
  LMap,
  LTileLayer,
  LCircle,
  LMarker,
  LIcon,
  LPolygon,
  LLayerGroup,
  LPopup,
  LTooltip,
  LPolyline
} from 'vue2-leaflet'

export default {
  components: {
    LMap,
    LTileLayer,
    LCircle,
    LMarker,
    LIcon,
    LPolygon,
    LLayerGroup,
    LPopup,
    LTooltip,
    LPolyline
  },
  data () {
    return {
      url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
      zoom: 8,
      // 北纬21°48′～22°27′、东经113°03′～114°19′
      center: [22.18433, 113.519482],
      data: null,
      customText: 'Foobar',
      staticAnchor: [12, 41],
      staticAnchor2: [15, 18],
      popupAnchor: [12, 5],
      caller: null
    }
  },
  methods: {
    openPopUp (latLng, caller) {
      if (caller !== 'tooltip') {
        this.caller = caller
        this.$refs.features.mapObject.openPopup(latLng)
      }
    },
    innerClick () {
      alert('Click!')
    },
    getArrowsLatLngs () {
      var arrowsLatLngs = []
      for (let i = 0; i < this.data.aisData.length; i++) {
        for (let j = 0; j < this.data.aisData[i].latlngs.length - 1; j++) {
          let arrowLat = (this.data.aisData[i].latlngs[j][0] + this.data.aisData[i].latlngs[j + 1][0]) / 2
          let arrowLng = (this.data.aisData[i].latlngs[j][1] + this.data.aisData[i].latlngs[j + 1][1]) / 2
          arrowsLatLngs.push({
            key: 'arrow' + i + j,
            color: this.data.aisData[i].color,
            latlng: [arrowLat, arrowLng]
          })
        }
      }
      this.data.arrowsLatLngs = arrowsLatLngs
    }
  },
  created () {
    var data = {
      aisData: [
        {
          key: 1,
          latlngs: [
            [22.18433, 113.519482],
            [21.18433, 113.519482],
            [20.18433, 113.519482],
            [21.18433, 114.519482],
            [19.18433, 115.519482]
          ],
          color: '#FFDEAD'
        },
        {
          key: 2,
          latlngs: [
            [19.18433, 115.519482],
            [20.18433, 116.519482],
            [20.18433, 117.519482],
            [20.18433, 118.519482],
            [22.18433, 114.519482]
          ],
          color: '#F4A460'
        }
      ],
      portData: [
        {
          key: 'port1',
          lat: 22.18433,
          lng: 113.519482,
          type: 'oringin'
        },
        {
          key: 'port2',
          lat: 19.18433,
          lng: 115.519482,
          type: 'tooltip'
        },
        {
          key: 'port3',
          lat: 22.18433,
          lng: 114.519482,
          type: 'destination'
        }
      ],
      circleData: [
        {
          key: 'c1',
          lat: 22.18433,
          lng: 113.519482,
          radius: 45000,
          color: '#e1414e'
        },
        {
          key: 'c2',
          lat: 22.18433,
          lng: 114.519482,
          radius: 41000,
          color: '#e1414e'
        }
      ],
      polygonData: [
        {
          key: 'p1',
          latlngs: [
            [23.98433, 116.519482],
            [21.18433, 116.519482],
            [22.18433, 110.519482]
          ],
          color: 'green'
        }
      ]
    }
    this.data = data
    this.getArrowsLatLngs()
  }
}
</script>
