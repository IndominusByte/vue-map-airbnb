<template>
<div class="card mb-3">
  <div class="row no-gutters">
    <div class="col-md-4">
      <a href="#">
        <img class="img-fit img-left" :src="'/properties/' + image" alt="...">
      </a>                                       
      <div class="img-block">                    
        <div class="rate-info">                  
          <h5> {{price}} <small class="fs-12"> / monthly</small></h5>                        
          <span class="for-sale">FOR SALE</span>
        </div>                                   
      </div>                                     
    </div><!--/col-->
    <div class="col-md-8">
      <div class="text-dark card-body">
        <div class="text-dark font-weight-bold mb-1 hov_pointer card-title text-truncate h5">{{name}}</div>
        <p class="fs-12 text-secondary card-text text-truncate"><i class="jsx-1104155267 fal fa-map-marker-alt"></i> {{location}}</p>
        <span class="font-weight-normal pl-0 mr-2 bd-right badge"><i class="jsx-1104155267 far fa-bed fa-lg mr-2"></i><span class="jsx-1104155267 pr-1">2</span></span>
        <span class="font-weight-normal pl-0 mr-2 bd-right badge"><i class="jsx-1104155267 far fa-bath fa-lg mr-2"></i><span class="jsx-1104155267 pr-1">2</span></span>
        <span class="font-weight-normal pl-0 mr-2 bd-right badge"><i class="jsx-1104155267 far fa-expand-arrows fa-lg mr-2"></i><span class="jsx-1104155267 pr-1">2 are</span></span>
        <span class="font-weight-normal pl-0 mr-2 bd-right badge"><i class="jsx-1104155267 far fa-home fa-lg mr-2"></i><span class="jsx-1104155267 pr-1">1300 m²</span></span>
        <span class="font-weight-normal pl-0 mr-2 badge"><i class="jsx-1104155267 far fa-file-certificate fa-lg mr-2"></i><span class="jsx-1104155267 pr-1">Lease Hold</span></span>

      </div><!--/card-body-->
      <div class="card-footer bg-white text-muted">
        <div class="fs-12 row ">
          <div class="col"><i class="jsx-1104155267 fal fa-lg fa-calendar-check mr-2"></i> 2 Days ago</div>
          <div class="text-right col">
            <span class="jsx-1104155267 text-decoration-none text-muted mr-2 pr-2 hov_pointer bd-rt"><i class="jsx-1104155267 fal fa-lg fa-heart"></i></span>
            <a class="jsx-1104155267 text-decoration-none text-muted hov_pointer]"><i class="jsx-1104155267 fal fa-lg fa-share-alt"></i></a>
          </div>
        </div>
      </div>
    </div><!--/col-->
    
  </div><!--/row-no-gutters-->

</div><!--/card-->
</template>

<script>
export default{
  props: ['price','name','image','location']
}
</script>

<style>
/* Image left */
.img-left {
  object-fit: cover;
  width: 100%;
  height: 100%
}
/* Card Label */
.bd-rt {
    border-right: 1px solid #ddd;
    border-radius: 0px;
    margin-right: 0.4em;
}
.fs-12 {
    font-size: 12px;
}
.rate-info small {
  font-weight: 400;
}
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
    font-size: 15px;
    font-weight: 600;
    float: left;
    position: relative;
    top: 14px;
    right: 16px;
}
.card .img-block span, .map-box .rate-info > span {
    color: #fff;
    text-transform: uppercase;
    font-size: 12px;
    background: rgba(145, 147, 152, 0.7);
    border-radius: 50px;
    padding: 2px 8px;
    float: right;
    margin-top: 2px;
    position: relative;
    left: 18px;
    top: 12px;
}
.card .img-block span.for-sale, .map-box .rate-info > span.for-sale {
    background: #fc384a;
}
.card .img-block span.for-rent, .map-box .rate-info > span.for-rent {
    background: #1778F2;
}
@media only screen and (max-width: 1366px) {
.img-block h5, .map-box .rate-info > h5 {
    margin: 0px;
    color: #fff;
    font-size: 15px;
    font-weight: 600;
    float: left;
    position: relative;
    top: 35px;
    right: 16px;
  }
}

</style>
