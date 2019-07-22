<template>
  <div class="vue-leaflet">
    <div class="map-info">
      <h1>台北市降雨淹水模擬圖</h1>
      <p>資料來源: <a target="_blank"
          href="http://wise.wra.gov.tw/dataset/inundationprobabilitymaps">經濟部水利署/水利防災中心:
          淹水潛勢圖</a>
      </p>
      <p>
        選擇雨量：
        <select id="rain" v-model="currRange" @change="renderMap">
          <option v-for="(range, idx) in rainRange" :value="idx" :key="range">{{ range }}</option>
        </select>
      </p>
    </div>

    <div class="map">
      <l-map :zoom="zoom" :center="center">
        <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
        <l-geo-json :geojson="geoJson" :options="geoJsonOptions"></l-geo-json>
      </l-map>
    </div>
  </div>
</template>

<script>
import { LMap, LTileLayer, LGeoJson, LPopup } from 'vue2-leaflet'

export default {
  name: 'VueLeaflet',
  components: {
    LMap,
    LTileLayer,
    LGeoJson,
    LPopup
  },
  data () {
    return {
      rainRange: {
        '06h_r150': '6小時延時定量降水150毫米',
        '06h_r250': '6小時延時定量降水250毫米',
        '06h_r350': '6小時延時定量降水350毫米',
        '12h_r200': '12小時延時定量降水200毫米',
        '12h_r300': '12小時延時定量降水300毫米',
        '12h_r400': '12小時延時定量降水400毫米',
        '24h_r200': '24小時延時定量降水200毫米',
        '24h_r350': '24小時延時定量降水350毫米',
        '24h_r500': '24小時延時定量降水500毫米',
        '24h_r650': '24小時延時定量降水650毫米'
      },
      currRange: '06h_r150',
      geoJson: [],
      zoom: 14,
      center: L.latLng(25.0677505, 121.5470599),
      token: '',
      url: 'https://api.tiles.mapbox.com/v4/mapbox.streets/{z}/{x}/{y}.png?access_token=pk.eyJ1IjoibWFwYm94IiwiYSI6ImNpejY4NXVycTA2emYycXBndHRqcmZ3N3gifQ.rJcFIG214AriISLbB6B5aw',
      attribution: 'Map data &copy; <a href="https://www.openstreetmap.org/">OpenStreetMap</a> contributors, <a href="https://creativecommons.org/licenses/by-sa/2.0/">CC-BY-SA</a>, Imagery © <a href="https://www.mapbox.com/">Mapbox</a>',
      geoJsonOptions: {
        style: function (feature) {
          const colorRange = ['', '#ffd27d', '#fcb906', '#ff620b', '#fc0000', '#850001'];
          return {
            color: colorRange[+feature.properties.GRIDCODE],
            weight: 4
          };
        },
        onEachFeature: function onEachFeature(feature, layer) {
          layer.bindPopup(feature.properties.NOTE);
        }
      }
    }
  },
  methods: {
    renderMap(){
      fetch('data/tp_' + this.currRange + '.json')
        .then( res => res.json() )
        .then( jsonData => { 
          this.geoJson = jsonData;
        });
    }
  },
  mounted () {
    this.renderMap();
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.vue-leaflet {
  display: block;
  position: absolute;
  width: 100%;
  height: 100%;
}

.map-info {
  display: block;
  padding: .75em 1.75em;
  background-color: #fff;
  border: 1px solid #000;
  position: absolute;
  z-index: 10;
  top: 0;
  right: 0;
}
.map{
  display: block;
  position: absolute;
  width: 100%;
  height: 100%;
  z-index: 1;
}
</style>
