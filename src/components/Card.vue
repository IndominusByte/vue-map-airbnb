<template>
<div class="card mb-3">
  <a href="#">
    <img class="card-img-top img-fit" src="../assets/sale.jpg" alt="Card image cap">
  </a>                                       
  <div class="img-block">                    
    <div class="rate-info">                  
      <h5> {{price}}</h5>                        
      <span class="for-sale">FOR SALE</span  >
    </div>                                   
  </div>                                     
                                             
  <div class="text-dark card-body">
    <div class="text-dark font-weight-bold mb-1 hov_pointer card-title h5">{{name}}</div>
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
</template>

<script>
export default{
  props: ['price','name']
}
</script>

<style>
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

</style>
