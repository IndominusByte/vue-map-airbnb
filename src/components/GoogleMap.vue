<template>
  <div>
    <div class="row scroll-none">
      <div class="col map-sticky">
        <span class="position-absolute text-searching badge badge-light text-center" v-if="searchLoading">
          <div class="spinner-border spinner-border-sm mr-2" role="status">
            <span class="sr-only">Loading...</span>
          </div>
          <span>Searching...</span>
        </span>
        <transition leave-active-class="animate__animated animate__fadeOut animate__faster">
        <span class="position-absolute text-searching badge badge-light text-center" 
          v-if="afterLoading && !searchLoading">
          <span>{{circle_markers.length}} of {{markers.length}} results</span>
        </span>
        </transition>
        <gmap-map
          ref="mapRef"
          :options="{
           zoomControl: true,
           mapTypeControl: false,
           scaleControl: false,
           streetViewControl: true,
           rotateControl: false,
           fullscreenControl: true,
           disableDefaultUi: false,
           zoomControlOptions: {position: 1},
           streetViewControlOptions: {position: 5}
          }"
          :center="center"
          :zoom="10"
          map-type-id="roadmap"
          style="width:100%;height: 100vh;"
          @center_changed="updateCenter"
          @zoom_changed="updateZoom"
          @drag="infoWinOpen = false"
          @idle="updateData">

          <gmap-info-window 
            :options="infoOptions" 
            :position="infoWindowPos" 
            :opened="infoWinOpen">
          </gmap-info-window>

          <gmap-marker
            v-for="(m, index) in circle_markers"
            :key="index"
            :position="m.position"
            :icon="markerOptions"
            @click="markerInfoWindow(m,index)">
          </gmap-marker>
        </gmap-map>
      </div>

      <div class="col pr-0 pl-0 main">
        <p class="pl-3 pt-3 font-weight-bold">{{circle_markers.length ? circle_markers.length : 'No' }} results</p>
        <transition-group class="row p-3" tag="div"
          enter-active-class="animate__animated animate__fadeIn animate__faster"
          leave-active-class="animate__animated animate__fadeOut animate__faster">
           <div class="col-md-6" v-for="(m,index) in circle_markers" :key="m.name" 
            @mouseover="toggleInfoWindow(m,index)"
            @mouseout="infoWinOpen = !infoWinOpen">

            <app-card :price="m.infoText" :name="m.name"></app-card>
           </div>
        </transition-group>
      </div>

    </div><!-- row -->
  </div>
</template>

<script>
import {gmapApi} from 'vue2-google-maps'
import Card from './Card.vue'

const mapMarker = require('../assets/marker.png');

