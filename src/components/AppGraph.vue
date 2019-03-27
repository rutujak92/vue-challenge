<template>
  <div class="container-fluid chart-wrapper">
    <div class="row text-left">
        <div class="col-lg-6 col-xs-12">
          <div class="row">
            <div class="col-lg-6 form-group">
                <label>Enter Graph Width:</label>
                <input v-model="width" type="text" class="form-control" placeholder="e.g 300">
              </div>
              <div class="col-lg-6 form-group">
                <label>Enter Graph Height:</label>
                <input v-model="height" class="form-control" placeholder="e.g. 300">
              </div>
          </div>
          <div class="row">
            <div class="form-inline col-lg-6 ui-form text-left">
              <div class="form-group py-2">
                <label class ="mr-2">Enter Label:</label>
                <input v-model="label" class="form-control" placeholder="e.g.January">
              </div>
              <div class="form-group py-2">
                <label class ="mr-2">Enter Data:</label>
                <input v-model="data" type="number" class="form-control" placeholder="e.g 50">
              </div>
              <div class="form-group py-2">
                <label class ="mr-2">Enter Color Code:</label>
                <input v-model="color" class="form-control" placeholder="e.g f2f2f2">
              </div>
              
            </div>   
          </div>
          <div class="row">
            <div class="form-group">
                <button class="btn btn-primary" @click="addData">Add Data</button>
            </div>
          </div>
          <div class="row">
            <div class="col-lg-12 error text-center py-2" ref=error >
              <span >All inputs are needed to generate graph</span>
            </div>
            
          </div>
      
      </div>
      <div class="col-lg-6 ">
        <div class="row py-4">
            <div class="col-lg-12 graph-specs">
              <h6 class="py-2">Graph Specs:</h6>
              <ul ref="specs">

              </ul>
            </div>
            <div class="col-lg-12 py-4 px-0">
              <button class="btn btn-primary" @click="generateGraph">Generate Graph</button>
            </div>
          </div>
      </div>
    </div>
    <div class="row py-4">
      <div class="col-lg-12 text-left"> 
        <h6>Bar Graph:</h6>
        <bar-graph :graph-data = "graphObject" ref = "graph"></bar-graph>
      </div>
    </div>
    
  </div>
</template>

<script>
import BarGraph from "./BarGraph";
export default {
  components: {
    "bar-graph": BarGraph
  },
  props: {
    label: String,
    data: Number,
    color: String,
    width: Number,
    height: Number
  },
  data(){
    return{
      graphObject: {
        graphData : [],
        xWidth : '',
        yHeight : '' 
      }
    }
  },
  methods:{
    generateGraph(){
      var isValid = this.validateUiInputs();
      var error = this.$refs.error;
      if(isValid){
        this.graphObject.xWidth = this.width;
        this.graphObject.yHeight = this.height;
        error.style.display = 'none';
        this.$refs.graph.drawGraph(this.graphObject);
      }
      else{
        var error = this.$refs.error;
        error.style.display = 'block';
      }
     
    },
    validateDataInputs (){
      return this.label && this.color && this.data;
    },
    validateUiInputs (){
       return this.width && this.height;
    },
    addData(event){
      var error = this.$refs.error;
      var isValid = this.validateDataInputs();
      if(isValid){
        var label = this.label;
        var data = parseInt(this.data,10);
        var color = this.color;
        var obj = {
            label : label,
            data : data,
            color : color
        };
        this.graphObject.graphData.push(obj);
        this.generateSpecs(label,data,color);
        this.clearInputs();
        error.style.display = 'none';
      }
      else{
        
        error.style.display = 'block';
      }

    },
    clearInputs(){
      this.label = "";
      this.data = "";
      this.color = "";

    },
    generateSpecs(label,data,color){
     
      var ul = this.$refs.specs;
      var li = document.createElement("li");
      var textLabel = label + ":" + data + ', color: ' + color  ;
      li.textContent = textLabel;
      ul.append(li);
    }
  },
  name: "AppGraph"
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
.error{
  color: #721c24;
  background-color: #f8d7da;
  border-color: #f5c6cb;
  display:none;
}
a {
  color: #42b983;
}
.graph-specs{
  background-color :#f2f2f2;
}
.btn{
  
}
</style>
