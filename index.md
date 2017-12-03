# Votaciones al Concejo de Bogotá en los periodos 2011 y 2015
<br/>
### Lo que se espera lograr:
La tarea que se buscan conseguir por medio de esta visualizacion es:<br/>
○ Comparar los resultados obtenidos por un candidato frente a otros en los resultados electorales por localidad (zona) para las elecciones al Concejo de Bogotá en los periodos 2011 y 2015.<br/><br/>

Para lograrlo se ha elegido visualizar por medio de paralell coordinates que permite ver el comportamiento para las distintas localidades, así mismo se han implementado filtros que permiten elegir los candidatos que se quieren comparar.  Se puede hacer brush para elegir el eje o la zona a verificar. <br/><br/>

Esta visualización permitirá a nuestro cliente Diego Laserna comparar visualmente la cantidad de votos que obtuvo contra otros candidatos de su elección evidenciando facilmente en que localidades fue más fuerte uno u otro, además le da la facilidad de comparar con candidatos del periodo 2011, que siendo el candidato de 2015 es una buena estrategia de comparación. 
<br/><br/>

### Análisis e Insights
Los insights logrados a partir de la visualizacion son:<br/><br/>
○ La diferencia de votos entre el consejal elegido del partido verde con mayor cantidad de votos, Antonio Sanguino Páez,  y Diego Laserna es cercana a los 4500 votos.  <br/> 
○ La localidad que presenta mayor cantidad de votos del candidato Diego Laserna, son las zonas 1 y 2 seguida, no tan de cerca por la zona 11, lo cual resulta consistente con el comportamiento global de votos de candidatos de su partido. 

<title>Parallel Coordinates - Concejo de Bogotá</title>
  <meta charset="utf-8">
  <link rel="stylesheet" href="css/style.css">
  <script src="js/d3.v4.min.js"></script>
  <script src="js/render-queue.js"></script>
</head>

<body>
  
  <div class="pcCenterControls">
    <p>
      <label>Año:</label>
      <select class="selectPC" id="yearList">
        <option value=0>...</option>
      </select>
    </p>
    <p>
      <label>Partido:</label>
      <select class="selectPC" id="partyList">
        <option value=0>...</option>
      </select>  
    </p>
    <p>
      <label>Candidato:</label>
      <select class="selectPC" id="candidateList">
        <option value=0>...</option>
      </select>  
      <button class="buttonPC" type="button" onclick="addName()">Agregar</button>      
    </p>
    <p>
      <label></label>
      <select class="selectMultiplePC" id="selectNames" multiple>
      </select>
      <button class="buttonPC" type="button" onclick="removeName()">Quitar</button>      
    </p>
  </div>
 
  <div id="pcDiv" class="pcCenterGraph"></div>  
    
