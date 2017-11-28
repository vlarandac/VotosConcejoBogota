# Accidentalidad en Bogotá en el año 2016 

### Definición de Accidentes de Tránsito

Accidentes de Tránsito: Definiciones de la Ley 769 de 2002 – Código Nacional de Tránsito
– “Evento generalmente involuntario, generado al menos por un vehículo en movimiento, que causa daños a personas y bienes involucrados en él e igualmente afecta la normal circulación de los vehículos que se movilizan por la vía o vías comprendidas en el lugar o dentro de la zona de influencia del hecho.”

### ¿Qué hay en este Conjunto de Datos?

#### [ACCIDENTES BOGOTÁ 2016](https://www.datos.gov.co/Transporte/2016-ACCIDENTES-DE-TR-NSITO-BOGOT-/79fi-zm8c)

El datasset elegido esta compuesto por 34931 Filas y 38 Columnas que presentan la información de todos los accidentes registrados en la ciudad de Bogotá durante el año 2016.  Se presentan caracterizaciones del tipo de accidente, gravedad, día y hora de ocurrencia, localidad y otros atributos de interes frente a los datos. 

La fecha de carga del dataset fue el 11 de mayo de 2017 y fué la única carga que se hizo.

### Lo que se espera lograr:
○ Comparar los días en los cuales se presentaron más accidentes durante el año 2016 en la ciudad de Bogotá.<br/>
○ Presentar cuáles fueron las localidades de Bogotá con mayor cantidad de accidentes para los distintos meses del año 2016.
<br/><br/>
Hipotesis 1: Las localidades con mayor canidad de accidentes son las que mayor población residente tienen.<br/><br/>
Hipotesis 2: Existen meses del año y días en los cuales se presentan mayor cantidad de accidentes.

### Análisis e Insights
         	 
<!DOCTYPE html>
<title>Parallel Coordinates - Consejo de Bogotá</title>
<head>
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
        description: "Zona 1", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "2",
        description: "Zona 2", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "3",
        description: "Zona 3", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "4",
        description: "Zona 4", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "5",
        description: "Zona 5", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "6",
        description: "Zona 6", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "7",
        description: "Zona 7", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "8",
        description: "Zona 8", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "9",
        description: "Zona 9", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "10",
        description: "Zona 10", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "11",
        description: "Zona 11", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "12",
        description: "Zona 12", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "13",
        description: "Zona 13", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "14",
        description: "Zona 14", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "15",
        description: "Zona 15", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "16",
        description: "Zona 16", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "17",
        description: "Zona 17", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "18",
        description: "Zona 18", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "19",
        description: "Zona 19", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "20",
        description: "Zona 20", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "90",
        description: "Zona 90", 
        type: types["Number"],
        domain: [0, 10000]
      },{
        key: "98",
        description: "Zona 98", 
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

Podemos observar que para el año 2016 existe una clara evidencia de cuáles fueron las localidades con mayor y menor cantidad de accidentes reportados, así vemos que la localidad con menos cantidad de accidentes reportados es la localidad de La Candelaria, la cual presenta por gran diferencia muchos menos accidentes que las demás localidades, y es un comportamiento que se mantiene a lo largo de todo el año.   Así mismo se puede ver que la localidad que mayor cantidad de accidentes reportó fue la localidad de Kennedy, que junto con la localidad de Suba y Engativa reportaron una cifra cercana a los 220 accidentes al iniciar el año, y posteriormente presentaron un incremento más notorio en los meses de mayo y noviembre para Kennedy, y en abril, julio y octubre para Engativa.   

De acuerdo con nuestra gráfica podemos decir que varias de las localidades permanecieron con una cifra entre los 50 y 100 accidentes mensuales a lo largo del año, como es el caso de Tunjuelito, Rafael Uribe Uribe, Santa Fe, San Cristobal, Antonio Nariño y Usme. Y localidades como Fontibón, Puente Aranda y Chapinero fluctúan entre los 100 y 250 accidentes mensuales a lo largo del año. 

De igual manera podemos ver en la siguiente gráfica cómo estuvieron distribuidos los accidentes a los largo del año en toda la ciudad, y al igual que en la gráfica anterior es de notar que hacia el final de año, en los meses de octubre, noviembre y diciembre se presentaron la mayor cantidad de accidentes. 

De acuerdo con la Secretaría Distrital de Planeación en su Boletin 69 de Proyecciones de población por Localidades emitido en diciembre de 2014, las tres localidades con mayor población proyectada para 2016 fueron Suba(1.250.734), Kennedy (1.187.315) y Engativa (873.243), lo cual nos permite validar nuestra hipótesis de que las localidades con mayor población son las que reportan mayor cantidad de accidentes. 
<br/><br/>
Vivian Lucia Aranda<br/>
201022509<br/>
ISIS4822 – Visual Analytics - 20172	<br/>		
Universidad de los Andes, Bogotá, Colombia
