<template>
  yay
  <div id='mapContainer'></div>
</template>

<script>
import 'leaflet/dist/leaflet.css';
import L from 'leaflet';
import countries from '../assets/countries.geo.json';

const language = 'pt';

let geojson;
let map;
let toooltip;
let active;


function tooltip(layer) {
    let Center = layer.getBounds().getCenter();
    switch (layer.feature.properties.name) {
        case 'Russia':
            Center = [60, 90];
            break;
        case 'France':
            Center = [47, 1];
            break;
    }
    return toooltip = L.tooltip({ className: 'info' })
        .setContent('<strong>' + layer.feature.properties.name_pt + '</strong>')
        .setLatLng(Center);
}

function zoomToFeature(e) {
    const layer = e.target;
    let _name = layer.feature.properties.name;

    if (active === '' || active !== _name) {
        map.fitBounds(e.target.getBounds());
        active = _name;
        this.submit;

    } else {
        map.zoomOut(20);
        active = '';
    }
}

function highlightFeature(e) {
    let layer = e.target;
    layer.setStyle({
        weight: 5,
        color: '#666',
        dashArray: '',
        fillOpacity: 0.7,
    });
    tooltip(layer).openOn(map);

    layer.bringToFront();
}

function resetHighlight(e) {
    geojson.resetStyle(e.target);
    toooltip.remove();
}

function onEachFeature(feature, layer) {
    layer.on({
        mouseover: highlightFeature,
        mouseout: resetHighlight,
        click: zoomToFeature,
    });
}

function style(feature) {
    return {
        weight: 2,
        opacity: 1,
        color: 'black',
        fill: true,
        fillColor: '#fafafd',
        dashArray: '3',
        fillOpacity: 0.7,
    };
}


export default {
    name: 'LeafletMap',
    emits: [active],
    methods: {
        submit() {
            console.log(active);
            this.$emit('onGetLocale', active);
        },
    },
    data() {
        return {
            map: null,
        };
    },
    mounted() {
        this.map = map = L.map('mapContainer', {
            maxZoom: 6,
            minZoom: 4,
        }).setView([46.05, 11.05], 5);

        L.tileLayer('http://{s}.tile.osm.org/{z}/{x}/{y}.png', {
            attribution:
                '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
        }).addTo(this.map);
        geojson = L.geoJson(countries, {
            style: style,
            onEachFeature: onEachFeature,
        }).addTo(this.map);
    },

};
</script>

<style>
#mapContainer {
    position: absolute;
    left: 0;
    top: 0;
    width: 100vw;
    height: 100vh;
}

.info {
    padding: 6px 8px;
    position: absolute;
    height: 30px;
    min-width: 40px;
    font: 14px/16px Arial, Helvetica, sans-serif;
    background: rgba(0, 0, 0, 0.8);
    box-shadow: 0 0 15px rgba(0, 0, 0, 0.2);
    border-radius: 5px;
}

.info, .info h4 {
    margin: 0 0 5px;
    font-size: 1.5rem;
    padding: 18px;
    color: white;
}
</style>