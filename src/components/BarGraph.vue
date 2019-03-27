<template>
  <div class="container-fluid wrapper">
    <canvas class="canvas bar-canvas" ref="graph" @mousemove="showToolTip"></canvas>
    <canvas class="canvas tip-canvas" ref="tip" width="100" height="20" ></canvas>
    <legend ref="legend"></legend>
  </div>
  
</template>

<script>
export default {
  name: "BarGraph",
  props: ["graphData"],
  data: function(){
    return{
      dataPoints : [],
    }
  },
  methods: {
    drawGraph(graphObject){

      var graphCanvas = this.$refs.graph; 
      var dataSet = graphObject.graphData;
      var gridScale = 5;
      var padding = 20;
      var ctx = graphCanvas.getContext("2d");
      var maxValue=0;
      for(var value of dataSet){
        maxValue = Math.max(maxValue,value.data)
      }
     if(window.innerWidth<=768){
        var relativeWidth = graphObject.xWidth/window.innerWidth *100;
        graphCanvas.height = relativeWidth;
        graphCanvas.width = relativeWidth;
      }
      else{
        graphCanvas.height = graphObject.yHeight;
        graphCanvas.width = graphObject.xWidth;
      }
        
      var canvasActualHeight = graphCanvas.height - padding * 2;
      var canvasActualWidth = graphCanvas.width - padding * 2;
      var gridValue = 0;
      //drawing the grid lines

      while (gridValue <= maxValue){
        var gridY = canvasActualHeight * (1 - gridValue/maxValue) + padding;
        this.drawLine( ctx, 0, gridY, graphCanvas.width, gridY, '#ccc');
        //writing grid markers
        ctx.save();
        ctx.fillStyle = '#696969';
        ctx.font = "bold 10px Arial";
        ctx.fillText(gridValue, 0,gridY - 2);
        ctx.restore();
        gridValue+=gridScale;
      }
     
      //drawing the bars
      var barIndex = 0;
      var numberOfBars = dataSet.length;
      var barSize = (canvasActualWidth)/numberOfBars;
      
      for(var item of dataSet){
        var value = item.data;
        var barHeight = Math.round( canvasActualHeight * value/maxValue) ;
        var upperLeftCornerX = padding + barIndex * barSize;
        var nextX = padding + (barIndex+1) * barSize;
        var upperLeftCornerY = graphCanvas.height - barHeight - padding;
        var label = item.label;
        this.drawBar(
            ctx,
            upperLeftCornerX,
            upperLeftCornerY,
            barSize,
            barHeight,
            item.color
        );
        var upperEdge = canvasActualHeight - barHeight;
        var dataPoint = this.getDataPoint( upperLeftCornerX, nextX, upperEdge, value, label);
        this.dataPoints.push(dataPoint);
        barIndex++;
      }
      
      //draw legend
      barIndex = 0;
      var legend = this.$refs.legend;
      this.$refs.legend.innerHTML = '';
      console.log( this.$refs.legend.innerHTML)
      legend.style.left = canvasActualWidth + 50 + "px";
      var ul = document.createElement("ul");
      legend.append(ul);
      for (item of dataSet) {
        var li = document.createElement("li");
        li.style.listStyle = "none";
        li.style.borderLeft = "20px solid #" + item.color;
        li.style.padding = "5px";
        li.textContent = item.label;
        ul.append(li);
        barIndex++;
      } 
    },
    drawLine(ctx, startX, startY, endX, endY, color) {
      ctx.save();
      ctx.strokeStyle = color;
      ctx.beginPath();
      ctx.moveTo(startX, startY);
      ctx.lineTo(endX, endY);
      ctx.stroke();
      ctx.restore();
    },
    drawBar(ctx, upperLeftCornerX, upperLeftCornerY, width, height, color) {
      ctx.save();
      ctx.fillStyle = "#" + color;
      ctx.fillRect(upperLeftCornerX, upperLeftCornerY, width, height);
      ctx.restore();
    },
    getDataPoint(x, nextX, upperEdge, value, label) {
      return {
        leftEdge: x,
        rightEdge: nextX,
        upperEdge: upperEdge,
        value: value,
        label: label
      };
    },
    showToolTip(event) {
      var mouseX = event.offsetX;
      var mouseY = event.offsetY;
      var hit = false;
      var toolTipVal = "";
      var toolTipLabel = "";
      var toolTipText = "";
      var tipCanvas = this.$refs.tip;
      var tipCtx = tipCanvas.getContext("2d");
      tipCtx.font = "12px Arial";
      this.dataPoints.forEach(function(item) {
        if (
          mouseX >= item.leftEdge &&
          mouseX <= item.rightEdge &&
          mouseY >= item.upperEdge
        ) {
          toolTipVal = item.value;
          toolTipLabel = item.label;
          toolTipText = toolTipLabel + " : " + toolTipVal;
          tipCtx.save();
          tipCanvas.style.left = mouseX + "px";
          tipCanvas.style.top = mouseY - 40 + "px";
          tipCtx.clearRect(0, 0, tipCanvas.width, tipCanvas.height);
          tipCtx.fillText(toolTipText, 5, tipCanvas.height - 6);
          tipCtx.restore();
          hit = true;
          return;
        }
      });
      if (!hit) {
        tipCanvas.style.left = "-999px";
      }
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.wrapper {
  min-height: 500px;
  position: relative;
}
legend {
  position: absolute;
}
legend ul {
  text-align: left;
  line-height :12px;
  font-size:14px;
}
.canvas {
  position: absolute;
}
.bar-canvas {
  left: 0;
  cursor: pointer;
}
.tip-canvas {
  left: -999px;
  background-color: #ccc;
  border: 1px solid #888;
}
</style>