<script>

  jsonFileLocation = "data/JSON/Datos_Total_PC.json";


  d3.select("#yearList").on("change", function () {
    year = d3.select("#yearList").property("value");
    fillPartyList(year);
  }); 

  d3.select("#partyList").on("change", function () {
    year = d3.select("#yearList").property("value");
    partyCode = d3.select("#partyList").property("value");
    fillCandidateList(year, partyCode);
  });
  // función que carga la lista de periodos
  function fillYearList() {
    
    d3.json(jsonFileLocation, function(error, data) {
    
      if (error) throw error;
      
      document.getElementById('yearList').options.length = 0;
    
      var sel = document.getElementById('yearList');
      var options = d3.map(data, function(d){return d.PERIODO;}).keys();
      var opt = document.createElement('option');
      
      opt.innerHTML = "...";
      opt.value = 0;
      sel.appendChild(opt);
      
      options = options.sort();
      
      for(var i = 0; i < options.length; i++) {
        opt = document.createElement('option');
        opt.innerHTML = options[i];
        opt.value = options[i];
        sel.appendChild(opt);
      } 

    });
      
  }

  // funcion que carga la lista de partidos por periodo
  function fillPartyList(year){
  
    d3.json(jsonFileLocation, function(error, data) {
    
      if (error) throw error;    
          
      document.getElementById('partyList').options.length = 0;
      document.getElementById('candidateList').options.length = 0;
      
      data = data.filter(function(row) {
        return row['PERIODO'] == year;
      });

      var opt;
     
      var sel = document.getElementById('candidateList');
      opt = document.createElement('option');
      opt.innerHTML = "...";
      opt.value = 0;      
      sel.appendChild(opt);

      sel = document.getElementById('partyList');
      opt = document.createElement('option');
      opt.innerHTML = "...";
      opt.value = 0;      
      sel.appendChild(opt);
      
      var parties = d3.map(data, function(d){return d.COD_PARTIDO +"||"+ d.NOM_PARTIDO;}).keys();
      
      for(var i = 0; i < parties.length; i++) {
        opt = document.createElement('option');
        opt.innerHTML = parties[i].split("||")[1];
        opt.value = parties[i].split("||")[0];
        sel.appendChild(opt);
      } 
     
    });  
    
  }   
  
  // función que carga la lista de candidatos por partido por periodo
  function fillCandidateList(year, partyCode){
  
    d3.json(jsonFileLocation, function(error, data) {
    
      if (error) throw error;    
          
      document.getElementById('candidateList').options.length = 0;
      
      data = data.filter(function(row) {
        return row['PERIODO'] == year;
      });
      
      data = data.filter(function(row) {
        return row['COD_PARTIDO'] == partyCode;
      });
      
      var sel = document.getElementById('candidateList');
      var candidates = d3.map(data.filter(function(d){return d.PERIODO ==  year;}), function(d){return d.NOM_CANDIDATO +"||"+ d.COD_CANDIDATO;}).keys();
      
      var opt = document.createElement('option');
      
      opt.innerHTML = "...";
      opt.value = 0;
      sel.appendChild(opt);
      
      candidates = candidates.sort()
      
      for(var i = 0; i < candidates.length; i++) {
        opt = document.createElement('option');
        opt.innerHTML = candidates[i].split("||")[0];
        opt.value = candidates[i].split("||")[1];
        sel.appendChild(opt);
      } 
     
    });  
    
  } 
  
  // función agregar candidato
  function addName() {
      
    var e = document.getElementById("candidateList");
    var valueToAdd = e.options[e.selectedIndex].value;
    var nameToAdd = e.options[e.selectedIndex].text;

    var e = document.getElementById("yearList");
    var yearToAdd = e.options[e.selectedIndex].text;

    
    if (nameToAdd == "...") {
        return;
    }
    
    var options = document.getElementById("selectNames").options;
    
    for (i=0; i<options.length; i++) {
      if (options[i].value == valueToAdd) { 
        return;
      } 
    }
        
    var sel = document.getElementById("selectNames");
    var opt = document.createElement("option");    
    
    opt.value = valueToAdd;    
    opt.innerHTML = nameToAdd + " ("+yearToAdd+")";
    sel.appendChild(opt);   
    
    pcGraph();
    
  }  
  
  // función retirar candidato
  function removeName() {
  
    var options = document.getElementById("selectNames").options;
    var selectedOptions = [];
    
    for (i=0; i<options.length; i++) {
      if (options[i].selected) { 
        selectedOptions.unshift(i);
      } 
    }
    
    var sel = document.getElementById("selectNames");
    
    for (i=0; i<selectedOptions.length; i++) {
      sel.removeChild(sel[selectedOptions[i]]);
    }
    
    pcGraph();
    
  }
   
  function pcGraph(){
  
    var margin = {top: 50, right: 250, bottom: 10, left: 250},
        width = 1150 - margin.left - margin.right,
        height = 400 - margin.top - margin.bottom,
        innerHeight = height - 2;

    var devicePixelRatio = window.devicePixelRatio || 1;

    var color = d3.scaleOrdinal()
      .range(["#5DA5B3","#D58323","#DD6CA7","#54AF52","#8C92E8","#E15E5A","#725D82","#776327","#50AB84","#954D56","#AB9C27","#517C3F","#9D5130","#357468","#5E9ACF","#C47DCB","#7D9E33","#DB7F85","#BA89AD","#4C6C86","#B59248","#D8597D","#944F7E","#D67D4B","#8F86C2"]);

    var types = {
      "Number": {
        key: "Number",
        coerce: function(d) { return +d; },
        extent: d3.extent,
        within: function(d, extent, dim) { return extent[0] <= dim.scale(d) && dim.scale(d) <= extent[1]; },
        defaultScale: d3.scaleSqrt().range([innerHeight, 0])
      },
      "String": {
        key: "String",
        coerce: String,
        extent: function (data) { return data.sort(); },
        within: function(d, extent, dim) { return extent[0] <= dim.scale(d) && dim.scale(d) <= extent[1]; },
        defaultScale: d3.scalePoint().range([0, innerHeight])
      },
      "Date": {
        key: "Date",
        coerce: function(d) { return new Date(d); },
        extent: d3.extent,
        within: function(d, extent, dim) { return extent[0] <= dim.scale(d) && dim.scale(d) <= extent[1]; },
        defaultScale: d3.scaleTime().range([0, innerHeight])
      }
    };

    var dimensions = [
      {
        key: "NOM_PARTIDO",
        description: "Partido", 
        type: types["String"],
        axis: d3.axisLeft()
          .tickFormat(function(d,i) {
            return d;
          })
      },{
        key: "PERIODO",
        description: "Año", 
        type: types["String"],
      },{
        key: "1",
        description: "Usaquen", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "2",
        description: "Chapinero", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "3",
        description: "Santa Fe", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "4",
        description: "San Cristóbal", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "5",
        description: "Usme", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "6",
        description: "Tunjuelito", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "7",
        description: "Bosa", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "8",
        description: "Kennedy", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "9",
        description: "Fontibón", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "10",
        description: "Engativá", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "11",
        description: "Suba", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "12",
        description: "Barrios Unidos", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "13",
        description: "Teusaquillo", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "14",
        description: "Los Mártires", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "15",
        description: "Antonio Nariño", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "16",
        description: "Puente Aranda", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "17",
        description: "La Candelaria", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "18",
        description: "Rafael Uribe Uribe", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "19",
        description: "Ciudad Bolivar", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "20",
        description: "Sumapaz", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "90",
        description: "Corferias", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "98",
        description: "Penitenciarias", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "TOTAL",
        description: "Total votos", 
        type: types["Number"],
        domain: [0, 50000],
        axis: d3.axisLeft()        
      },{
        key: "NOM_CANDIDATO",
        description: "Candidato", 
        type: types["String"],
        axis: d3.axisRight()
          .tickFormat(function(d,i) {
            return d;
          })
      }
    ];  

    var xscale = d3.scalePoint()
        .domain(d3.range(dimensions.length))
        .range([0, width]);

    var yAxis = d3.axisLeft();
    
    d3.select("#parcoordsdiv").remove();

    var container = d3.select("#pcDiv").append("div")
        .attr("id", "parcoordsdiv")
        .attr("class", "parcoords")
        .style("width", width + margin.left + margin.right + "px")
        .style("height", height + margin.top + margin.bottom + "px"); 

    var backgroundCanvas = container.append("canvas")
        .attr("id", "background")
        .attr("width", width * devicePixelRatio)
        .attr("height", height * devicePixelRatio)
        .style("width", width + "px")
        .style("height", height + "px")
        .style("margin-top", margin.top + "px")
        .style("margin-left", margin.left + "px")
        .attr("transform", "translate(" + margin.left + "," + margin.top + ")");        
        
    var foregroundCanvas = container.append("canvas")
        .attr("id", "foreground")
        .attr("width", width * devicePixelRatio)
        .attr("height", height * devicePixelRatio)
        .style("width", width + "px")
        .style("height", height + "px")
        .style("margin-top", margin.top + "px")
        .style("margin-left", margin.left + "px")
        .attr("transform", "translate(" + margin.left + "," + margin.top + ")");        
        
    var svg = container.append("svg")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom)
      .append("g")
        .attr("transform", "translate(" + margin.left + "," + margin.top + ")");
       
    var background = backgroundCanvas.node().getContext("2d");
    var foreground = foregroundCanvas.node().getContext("2d");
    foreground.globalCompositeOperation = 'darken';
    foreground.globalAlpha = 0.15;
    foreground.lineWidth = 1.5;
    foreground.scale(devicePixelRatio, devicePixelRatio);
        
    var axes = svg.selectAll(".axis")
        .data(dimensions)
      .enter().append("g")
        .attr("class", function(d) { return "axis " + d.key.replace(/ /g, "_"); })
        .attr("transform", function(d,i) { return "translate(" + xscale(i) + ")"; });
        
    d3.json(jsonFileLocation, function(error, data) {
    
      if (error) throw error;
            
      var choices = [ ];
      var opciones = document.getElementById("selectNames").options;

      for (i=0; i<opciones.length; i++) {
        choices.push(opciones[i].value);
      }
      
      data = data.filter(function(row) {
        return choices.includes(row['COD_CANDIDATO']); 
      });
      
      data.forEach(function(d) {
        dimensions.forEach(function(p) {
          d[p.key] = !d[p.key] ? null : p.type.coerce(d[p.key]);
        });
        for (var key in d) {
          if (d[key] && d[key].length > 35) d[key] = d[key].slice(0,36);
        }
      });
      
      dimensions.forEach(function(dim) {
        if (!("domain" in dim)) {
          dim.domain = d3_functor(dim.type.extent)(data.map(function(d) { return d[dim.key]; }));
        }
        if (!("scale" in dim)) {
          dim.scale = dim.type.defaultScale.copy();
        }
        dim.scale.domain(dim.domain);
      });
      
      var render = renderQueue(drawBackground).rate(50);
      render(data);

      render = renderQueue(drawForeground).rate(50);
      foreground.clearRect(0,0,width,height);
      foreground.globalAlpha = d3.min([0.85/Math.pow(data.length,0.3),1]);
      render(data);

      axes.append("g")
          .each(function(d) {
            var renderAxis = "axis" in d
              ? d.axis.scale(d.scale)  // custom axis
              : yAxis.scale(d.scale);  // default axis
            d3.select(this).call(renderAxis);
          })
        .append("text")
          .attr("class", "title")
          .attr("text-anchor", "start")
          .text(function(d) { return "description" in d ? d.description : d.key; });

      axes.append("g")
          .attr("class", "brush")
          .each(function(d) {
            d3.select(this).call(d.brush = d3.brushY()
              .extent([[-10,0], [10,height]])
              .on("start", brushstart)
              .on("brush", brush)
              .on("end", brush)
            )
          })
        .selectAll("rect")
          .attr("x", -8)
          .attr("width", 16);

      d3.selectAll(".axis.NOM_PARTIDO .tick text")
        .style("fill", color); 
      
      function project(d) {
        return dimensions.map(function(p,i) {
          if (
            !(p.key in d) ||
            d[p.key] === null
          ) return null;

          return [xscale(i),p.scale(d[p.key])];
        });
      };
      
      function drawBackground(d) {

        background.strokeStyle = "rgba(0,0,0,0.05)";
        background.beginPath();
        
        var coords = project(d);
        coords.forEach(function(p,i) {
          if (p === null) {
            if (i > 0) {
              var prev = coords[i-1];
              if (prev !== null) {         
                background.moveTo(prev[0],prev[1]);
                background.lineTo(prev[0]+6,prev[1]);
              }
            }
            if (i < coords.length-1) {
              var next = coords[i+1];
              if (next !== null) {
                background.moveTo(next[0]-6,next[1]);
              }
            }
            return;
          }
          if (i == 0) {
            background.moveTo(p[0],p[1]);
            return;
          }
          background.lineTo(p[0],p[1]);
        });
        background.stroke();
      }

      function drawForeground(d) {

        foreground.strokeStyle = color(d.NOM_PARTIDO);
        foreground.beginPath();
        
        var coords = project(d);
        coords.forEach(function(p,i) {
          if (p === null) {
            if (i > 0) {
              var prev = coords[i-1];
              if (prev !== null) {         
                foreground.moveTo(prev[0],prev[1]);
                foreground.lineTo(prev[0]+6,prev[1]);
              }
            }
            if (i < coords.length-1) {
              var next = coords[i+1];
              if (next !== null) {
                foreground.moveTo(next[0]-6,next[1]);
              }
            }
            return;
          }
          if (i == 0) {
            foreground.moveTo(p[0],p[1]);
            return;
          }
          foreground.lineTo(p[0],p[1]);
        });
        foreground.stroke();
      }

      function brushstart() {
        d3.event.sourceEvent.stopPropagation();
      }

      function brush() {
        render.invalidate();

        var actives = [];
        svg.selectAll(".axis .brush")
          .filter(function(d) {
            return d3.brushSelection(this);
          })
          .each(function(d) {
            actives.push({
              dimension: d,
              extent: d3.brushSelection(this)
            });
          });

        var selected = data.filter(function(d) {
          if (actives.every(function(active) {
              var dim = active.dimension;
              return dim.type.within(d[dim.key], active.extent, dim);
            })) {
            return true;
          }
        });

        foreground.clearRect(0,0,width,height);
        foreground.globalAlpha = d3.min([0.85/Math.pow(selected.length,0.3),1]);
        render(selected);

      }      
      
    });      

    function d3_functor(v) {
      return typeof v === "function" ? v : function() { return v; };
    };

  }
    
  fillYearList();
  pcGraph();

</script>

<p align="center" style="font-size: 13px; text-align: center;">
	      Vivian Lucia Aranda Camacho<br>
	      Código: 201022509<br>
	      Universidad de los Andes<br>
	      Maestría en Ingeniería de Información<br>
	      Visual Analytics
	    </p>
