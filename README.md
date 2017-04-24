# d3-randoms
Just some d3 tinkering.


## Bubble Sort
'''html
<!DOCTYPE html>
<meta charset="utf-8">

<script type="text/javascript" src="d3.min.js"></script>
<svg></svg>
<button onclick="randomCircles()">Randomize</button>
<script>
// Adapted from http://martin.ankerl.com/2009/12/09/how-to-create-random-colors-programmatically/
var randomColor = (function(){
  var golden_ratio_conjugate = 0.618033988749895;
  var h = Math.random();
  var hslToRgb = function (h, s, l){
      var r, g, b;
      if(s == 0){
          r = g = b = l; // achromatic
      }else{
          function hue2rgb(p, q, t){
              if(t < 0) t += 1;
              if(t > 1) t -= 1;
              if(t < 1/6) return p + (q - p) * 6 * t;
              if(t < 1/2) return q;
              if(t < 2/3) return p + (q - p) * (2/3 - t) * 6;
              return p;
          }
          var q = l < 0.5 ? l * (1 + s) : l + s - l * s;
          var p = 2 * l - q;
          r = hue2rgb(p, q, h + 1/3);
          g = hue2rgb(p, q, h);
          b = hue2rgb(p, q, h - 1/3);
      }
      return '#'+Math.round(r * 255).toString(16)+Math.round(g * 255).toString(16)+Math.round(b * 255).toString(16);
  };
  
  return function(){
    h += golden_ratio_conjugate;
    h %= 1;
    return hslToRgb(h, 0.5, 0.60);
  };
})();
</script>

<script>
function randomArray(num_values){
	for (var A=[],i=0;i<num_values; ++i) A[i]=i+1;
	// http://stackoverflow.com/questions/962802#962890
	function shuffle(array) {
  		var tmp, current, top = array.length;
  		if(top) while(--top) {
    		current = Math.floor(Math.random() * (top + 1));
    		tmp = array[current];
    		array[current] = array[top];
    		array[top] = tmp;
  		}
  	return array;
	}
	return shuffle(A);
}
</script>

<script>
	//See http://martin.ankerl.com/2009/12/09/how-to-create-random-colors-programmatically/
	//and http://bl.ocks.org/jdarling/06019d16cb5fd6795edf
	// for randomness in colors
	var SVG_WIDTH = 900,
		SVG_HEIGHT = 240;
	var CIRCLE_PADDING = 1;
	var data = randomArray(10);
	var svg = d3.select("svg")
				.attr("width", SVG_WIDTH)
				.attr("height", SVG_HEIGHT);
	g = svg.append("g");
	
	function update(data){
		var t = d3.transition().duration(1750);
		var max_radius = Math.sqrt(Math.max.apply(Math, data)),
			section_width_out_padding = (SVG_WIDTH / data.length) - (2 * CIRCLE_PADDING); //punny
		
		var gradient = svg.append("defs")
				  	  .selectAll("radialGradient")
				  	  .data(data)
				  	  .enter()
				  	  .append("radialGradient")
				      .attr("id", function(d, i){
				      	return "grad" + i;
				      })
				      .attr("x1", "0%")
				      .attr("y1", "0%")
				      .attr("x2", "100%")
				      .attr("y2", "100%")
				      .attr("spreadMethod", "pad");
		gradient.append("stop")
			    .attr("offset", "0%")
			    .attr("stop-color", "white")
			    .attr("stop-opacity", 0.8);
    	gradient.append("stop")
    			.attr("offset", "100%")
    			.attr("stop-color", randomColor)
    			.attr("stop-opacity", 0.8);
		//JOIN
		var circles = g.selectAll("circle")
					   .data(data, function(d){ return d; }); //update selection
		//EXIT
		circles.exit() //get exit selection
			   .attr("class", "exit")
			   .transition(t)
			   .attr("cy", 200)
			   .style("fill-opacity", 1e-6)
      		   .remove();
      	//UPDATE
      	circles.attr("class", "update")
      		   .attr("cy", 120)
      		   .style("fill-opacity", 1)
      		   .transition(t)
      		   .attr("r", function(d) { return section_width_out_padding * (Math.sqrt(d) / max_radius) / 2; })
      		   .attr("cx", function(d, i){
      		   		return (i * (section_width_out_padding + 2 * CIRCLE_PADDING)) + CIRCLE_PADDING + (section_width_out_padding / 2);
      		   });
      	//ENTER
      	circles.enter()
      		   .append("circle")
      		   .attr("class", "enter")
      		   .attr("cy", 80)
      		   .attr("cx", function(d, i){
      		   		return (i * (section_width_out_padding + 2 * CIRCLE_PADDING)) + CIRCLE_PADDING + (section_width_out_padding / 2);
      		   })
      		   .attr("r", function(d) { return section_width_out_padding * (Math.sqrt(d) / max_radius) / 2; })
      		   .attr("fill", function(d, i){
					return "url(#grad" + i + ")"
				})
      		   .style("fill-opacity", 1e-6)
      		   .attr("stroke", "black")
      		   .transition(t)
      		   .attr("cy", 120)
       		   .style("fill-opacity", 1);
	}
	update(randomArray(10));
	function randomCircles(){
		var r = Math.floor((Math.random(10) * 10) + 10);
		update(randomArray(r));
		return;
	}
/*
	var circle = svg.selectAll("circle")
					.data(data)
					.enter()
					.append("circle")
					.attr("cy", SVG_HEIGHT / 2)
					.attr("cx", function(d, i) {
						return (i * (section_width_out_padding + 2 * CIRCLE_PADDING)) + CIRCLE_PADDING + (section_width_out_padding / 2);
						
					})
					.attr("r", function(d) { return section_width_out_padding * (Math.sqrt(d) / max_radius) / 2; })
					.attr("fill", function(d, i){
						return "url(#grad" + i + ")"
					})
					.attr("opacity", 0.8)
					.attr("stroke", "black");
*/
</script>
'''