export default {
  data() {
    return {
      center: { lat: -8.340539, lng: 115.091949 },
      searchLoading: false,
      afterLoading: false,
      circle_markers: [],
      current_position: {lat: null,lng: null},
      current_zoom: null,
      radius: null,
      markers: [
        {position:{lat: -8.340539,lng: 115.091948},infoText:'$200',name:'item1'},
        {position:{lat: -8.267559,lng: 114.524339},infoText:'$1800',name:'item2'},
        {position:{lat: -8.506854,lng: 115.262482},infoText:'$1300',name:'item3'},
        {position:{lat: -8.438413,lng: 115.496922},infoText:'$3900',name:'item4'},
        {position:{lat: -8.811012,lng: 115.173601},infoText:'$2200',name:'item5'},
        {position:{lat: -8.582952,lng: 115.085652},infoText:'$550',name:'item6'},
      ],
      markerOptions: {
        url: mapMarker,
        scaledSize: {width: 50, height: 50, f: 'px', b: 'px'},
      },

      infoWindowPos: null,
      infoWinOpen: false,
      currentMidx: null,
        
      infoOptions: {
        content: '',
        pixelOffset: {
          width: 0,
          height: -35
        }
      },

    }
  },

  computed: {
    google: gmapApi
  },

  methods:{
    updateZoom(e){
      this.current_zoom = e
    },
    updateCenter(e) {
      this.current_position.lat = e.lat()
      this.current_position.lng = e.lng()
    },
    updateData(){
      this.searchLoading = true
      setTimeout(() => {
        this.searchLoading = false

        if (this.current_zoom){
          if (this.current_zoom <= 7){
            this.circle_markers = []
            return false
          }
          if (this.current_zoom == 8) this.radius = 60 * 1000 // 60 km
          if (this.current_zoom == 9) this.radius = 50 * 1000 // 50 km
          if (this.current_zoom == 10) this.radius = 30 * 1000 // 30 km
          if (this.current_zoom == 11) this.radius = 20 * 1000 // 20 km
          if (this.current_zoom >= 12) this.radius = 10 * 1000 // 10 km
          if (this.current_zoom >= 13) this.radius = (30 * 1000) / this.current_zoom
        }

        var searchArea = new this.google.maps.Circle({
          strokeColor: '#FF0000',
          strokeOpacity: 0.8,
          strokeWeight: 2,
          center : new this.google.maps.LatLng(this.current_position.lat,this.current_position.lng),
          radius : this.radius
        });

        this.circle_markers = []

        this.markers.map((x) => {
          var lol = new this.google.maps.LatLng(x.position.lat,x.position.lng)
          if (this.google.maps.geometry.spherical.computeDistanceBetween(lol, searchArea.getCenter()) 
            <= searchArea.getRadius()) {
              this.circle_markers.push(x)
            }
        })

        this.afterLoading = true
      }, 1000)
    },
    toggleInfoWindow (marker, idx) {
      var content = "<h6 class='font-weight-bold' style='padding:14px;padding-bottom:6px;'>" + marker.infoText + "</h6>"
      this.infoWindowPos = marker.position;
      this.infoOptions.content = content;

      this.infoWinOpen = true;
      this.currentMidx = idx;
    },

    markerInfoWindow (marker,idx){
      var content = "<img src='https://firebasestorage.googleapis.com/v0/b/zooka-tampilan.appspot.com/o/sale.jpg?alt=media&token=8f453e22-bbe4-44e3-8f7f-fcfab9b87810' class='' width='200'>" + "<div class='info'>" + "<div class='title'>" + marker.name + "</div>" +"<div class='price'>"+ marker.infoText + "</div>" + "</div>"
      this.infoWindowPos = marker.position;
      this.infoOptions.content = content;

      //check if its the same marker that was selected if yes toggle
      if (this.currentMidx == idx) {
        this.infoWinOpen = !this.infoWinOpen;
      }
      //if different marker set infowindow to open and reset current marker index
      else {
        this.infoWinOpen = true;
        this.currentMidx = idx;
      }
    }
  },
  mounted(){
    // set to global
    this.current_position.lat = this.center.lat
    this.current_position.lng = this.center.lng
    this.radius = 30 * 1000 // 30 km
  },
  components:{
    appCard:Card
  }
}
</script>

<style>
@import '../assets/fontawesome/css/all.css';

.gm-ui-hover-effect { display: none !important; }
.gm-style-iw-d {overflow:hidden !important;}
.gm-style .gm-style-iw-t::after{top:38px;}
.gm-control-active.gm-fullscreen-control{border-radius: 8px !important;}

.gm-style .gm-style-iw-c{
  padding: 0px;
  top:40px;
}
.info {
    padding: 10px;
    text-align: left;
    overflow-wrap: break-word;
}
.info .title {
    font-size: 16px;
    font-weight: 500;
    padding-bottom: 5px;
}
.info .price {
  font-size: 13px;
}
.text-searching{
  z-index: 10;
  margin: 0 auto;
  top: 2rem;
  font-size: 1rem;
  left: 50%;
  transform: translate(-50%, -50%);
  padding: 15px 20px;
  align-items: center !important;
  justify-content: center !important;
  background: rgb(255, 255, 255) !important;
  border-radius: 8px !important;
}
.gmnoprint > div {
  border-radius: 8px !important;
}
.map-sticky {
  position: sticky;
  top: 0px;
}
.main {
  overflow-y: scroll;
  overflow-x: hidden;
  height: 100vh;
}
.scroll-none {
  width: 100vw;
}
.main::-webkit-scrollbar {
    display: none;
}
</style>
