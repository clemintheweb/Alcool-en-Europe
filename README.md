<html>
    <head> 
        <meta charset="utf-8"> 
        <title>Consommation d'alcool en Europe</title> 
        <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" >
        <script type="text/javascript" src="http://ajax.googleapis.com/ajax/libs/jquery/1.7.1/jquery.min.js"></script>
        <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"> </script>
        <script src="https://cdn.jsdelivr.net/npm/sweetalert2@8"></script>
        <script type='text/javascript' src="eurodata2.json"></script>
        <style>
            p {font-size: 0.85em; padding: 1%;}
            #map1 {
                width:100%;
                height:81%;}
            #map1 {
                width: 100%;
                height: 81%;
            }
			body{font-family: 'Roboto';}
            #button-container {
                position: absolute;
                bottom: 10px;
                right: 10px;
                background: transparent; 
                border-radius: 5px;
                padding: 10px;
                z-index: 1000; 
            }
            
            #visu1, #visu2 {
                display: block;
                width: 100%;
                background-color: #fff; 
                color: #000;
                padding: 8px 16px;
                font-size: 14px;
                border: none;
                border-radius: 5px;
                cursor: pointer;
                margin: 5px;
                box-shadow: 2px 2px 5px #888; 
            }
            
            #visu1:hover, #visu2:hover {
                background-color: #e0e0e0; 
            }            
            #header {
            height: 15%;
            max-height: 15%;
            background-color:#810f7c;
            display: flex;
            }
            #header:hover {background-color: #8856a7; }
            #title {width: 90%;margin: auto auto;}
            #maintitle {
                font-size: 1.25em;
                color: white;
                margin-top: auto;
                margin-bottom: auto;
                font-family: "Century Gothic", CenturyGothic, "AppleGothic", sans-serif; 
                text-align: left;
                white-space: nowrap;
                cursor : default; 
                margin-left: 1% ;}
            
            #boutoninformations:hover {
                transform: rotate(720deg);
                transition: transform 2s;
            }
            
            
            .info { padding: 6px 8px; font: 16px/20px Arial, Helvetica, sans-serif; background: white; background: rgba(255,255,255,0.8); box-shadow: 0 0 15px rgba(0,0,0,0.2); border-radius: 5px; } .info h4 { margin: 0 0 5px; color: #777; } 
            .legend { text-align: left; line-height: 20px; color: #555; } .legend i { width: 20px; height: 20px; float: left; margin-right: 8px; opacity: 1; }
}
            
        </style>  
        
        
        
    </head> 
    
    <body>  
        <div id="header">
            <table width="100%">
                <tr>
                    <td width="85%">
                        <div id="title">
                            <p id="maintitle">La consommation d'alcool en Europe<br> Information statistique de la consommation d'alcool par pays en Europe</p>
                        </div>
                    </td>
                    <td width="100%">
                        <div id="boutoninfo">
                            <img style="float: right;" id="boutoninformations" src="https://cdn-icons-png.flaticon.com/512/920/920541.png">
                        </div>
                    </td>
                </tr>
            </table>
        </div>
        
        <div id="map1">
            <div id="button-container">
                <button id="visu1">Consommation moyenne d'alcool</button>
                <button id="visu2">Nombre de décès dûs à la consommation</button>
            </div>
        </div>
           
        <script>
// Géovisulation de la carte  
            
            var map = L.map('map1').setView([55.45, 11.07675],2);
            
// Configuration du Min et Max zoom de la carte 
            L.tileLayer('http://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}.png', {	attribution: 'Map tiles by <a href="basemaps.cartocdn.com">Cartocdn</a>, <a href="http://creativecommons.org/licenses/by/3.0">CC BY 3.0</a> &mdash; Map data &copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors',	subdomains: 'abcd',	minZoom: 3.5,	maxZoom: 7,	ext: 'png'})
            .addTo(map);
            
// Création de la carte de visualisation 1 : conso d'alcool
            
             function color1(d) {
                 return  d > 11.29 ? '#810f7c' :
                 d > 10.12 ? '#8856a7' :
                 d > 8.58 ? '#8c96c6' :
                 d > 7.41 ? '#9ebcda' :
                 d > 6.3 ? '#bfd3e6' :
                 '#edf8fb';
             }
            function leg1(d) {
            return {
                weight: 0.1, 
                opacity: 1, 
                color: 'white',
                dashArray: '3',
                fillOpacity: 1, 
                fillColor:color1(d.properties.conso2019)
            };
              }
             var geojson1 = L.geoJson(eurodata2, {style:leg1}).addTo(map); 
            
// Creation de la Carte visualisation 2 : décès 
            
             function color2(d) {
                 return  d > 21.4  ? '#b30000' :
                 d > 14.8 ? '#de2d26' :
                 d > 4.7 ? '#fb6a4a' :
                 d > 2.2 ? '#fc9272' :
                 d > 1.6 ? '#fcbba1' :
                 '#fee5d9';
             }
             function leg2(d) {
            return {
                weight: 0.1,
                opacity: 1, 
                color: 'white',
                dashArray: '3',
                fillOpacity: 1, 
                fillColor:color2(d.properties.deces)
            };
              }
             var geojson2 = L.geoJson(eurodata2, {style:leg2});  
            

            
//////////////////////////////////////////////////////////////////////////////////
// Ajout d'éléments complémentaire à la lecture de la lecture 
// Ajout d'une échelle
            
			L.control.scale().addTo(map); 
            
// Permet d'ajouter les légendes
            //Légende 1 : conso
            
            var legend1 = L.control({position: 'topleft'}); 
            legend1.onAdd = function (map) {
                var div = L.DomUtil.create('div', 'info legend'),
                    grades = [0, 6.3, 7.41, 8.58,  10.12, 11.29];
                    labels = ["<strong>Consommation d'alcool"+ "<br>" + "(litres/habitants en 2019)" ];
                for (var i = 0; i<grades.length; i++){  
                    var from = grades [i];
                    var to = grades [i + 1];
                    labels.push( '<i style="background:' + color1(grades[i]) + '"></i>' + from + (to ? ' à ' + to: ' et plus')); 
                }
                div.innerHTML = labels.join('<br/><br/>');
                return div;
            }; 
            legend1.addTo(map);

            
            //Légende 2 : décès
            var legend2 = L.control({position: 'topleft'}); 
            legend2.onAdd = function (map) {
                var div = L.DomUtil.create('div', 'info legend'),
                    grades = [0, 1.6, 2.2, 4.7,  14.8, 21.4];
                    labels = ["<strong>Nombre de décès dûs à" + "<br>" + "une consommation excessive" + "<br>" + "(nb décès/100 000 hab en 2019)" ];
                for (var i = 0; i<grades.length; i++){  
                    var from = grades [i];
                    var to = grades [i + 1];
                    labels.push( '<i style="background:' + color2(grades[i]) + '"></i>' + from + (to ? ' à ' + to: ' et plus')); 
                }
                div.innerHTML = labels.join('<br/><br/>');
                return div;
            }; 
          
//////////////////////CREATION PANNEAU STAT ET FONCTIONNALITE ASSOCIEE 
// Panneau statistique de la carte 1 : 
    var info1 = L.control();

info1.onAdd = function (map) {
                this._div = L.DomUtil.create('div', 'info');
                this.update();
                return this._div;
            }; 

info1.update = function (props) {
    this._div.innerHTML =  '<h4>Statistiques nationales</h4>' + (props ?
							'<b>' + props.NAME 
							+ '</b><br><br>' 
							+ 'Consommation d' +"'"+'alcool annuelle moyenne par habitants en 2019: ' + props.conso2019 + ' litres '
							+ '</b><br>' 
							+ 'Indice des prix harmonisés pour l' + "'" + 'alcool  en 2019: ' + props.ipch
							+ '</b><br>'
							+ 'Décès causés par la consommation' 
							+ ' d'+"'"+'alcool pour 100 000 habitants en 2019: ' + props.deces +' décès'
							+ '</b><br>'
							: 'glisser la souris sur un pays' );
};

// Ajout de l'onglet info
info1.addTo(map);
    
// Panneau statistique de la carte 2 : 
    var info2 = L.control();

info2.onAdd = function (map) {
                this._div = L.DomUtil.create('div', 'info');
                this.update();
                return this._div;
            }; 

info2.update = function (props) {
    this._div.innerHTML =      this._div.innerHTML =  '<h4>Statistiques nationales</h4>' + (props ?
							'<b>' + props.NAME 
							+ '</b><br><br>' 
							+ 'Consommation d' +"'"+'alcool annuelle moyenne par habitants en 2019: ' + props.conso2019 + ' litres '
							+ '</b><br>' 
							+ 'Indice des prix harmonisés pour l' + "'" + 'alcool  en 2019: ' + props.ipch
							+ '</b><br>'
							+ 'Décès causés par la consommation' 
							+ ' d'+"'"+'alcool pour 100 000 habitants en 2019: ' + props.deces +' décès'
							+ '</b><br>'
							: 'glisser la souris sur un pays' );
};
    
// Affichage des unités spatiales au survol de la Carte 1
            
           function highlightA(x) {
                var layer = x.target;
                layer.setStyle({
                    weight: 3,
                    color: '#111',
                    dashArray: '',
                    fillOpacity: 0.7
                });

                if (!L.Browser.ie && !L.Browser.opera && !L.Browser.edge) {
                    layer.bringToFront();
                }


                info1.update(layer.feature.properties);
            }
            function resetA(x) {
                geojson1.resetStyle(x.target);
                info1.update();
            } 
            
            // Fonction de zoom sur une unité spatiale 
            
            function zoomA(x) {
                map.fitBounds(x.target.getBounds());
            }
            
            // Configuration du déclenchement des fonctions précédentes selon les évèvements survol et clic 
            
            function onEachFA(feature, layer) {
                layer.on({
                    mouseover: highlightA,
                    mouseout: resetA,
                    click: zoomA
                });
            }
            
            // Appel au fichier json et ajout à la carte 
            
            geojson1 = L.geoJson(eurodata2, {
                style: leg1,
                onEachFeature: onEachFA
            }).addTo(map);
    
// Affichage des unités spatiales au survol de la Carte 2
            
           function highlightB(y) {
                var layer = y.target;
                layer.setStyle({
                    weight: 3,
                    color: '#111',
                    dashArray: '',
                    fillOpacity: 0.7
                });
                if (!L.Browser.ie && !L.Browser.opera && !L.Browser.edge) {
                    layer.bringToFront();
                }
                info2.update(layer.feature.properties);
            }
            function resetB(y) {
                geojson2.resetStyle(y.target);
                info2.update();
            } 
            
            // Fonction de zoom sur une unité spatiale 
            
            function zoomB(y) {
                map.fitBounds(y.target.getBounds());
            }
            
            // Configuration du déclenchement des fonctions précédentes selon les évèvements survol et clic 
            function onEachFB(feature, layer) {
                layer.on({
                    mouseover: highlightB,
                    mouseout: resetB,
                    click: zoomB
                });
            }
            
            // Appel au fichier json et ajout à la carte 
            
            geojson2 = L.geoJson(eurodata2, {
                style: leg2,
                onEachFeature: onEachFB
            });

            
// Configuration des pop up de chaque pays 

////////////////////Popup par pays

var customOptions = {'maxWidth': '500', 'minWidth': '350', 'className' : 'custom'}	
var PopupAut = "<img src='Autriche.png' width='400px' class='center' />";
var Autriche = L.marker([  48.20, 16.37]).bindPopup(PopupAut,customOptions)
.addTo(map);
	
var customOptions = {'maxWidth': '500', 'minWidth': '350', 'className' : 'custom'}	
var PopupBel = "<img src='Belgique.png' width='400px' class='center' />";
var Belgique = L.marker([  50.84, 4.35]).bindPopup(PopupBel,customOptions)
.addTo(map);
	
var customOptions = {'maxWidth': '500', 'minWidth': '350', 'className' : 'custom'}	
var PopupCro = "<img src='Croatie.png' width='400px' class='center' />";
var Croatie = L.marker([  45.81, 15.97]).bindPopup(PopupCro,customOptions)
.addTo(map);

var customOptions = {'maxWidth': '500', 'minWidth': '350', 'className' : 'custom'}	
var PopupRep = "<img src='R‚publique Tcheque.png' width='400px' class='center' />";
var Republique = L.marker([  50.07, 14.43]).bindPopup(PopupRep,customOptions)
.addTo(map);

var customOptions = {'maxWidth': '500', 'minWidth': '350', 'className' : 'custom'}	
var PopupDan = "<img src='Danemark.png' width='400px' class='center' />";
var Danemark = L.marker([  55.67, 12.56]).bindPopup(PopupDan,customOptions)
.addTo(map);

var customOptions = {'maxWidth': '500', 'minWidth': '350', 'className' : 'custom'}	
var PopupEst = "<img src='Estonie.png' width='400px' class='center' />";
var Estonie = L.marker([  59.43, 24.75]).bindPopup(PopupEst,customOptions)
.addTo(map);

var customOptions = {'maxWidth': '500', 'minWidth': '350', 'className' : 'custom'}	
var PopupFin = "<img src='Finlande.png' width='400px' class='center' />";
var Finlande = L.marker([   60.19, 24.94]).bindPopup(PopupFin,customOptions)
.addTo(map);

var customOptions = {'maxWidth': '500', 'minWidth': '350', 'className' : 'custom'}	
var PopupFra = "<img src='France.png' width='400px' class='center' />";
var France = L.marker([   48.86, 2.33]).bindPopup(PopupFra,customOptions)
.addTo(map);

var customOptions = {'maxWidth': '500', 'minWidth': '350', 'className' : 'custom'}	
var PopupAll = "<img src='Allemagne.png' width='400px' class='center' />";
var Allemagne = L.marker([   52.52, 13.40]).bindPopup(PopupAll,customOptions)
.addTo(map);

var customOptions = {'maxWidth': '500', 'minWidth': '350', 'className' : 'custom'}	
var PopupGre = "<img src='Grece.png' width='400px' class='center' />";
var Grece = L.marker([   37.94, 23.67]).bindPopup(PopupGre,customOptions)
.addTo(map);

var customOptions = {'maxWidth': '500', 'minWidth': '350', 'className' : 'custom'}	
var PopupHon = "<img src='Hongrie.png' width='400px' class='center' />";
var Hongrie = L.marker([   47.49, 19.04]).bindPopup(PopupHon,customOptions)
.addTo(map);

var customOptions = {'maxWidth': '500', 'minWidth': '350', 'className' : 'custom'}	
var PopupIrl = "<img src='Irlande.png' width='400px' class='center' />";
var Irlande = L.marker([   53.34, -6.26]).bindPopup(PopupIrl,customOptions)
.addTo(map);

var customOptions = {'maxWidth': '500', 'minWidth': '350', 'className' : 'custom'}	
var PopupIta = "<img src='Italie.png' width='400px' class='center' />";
var Italie = L.marker([   41.89, 12.48]).bindPopup(PopupIta,customOptions)
.addTo(map);

var customOptions = {'maxWidth': '500', 'minWidth': '350', 'className' : 'custom'}	
var PopupLux = "<img src='Luxembourg.png' width='400px' class='center' />";
var Luxembourg = L.marker([   49.81,  6.13]).bindPopup(PopupLux,customOptions)
.addTo(map);

var customOptions = {'maxWidth': '500', 'minWidth': '350', 'className' : 'custom'}	
var PopupPB = "<img src='Pays-Bas.png' width='400px' class='center' />";
var PaysBas = L.marker([   52.37, 4.89]).bindPopup(PopupPB,customOptions)
.addTo(map);

var customOptions = {'maxWidth': '500', 'minWidth': '350', 'className' : 'custom'}	
var PopupPol = "<img src='Pologne.png' width='400px' class='center' />";
var Pologne = L.marker([   52.23, 21.01]).bindPopup(PopupPol,customOptions)
.addTo(map);

var customOptions = {'maxWidth': '500', 'minWidth': '350', 'className' : 'custom'}	
var PopupPor = "<img src='Portugal.png' width='400px' class='center' />";
var Portugal = L.marker([   38.70, -9.13]).bindPopup(PopupPor,customOptions)
.addTo(map);

var customOptions = {'maxWidth': '500', 'minWidth': '350', 'className' : 'custom'}	
var PopupSlovaquie = "<img src='Slovaquie.png' width='400px' class='center' />";
var Slovaquie = L.marker([   48.14, 17.10]).bindPopup(PopupSlovaquie,customOptions)
.addTo(map);

var customOptions = {'maxWidth': '500', 'minWidth': '350', 'className' : 'custom'}	
var PopupEsp = "<img src='Espagne.png' width='400px' class='center' />";
var Espagne = L.marker([   40.41, -3.70]).bindPopup(PopupEsp,customOptions)
.addTo(map);

var customOptions = {'maxWidth': '500', 'minWidth': '350', 'className' : 'custom'}	
var PopupSlovenie = "<img src='Slov‚nie.png' width='400px' class='center' />";
var Slovenie = L.marker([   46.04, 14.50]).bindPopup(PopupSlovenie,customOptions)
.addTo(map);

var customOptions = {'maxWidth': '500', 'minWidth': '350', 'className' : 'custom'}	
var PopupSue = "<img src='Suede.png' width='400px' class='center' />";
var Suede = L.marker([   59.32, 18.07]).bindPopup(PopupSue,customOptions)
.addTo(map);

var customOptions = {'maxWidth': '500', 'minWidth': '350', 'className' : 'custom'}	
var PopupBul = "<img src='Bulgarie.png' width='400px' class='center' />";
var Bulgarie = L.marker([   42.69, 23.31]).bindPopup(PopupBul,customOptions)
.addTo(map);

var customOptions = {'maxWidth': '500', 'minWidth': '350', 'className' : 'custom'}	
var PopupRou = "<img src='Roumanie.png' width='400px' class='center' />";
var Roumanie = L.marker([   44.42, 26.10]).bindPopup(PopupRou,customOptions)
.addTo(map);
            
//Ajout des boutons pour sélectionner les cartes à géovisualiser
            
             $("#visu1").click(function(event) {
                 map.removeLayer(geojson2).removeControl(legend2).removeControl(info2);
                 map.addLayer(geojson1).addControl(legend1).addControl(info1);
             }); 
             $("#visu2").click(function(event) {
                 map.removeLayer(geojson1).removeControl(legend1).removeControl(info1);
                 map.addLayer(geojson2).addControl(legend2).addControl(info2);
             });
            

            
// Configuration du bouton d'information 
  $('#boutoninfo img').on("click", function(){
                Swal.fire({
                    title:'Visualiser la consommation d&rsquo;alcool en Europe ',
                    html:'Cette application permet de visualiser sous différentes approches la consommation d&rsquo;alcool en Europe.'
					+ '</b><br>'
					+ '</b><br>'
					+ '- La consommation annuelle moyenne par habitant &acirc;gé de 15 ans ou plus, en 2019 / de 1995 à 2019. Source : Organisation de la Coopération et de Développement &Eacute;conomique.'
					+ '</b><br>'
					+ '</b><br>'
					+ '- Le nombre de décès pour 100 000 habitants en 2019. Source : Commission européenne par Eurostat.'
					+ '</b><br>'
					+ '</b><br>'
					+ '- L'+"'"+'indice des prix à la consommation harmonisés ou IPCH. Source : Commission européenne par Eurostat.'
					+ '</b><br>'
					+ '</b><br>'
					+ 'Réalisation : Mr KUMM, HEINTZ <br>dans le cadre du CSC1 d&rsquo;information géographique, Master 1 OTG</i>  '
					  });
});
// Configuration du tutoriel à l'ouverture du site   
    Swal.mixin({
  
	
  confirmButtonText: '&#201;tape suivante &rarr;',
  showCancelButton: true,
  progressSteps: ['1', '2', '3', '4',],
	reverseButtons: true,
	cancelButtonText: 'Quitter le tutoriel'
	
}).queue([
  {
    title: 'Tutoriel de l&rsquo;application',
   html: "Cette application permet de visualiser différentes statistiques sur la consommation d'alcool en Europe",
  },
  {title:'Lecture du document', html: 'Déplacement possible dans le document, zoom, etc. ',},
  {title:'Pour en savoir plus sur un pays', html: 'Au survol, des informations supplémentaires apparaissent en haut à droite dans le panneau statistiques locales.' 
  +'</b><br><br>'
  +'Au clic sur les capitales, apparait l'+"'"+'&eacute;volution de la consommation moyenne d'+"'"+'alcool dans le temps.'},
{title:'Application réalisée par Clément HEINTZ, Anthonin KUMM', html: 'CSC1 - Information géographique - M1 OTG',},
]).then((result) => {
  if (result.value) {
    Swal.fire({
      title: 'Vous savez tout',
      html:
        'Pour plus d&rsquo;information faite le plein au bar en haut à droite de l&rsquo;écran',
      confirmButtonText: 'Santé !'
    })
  }
})		
            
      
  $("#boutoninformations").width($(window).width() / 23);
  window.addEventListener("resize", function() {
    $("#boutoninformations").width($(window).width() / 23);
  });

        </script>
    </body>
</html>
