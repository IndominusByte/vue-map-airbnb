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
          @tilesloaded="infoWinOpen = false"
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
            :icon="!m.clicked ? markerOptions : markerClicked"
            @click="markerInfoWindow(m,index)">
          </gmap-marker>
        </gmap-map>
      </div>
      <div class="col pr-0 pl-0 main">
        <div class="container mt-4">
          <div class="hov_none shadow-none m-t-35 card">
            <div class=" card-body property-distance">
              <h3 class=" card-title mb-4">Distance to:</h3>
              <div class="row">
                <div class="mb-2 col-lg-4 col-md-6">
                  <h4 class=" fs-14"><i class=" fal fa-credit-card mr-2 fs-16"></i>ATM:<span class=" font-weight-normal ml-1 text-secondary">{{distance_from.atm}} Km</span></h4>
                </div>
                <div class="mb-2 col-lg-4 col-md-6">
                  <h4 class=" fs-14"><i class=" fal fa-utensils mr-2 fs-16"></i>Retaurant:<span class=" font-weight-normal ml-1 text-secondary">{{distance_from.restaurant}} Km</span></h4>
                </div>
                <div class="mb-2 col-lg-4 col-md-6">
                  <h4 class=" fs-14"><i class=" fal fa-mug-hot mr-2 fs-16"></i>Cafe:<span class=" font-weight-normal ml-1 text-secondary">{{distance_from.cafe}} Km</span></h4>
                </div>
                <div class="mb-2 col-lg-4 col-md-6">
                  <h4 class=" fs-14"><i class=" fal fa-capsules mr-2 fs-16"></i>Pharmacy:<span class=" font-weight-normal ml-1 text-secondary">{{distance_from.pharmacy}} Km</span></h4>
                </div>
                <div class="mb-2 col-lg-4 col-md-6">
                  <h4 class=" fs-14"><i class=" fal fa-store mr-2 fs-16"></i>Corner Store:<span class=" font-weight-normal ml-1 text-secondary">{{distance_from.convenience_store}} Km</span></h4>
                </div>
              </div>
            </div>
          </div>

          <form class="mt-4">
            <div class="form-group">
              <label for="inputAddress" class="map-search-title">Location</label>
              <input type="text" class="form-control" id="inputAddress" placeholder="Enter Address, City or State">
            </div>
            <div class="form-row">
              <div class="form-group col-md-4">
                <div class="dropdown">
                  <button class="btn border btn-block dropdown-toggle" type="button" id="dropdownMenuButton" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
                    Type
                  </button>
                  <div class="dropdown-menu" aria-labelledby="dropdownMenuButton">
                    <a class="dropdown-item" href="#">Modern Style</a>
                    <a class="dropdown-item" href="#">Balinese</a>
                    <a class="dropdown-item" href="#">Luxury</a>
                  </div>
                </div>
              </div>
              <div class="form-group col-md-4">
                <div class="dropdown">
                  <button class="btn border btn-block dropdown-toggle" type="button" id="dropdownMenuButton" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
                    Status
                  </button>
                  <div class="dropdown-menu" aria-labelledby="dropdownMenuButton">
                    <a class="dropdown-item" href="#">Free Hold</a>
                    <a class="dropdown-item" href="#">Lease Hold</a>
                  </div>
                </div>
              </div>
              <div class="form-group col-md-4">
                <div class="dropdown">
                  <button class="btn border btn-block dropdown-toggle" type="button" id="dropdownMenuButton" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
                    Price
                  </button>
                  <div class="dropdown-menu" aria-labelledby="dropdownMenuButton">
                    <a class="dropdown-item" href="#">Action</a>
                    <a class="dropdown-item" href="#">Another action</a>
                    <a class="dropdown-item" href="#">Something else here</a>
                  </div>
                </div>
              </div>
            </div>
            <button type="submit" class="btn btn-red-hot btn-block">Search</button>
          </form>
        </div>
        <p class="pl-3 pt-3 font-weight-bold">{{circle_markers.length ? circle_markers.length : 'No' }} results</p>
        <transition-group class="row p-3" tag="div"
          enter-active-class="animate__animated animate__fadeIn animate__faster"
          leave-active-class="animate__animated animate__fadeOut animate__faster">
           <div class="col-12" v-for="(m,index) in circle_markers" :key="m.name" 
            @mouseover="toggleInfoWindow(m,index)"
            @mouseout="infoWinOpen = !infoWinOpen">

            <app-card :price="m.price" :name="m.name" :image="m.image" :location="m.location"></app-card>
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
const mapMarkerClicked = require('../assets/marker-clicked.png');

