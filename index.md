---
layout: page
title: HPC at the University of Huddersfield ~ QueensGate Grid
---
{% include JB/setup %}

<div class="jumbotron">
  <h1>Welcome to the QueensGate Grid</h1>
  <h2>The campus grid at the University of Huddersfield</h2>
  <br />
  <p>3500+ CPU cores and 7+ TB RAM running your jobs in <b>
  <span class="flyer">
        <span>MATLAB</span>
        <span>MPI C / C++</span>
        <span>ANSYS</span>
        <span>CFX</span>
        <span>Fluent</span>
        <span>COMSOL</span>
        <span>DL_POLY</span>
        <span>Docker</span>
        <span>mental ray</span>
    </span>

    </b></p>
</div>

<div class="row">
  <div class="col-md-12">
    
  </div>
</div>

<div class="row">
  <div class="col-sm-6 col-md-4">
    <div class="thumbnail">
      <div class="caption">
        <h3>Research Group</h3>
        <p>Information about our HPC Research Group, areas of interest and what we are doing.</p>
        <p><a href="/research/" class="btn btn-default" role="button">Find out more</a></p>
      </div>
    </div>
  </div>
  <div class="col-sm-6 col-md-4">
    <div class="thumbnail">
      <div class="caption">
        <h3>Systems and Services</h3>
        <p>Check status and find specs of the available HPC systems for students and staff.</p>
        <p><a href="/systems/" class="btn btn-default" role="button">Find out more</a></p>
      </div>
    </div>
  </div>
  <div class="col-sm-6 col-md-4">
    <div class="thumbnail">
      <div class="caption">
        <h3>Documentation and Help</h3>
        <p>The Resource Centre is here to provide help and support for our HPC systems.</p>
        <p><a href="/docs/" class="btn btn-default" role="button">Find out more</a></p>
      </div>
    </div>
  </div>
</div>


<a class="twitter-timeline" href="https://twitter.com/hpcrc" data-widget-id="723485854459662336" width="100%">Tweets by @hpcrc</a>
<script>!function(d,s,id){var js,fjs=d.getElementsByTagName(s)[0],p=/^http:/.test(d.location)?'http':'https';if(!d.getElementById(id)){js=d.createElement(s);js.id=id;js.src=p+"://platform.twitter.com/widgets.js";fjs.parentNode.insertBefore(js,fjs);}}(document,"script","twitter-wjs");</script>

<script>
$('.flyer>span:not(:first-child)').hide();
var count = 1;
setInterval(function() {
    var objs = $('.flyer>span');
    objs.hide();
    $(objs[count % objs.length]).fadeIn('slow');
    count++;
}, 2000);
</script>
