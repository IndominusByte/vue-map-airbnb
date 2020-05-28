<template>
  <div>
    <div class="row scroll-none">
      <div class="col map-sticky">
        <gmap-map
          ref="mapRef"
          :options="{
           zoomControl: true,
           mapTypeControl: false,
           scaleControl: false,
           streetViewControl: true,
           rotateControl: false,
           fullscreenControl: false,
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
          >

          <gmap-info-window 
            :options="infoOptions" 
            :position="infoWindowPos" 
            :opened="infoWinOpen" 
          >
          </gmap-info-window>

          <gmap-marker
            v-for="(m, index) in circle_markers"
            :key="index"
            :position="m.position"
            :icon="markerOptions"
            @click="markerInfoWindow(m,index)"
          >
          </gmap-marker>
        </gmap-map>
      </div>

      <div class="col pr-0 pl-0 main">
        <p class="pl-3 pt-3 font-weight-bold">{{circle_markers.length ? circle_markers.length : 'No' }} results</p>
        <div class="row p-3">
          <div class="col-md-6" 
            v-for="(m,index) in circle_markers" :key="index" @mouseover="toggleInfoWindow(m,index)"
            @mouseout="infoWinOpen = !infoWinOpen"
            >
            <div class="card mb-3">
              <a href="#">
                <img class="card-img-top img-fit" src="../assets/sale.jpg" alt="Card image cap">
              </a>                                       
              <div class="img-block">                    
                <div class="rate-info">                  
                  <h5> {{m.infoText}}</h5>                        
                  <span class="for-sale">FOR SALE</span  >
                </div>                                   
              </div>                                     
                                                         
              <div class="text-dark card-body">
                <div class="text-dark font-weight-bold mb-1 hov_pointer card-title h5">{{m.name}}</div>
                <p class="fs-12 text-secondary card-text"><i class="jsx-1104155267 fal fa-map-marker-alt"></i> Seminyak, Kuta, Bali 30123.</p>
                <span class="font-weight-normal pl-0 mr-2 bd-right badge"><i class="jsx-1104155267 far fa-bed fa-lg mr-2"></i><span class="jsx-1104155267 pr-1">2</span></span>
                <span class="font-weight-normal pl-0 mr-2 bd-right badge"><i class="jsx-1104155267 far fa-bath fa-lg mr-2"></i><span class="jsx-1104155267 pr-1">2</span></span>
                <span class="font-weight-normal pl-0 mr-2 bd-right badge"><i class="jsx-1104155267 far fa-expand-arrows fa-lg mr-2"></i><span class="jsx-1104155267 pr-1">2 are</span></span>
                <span class="font-weight-normal pl-0 mr-2 bd-right badge"><i class="jsx-1104155267 far fa-home fa-lg mr-2"></i><span class="jsx-1104155267 pr-1">1300 m²</span></span>
                <span class="font-weight-normal pl-0 mr-2 badge"><i class="jsx-1104155267 far fa-file-certificate fa-lg mr-2"></i><span class="jsx-1104155267 pr-1">Lease Hold</span></span>
              </div><!--/card-body-->

              <div class="text-muted card-footer">
                <div class="fs-12 row">
                  <div class="col"><i class="jsx-1104155267 fal fa-lg fa-calendar-check mr-2"></i> 2 Days ago</div>
                  <div class="text-right col">
                    <span class="jsx-1104155267 text-decoration-none text-muted mr-2 pr-2 hov_pointer bd-right"><i class="jsx-1104155267 fal fa-lg fa-heart"></i></span>
                    <a class="jsx-1104155267 text-decoration-none text-muted hov_pointer]"><i class="jsx-1104155267 fal fa-lg fa-share-alt"></i></a>
                  </div>
                </div>
              </div><!--/card-footer-->

            </div><!--/card-->

          </div><!--/col-->
        </div><!--/row-->
      </div>

    </div><!-- row -->
  </div>