export default {
  data() {
    return {
      center: { lat: -8.381357822670871, lng: 115.13967209436002 },
      searchLoading: false,
      afterLoading: false,
      circle_markers: [],
      current_position: {lat: null,lng: null},
      current_zoom: null,
      radius: null,
      current_distance: {
        atm: {lat: null,lng: null},
        restaurant: {lat: null,lng: null},
        cafe: {lat: null,lng: null},
        pharmacy: {lat: null,lng: null},
        convenience_store: {lat: null,lng: null}
      },
      distance_from:{
        atm: null,
        restaurant: null,
        cafe: null,
        pharmacy: null,
        convenience_store: null
      },
      markers: [
        {position: {lat: -8.340539,lng: 115.091948},
          price:'$200',
          name:'Bali Property for Sale – Chill House Hipster Retreat',
          image:'property1.jpg',
          location:'Canggu, Pererenan',
          clicked: false
        },
        {position:{lat: -8.267559,lng: 114.524339},
          price:'$1800',
          name:'Flawless Uluwatu Villa Zsa Zsa Finally for Sale',
          image:'property2.jpg',
          location:'Canggu, Tabanan, Tanah Lot',
          clicked: false
        },
        {position:{lat: -8.506854,lng: 115.262482},
          price:'$1300',
          name:'High Ranking Boutique Resort for Sale in Sanur',
          image:'property3.jpg',
          location:'Bukit, Ungasan',
          clicked: false
        },
        {position:{lat: -8.438413,lng: 115.496922},
          price:'$3900',
          name:'Modern Bali Villa for Rent in Seminyak',
          image:'property4.jpg',
          location:'Ubud, Tegallalang',
          clicked: false
        },
        {position:{lat: -8.811012,lng: 115.173601},
          price:'$2200',
          name:'Chic Serenity in Beach Lovers Paradise – Sanur.',
          image:'property5.jpg',
          location:'Canggu, Berawa',
          clicked: false
        },
        {position:{lat: -8.582952,lng: 115.085652},
          price:'$550',
          name:'Breathtaking Exotic Sanur Residence',
          image:'property6.jpg',
          location:'Canggu, Mengwi, Tumbak',
          clicked: false
        },
      ],
      markerOptions: {
        url: mapMarker,
        scaledSize: {width: 45, height: 45, f: 'px', b: 'px'},
      },
      markerClicked: {
        url: mapMarkerClicked,
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
    rad(x){
      return x * Math.PI / 180
    },
    getDistance(p1,p2){
      /* Haversine formula */
      let R = 6378137 // Earth’s mean radius in meter
      let dLat = this.rad(p2.lat() - p1.lat())
      let dLong = this.rad(p2.lng() - p1.lng())
      let a = Math.sin(dLat / 2) * Math.sin(dLat / 2) + Math.cos(this.rad(p1.lat())) * Math.cos(this.rad(p2.lat())) *
        Math.sin(dLong / 2) * Math.sin(dLong / 2)
      let c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a))
      let d = R * c
      return d // returns the distance in meter
    },
    callbackRestaurant(results,status){
      if (status == this.google.maps.places.PlacesServiceStatus.OK) {
        let last = results[0]
        this.current_distance.restaurant.lat = last.geometry.location.lat()
        this.current_distance.restaurant.lng = last.geometry.location.lng()
      }
    },
    callbackAtm(results,status){
      if (status == this.google.maps.places.PlacesServiceStatus.OK) {
        let last = results[0]
        this.current_distance.atm.lat = last.geometry.location.lat()
        this.current_distance.atm.lng = last.geometry.location.lng()
      }
    },
    callbackCafe(results,status){
      if (status == this.google.maps.places.PlacesServiceStatus.OK) {
        let last = results[0]
        this.current_distance.cafe.lat = last.geometry.location.lat()
        this.current_distance.cafe.lng = last.geometry.location.lng()
      }
    },
    callbackPharmacy(results,status){
      if (status == this.google.maps.places.PlacesServiceStatus.OK) {
        let last = results[0]
        this.current_distance.pharmacy.lat = last.geometry.location.lat()
        this.current_distance.pharmacy.lng = last.geometry.location.lng()
      }
    },
    callbackConvenienceStore(results,status){
      if (status == this.google.maps.places.PlacesServiceStatus.OK) {
        let last = results[0]
        this.current_distance.convenience_store.lat = last.geometry.location.lat()
        this.current_distance.convenience_store.lng = last.geometry.location.lng()
      }
    },
    getDistanceTo(){
      let current_cursor = new this.google.maps.LatLng(this.current_position.lat,this.current_position.lng)
      // object map
      let map = new this.google.maps.places.PlacesService(this.$refs['mapRef'].$mapObject)
      // search nearby restaurant from current cursor
      map.nearbySearch({
        location: current_cursor, //Add initial lat/lon here
        rankBy: this.google.maps.places.RankBy.DISTANCE,
        type: ['restaurant'],
      }, this.callbackRestaurant);
      // search nearby atm from current cursor
      map.nearbySearch({
        location: current_cursor, //Add initial lat/lon here
        rankBy: this.google.maps.places.RankBy.DISTANCE,
        type: ['atm'],
      }, this.callbackAtm);
      // search nearby cafe from current cursor
      map.nearbySearch({
        location: current_cursor, //Add initial lat/lon here
        rankBy: this.google.maps.places.RankBy.DISTANCE,
        type: ['cafe'],
      }, this.callbackCafe);
      // search nearby pharmacy from current cursor
      map.nearbySearch({
        location: current_cursor, //Add initial lat/lon here
        rankBy: this.google.maps.places.RankBy.DISTANCE,
        type: ['pharmacy'],
      }, this.callbackPharmacy);
      // search nearby convenience_store from current cursor
      map.nearbySearch({
        location: current_cursor, //Add initial lat/lon here
        rankBy: this.google.maps.places.RankBy.DISTANCE,
        type: ['convenience_store'],
      }, this.callbackConvenienceStore);


      let restaurant = new this.google.maps.LatLng(this.current_distance.restaurant.lat,
        this.current_distance.restaurant.lng)
      let atm = new this.google.maps.LatLng(this.current_distance.atm.lat,
        this.current_distance.atm.lng)
      let cafe = new this.google.maps.LatLng(this.current_distance.cafe.lat,
        this.current_distance.cafe.lng)
      let pharmacy = new this.google.maps.LatLng(this.current_distance.pharmacy.lat,
        this.current_distance.pharmacy.lng)
      let convenience_store = new this.google.maps.LatLng(this.current_distance.convenience_store.lat,
        this.current_distance.convenience_store.lng)

      this.distance_from.restaurant = (this.getDistance(current_cursor,restaurant) / 1000).toFixed(2)
      this.distance_from.atm = (this.getDistance(current_cursor,atm) / 1000).toFixed(2)
      this.distance_from.cafe = (this.getDistance(current_cursor,cafe) / 1000).toFixed(2)
      this.distance_from.pharmacy = (this.getDistance(current_cursor,pharmacy) / 1000).toFixed(2)
      this.distance_from.convenience_store = (this.getDistance(current_cursor,convenience_store) / 1000).toFixed(2)

    },
    updateZoom(e){
      this.current_zoom = e
    },
    updateCenter(e) {
      this.current_position.lat = e.lat()
      this.current_position.lng = e.lng()
    },
    updateData(){
      this.getDistanceTo()

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

        // default radius in meters
        let searchArea = new this.google.maps.Circle({
          center : new this.google.maps.LatLng(this.current_position.lat,this.current_position.lng),
          radius : this.radius
        });

        this.circle_markers = []

        this.markers.map((x) => {
          let marker_position = new this.google.maps.LatLng(x.position.lat,x.position.lng)
          if (this.google.maps.geometry.spherical.computeDistanceBetween(marker_position, searchArea.getCenter()) 
            <= searchArea.getRadius()) {
              this.circle_markers.push(x)
            }
        })

        this.afterLoading = true
      }, 1000)
    },
    toggleInfoWindow (marker, idx) {
      let content = `
      <h6 class="font-weight-bold" style="padding:15px;padding-bottom:7px;"> 
        ${marker.price} 
      </h6>`

      this.infoWindowPos = marker.position;
      this.infoOptions.content = content;

      this.infoWinOpen = true;
      this.currentMidx = idx;
    },

    markerInfoWindow (marker,idx){
      let content = `
      <img src="/properties/${marker.image}" class="img-marker">
      <div class="info">
        <div class="location text-truncate">
        <i class="fal fa-map-marker-alt mr-1"></i> <span class="text-secondary">${marker.location}</span>
        </div>
        <div class="title mt-2 text-truncate">
          ${marker.name}
        </div>
        <div class="price mt-1 mb-2">
          ${marker.price}
        </div>
        <span class="font-weight-normal pl-0 mr-1 bd-right badge">
          <i class="far fa-bed fa-lg mr-2"></i><span style="font-size:14px;">2</span>
        </span>
        <span class="font-weight-normal pl-0 mr-1 bd-right badge">
          <i class="far fa-bath fa-lg mr-2"></i><span style="font-size:14px;">2</span>
        </span>
        <span class="font-weight-normal pl-0 mr-1 bd-right badge">
          <i class="far fa-expand-arrows fa-lg mr-2"></i><span style="font-size:14px;">2 are</span>
        </span>
        <span class="font-weight-normal pl-0 mr-1 bd-right badge">
          <i class="far fa-home fa-lg mr-2"></i><span style="font-size:14px;">1300 m²</span>
        </span>
      </div>`

      this.infoWindowPos = marker.position;
      this.infoOptions.content = content;

      //check if its the same marker that was selected if yes toggle
      if (this.currentMidx == idx) {
        this.infoWinOpen = !this.infoWinOpen;
      }
      //if different marker set infowindow to open and reset current marker index
      else {
        marker.clicked = true
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
  updated(){
    this.getDistanceTo()
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
  border-radius:12px;
}
.map-search-title {
    font-weight: 600 !important;
    color: rgb(34, 34, 34) !important;
}
.btn-red-hot {
  background-color: #ff385c;
  color: #fff;
}
.btn-red-hot:hover {
    background-color: #ff385c;
    color: #fff;
}
.info {
    padding: 10px;
    text-align: left;
    overflow-wrap: break-word;
}
.info .location{
  font-size: 14px !important;
  max-width:230px;
}
.info .title {
    font-weight: 400 !important;
    color: rgb(34, 34, 34) !important;
    font-size: 16px !important;
    padding-bottom: 5px;
    max-width: 230px;
}
.info .price {
  color: rgb(34, 34, 34) !important;
  font-weight: 800;
  font-size: 16px !important;
}
.img-marker {
  object-fit: cover;
  width: 250px;
  height: 200px;
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
.property-distance h3{
  color: rgb(34,34,34) !important;
  font-size: 22px !important;
}
.fs-14{
  font-size: 14px;
}
</style>