</template>

<script>
import {gmapApi} from 'vue2-google-maps'

const mapMarker = require('../assets/marker.png');

export default {
  data() {
    return {
      center: { lat: -8.340539, lng: 115.091949 },
      circle_markers: [],
      current_position: {lat: null,lng: null},
      radius:0,
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
      if (e <= 7){
        this.circle_markers = []
        return false
      }
      if (e == 8) this.radius = 60000
      if (e == 9) this.radius = 50000
      if (e == 10) this.radius = 30000
      if (e == 11) this.radius = 20000
      if (e >= 12) this.radius = 10000
      if (e >= 13) this.radius = (300*100) / e

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

    },
    updateCenter(e) {
      // set to global
      this.current_position.lat = e.lat()
      this.current_position.lng = e.lng()

      var searchArea = new this.google.maps.Circle({
        strokeColor: '#FF0000',
        strokeOpacity: 0.8,
        strokeWeight: 2,
        center : new this.google.maps.LatLng(e.lat(), e.lng()),
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
    // At this point, the child GmapMap has been mounted, but
    // its map has not been initialized.
    // Therefore we need to write mapRef.$mapPromise.then(() => ...)

    this.$refs.mapRef.$mapPromise.then((map) => {
      map.panTo({lat: this.center.lat, lng: this.center.lng})

      // set to global
      this.current_position.lat = this.center.lat
      this.current_position.lng = this.center.lng
      this.radius = 30000

      var searchArea = new this.google.maps.Circle({
        strokeColor: '#FF0000',
        strokeOpacity: 0.8,
        strokeWeight: 2,
        center : new this.google.maps.LatLng(this.center.lat,this.center.lng),
        radius : this.radius
      });
      this.markers.map((x) => {
        var lol = new this.google.maps.LatLng(x.position.lat,x.position.lng)
        if (this.google.maps.geometry.spherical.computeDistanceBetween(lol, searchArea.getCenter()) 
          <= searchArea.getRadius()) {
            this.circle_markers.push(x)
          }
      })

    })
  }
}
</script>

<style>
@import '../assets/fontawesome/css/all.css';

.gm-ui-hover-effect { display: none !important; }
.gm-style-iw-d {overflow:hidden !important;}
.gm-style .gm-style-iw-c{
  padding: 0px;
  top:40px;
}
.gm-style .gm-style-iw-t::after{
  top:38px;
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
/* Card Label */
.rate-info {
    float: left;
    width: 100%;
    position: absolute;
    bottom: 0;
    left: 0;
    padding: 40px 23px 20px 23px;
    z-index: 2;
}
.rate-info:before {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-image: linear-gradient(rgba(255, 255, 255, 0.02), rgba(44, 44, 47, 47));
    z-index: -1;
}
.cer span {
    color: #fff;
    text-transform: uppercase;
    font-size: 12px;
    background: rgba(145, 147, 152, 0.7);
    border-radius: 50px;
    padding: 5px 13px;
    float: right;
    /* margin-right: 24px; */
    margin-top: 2px;
}
.img-block {
    position: relative;
    z-index: 1;
    float: left;
    width: 100%;
}
.img-block h5, .map-box .rate-info > h5 {
    margin: 0px;
    color: #fff;
    font-size: 20px;
    font-weight: 600;
    float: left;
    /* padding-left: 24px; */
    position: relative;
    top: 5px;
}
.card .img-block span, .map-box .rate-info > span {
    color: #fff;
    text-transform: uppercase;
    font-size: 12px;
    background: rgba(145, 147, 152, 0.7);
    border-radius: 50px;
    padding: 5px 13px;
    float: right;
    /* margin-right: 24px; */
    margin-top: 2px;
}
.card .img-block span.for-sale, .map-box .rate-info > span.for-sale {
    background: #fc384a;
}
.card .img-block span.for-rent, .map-box .rate-info > span.for-rent {
    background: #1778F2;
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
