<template>
    <div>
      <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css">
      <!--Creates 5 maps that each represent a different metric, can add more as needed-->
      <input type="radio" id="map1" value="map1" v-model="selectedMap">
      <i class="fa fa-gears"></i>
      <label for="map1">  Mental Health Index     </label>
      <input type="radio" id="map2" value="map2" v-model="selectedMap">
      <i class="fa fa-cutlery"></i>
      <label for="map2">  Food      </label>
      <input type="radio" id="map3" value="map3" v-model="selectedMap">
      <i class="fa fa-dollar"></i>
      <label for="map3">  Economic Stability      </label>
      <input type="radio" id="map4" value="map4" v-model="selectedMap">
      <i class="fa fa-group"></i>
      <label for="map4">  Community and Social Context Stability      </label>
      <input type="radio" id="map5" value="map5" v-model="selectedMap">
      <i class="fa fa-pencil"></i>
      <label for="map5">  Education</label>
      <!--If new map is made, copy l-map and l-tile-layers, but other layers will be different depending on data file used-->
      <div v-if="selectedMap === 'map1'" id="app" style="text-align: left;">
        <l-map :center="[33.7175, -117.8311]" :zoom="10" style="height: 700px;" :options="mapOptions">
          <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
          <!--data links to csv/js data file, titleKey and idKey identify specific column of data
              value links to specific data value/metric in the data file, geojsonIdKey identifies geojson file
              for choropleth layer, geojson is file name itself-->
          <l-choropleth-layer :data="weightedOCData" titleKey="City" idKey="zcta" :value="ocValue" :extraValues="ocExtraValues" geojsonIdKey="dpto" :geojson="ocGeojson" :colorScale="colorScale">
            <template slot-scope="info">
                <!--info control is the pop up box on the bottom left; item is the numerical data value of the
                    current area that is being hovered over, unit is the metric of the data value, for example
                    MHI map displays Mental Health Index, Economic Map is in percentages, etc.
                    title is the light grey text that appears at the top indicating the title,
                    placeholder is placeholder text that appears when not hovering over an area-->
                <l-info-control :item="info.currentItem" :unit="info.unit" title="City" placeholder="Hover over a city"/>
                <!--reference chart is the box at the top right of the page, showing the map's explanation and scale
                    title is the description/title of the current map
                    colorscale is the scale of colors, by default a green-yellow-red scale
                    min and max can be copied as they are shown to have the program set
                    default values for the min and max, which will be the min and max found in the
                    data respectively-->
              <l-reference-chart title="Mental Health Index Averages by Zip Code" :colorScale="colorScale" :min="info.min" :max="info.max" position="topright"/>
            </template>
          </l-choropleth-layer>
        </l-map>
      </div>
    <div>
      <div v-if="selectedMap === 'map2'" style="text-align: left;">
        <l-map :center="[33.7175, -117.8311]" :zoom="10" style="height: 700px;" :options="mapOptions">
          <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
              <l-choropleth-layer :data="weightedOCData" titleKey="City" idKey="zcta" :value="wfoodValue" :extraValues="wfoodExtraValues" geojsonIdKey="dpto" :geojson="ocGeojson" :colorScale="colorScale" @mouseover="onHover">
                <template slot-scope="info">
                  <l-info-control :item="info.currentItem" :unit="info.unit" title="City" placeholder="Hover over a zip code" @mouseover="onHover"/>
                  <l-reference-chart title="Average Percentage of Households under the poverty line not receiving Food Stamps by Zip Code" :colorScale="colorScale" :min="info.min" :max="info.max" position="topright"/>
              </template>
              </l-choropleth-layer>
        </l-map>
      </div>
    </div>
    <div>
      <div v-if="selectedMap === 'map3'" style="text-align: left;">
        <l-map :center="[33.7175, -117.8311]" :zoom="10" style="height: 700px;" :options="mapOptions">
          <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
          <l-choropleth-layer :data="weightedOCData" titleKey="City" idKey="zcta" :value="weconValue" :extraValues="weconExtraValues" geojsonIdKey="dpto" :geojson="ocGeojson" :colorScale="colorScale">
            <template slot-scope="info">
              <l-info-control :item="info.currentItem" :unit="info.unit" title="City" placeholder="Hover over a zip code"/>
              <l-reference-chart title="Average Percentage of Unemployment (Ages 16+) by Zip Code" :colorScale="colorScale" :min="info.min" :max="info.max" position="topright"/>
            </template>
          </l-choropleth-layer>
        </l-map>
      </div>
    </div>
    <div>
      <div v-if="selectedMap === 'map4'" style="text-align: left;">
        <l-map :center="[33.7175, -117.8311]" :zoom="10" style="height: 700px;" :options="mapOptions">
          <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
          <l-choropleth-layer :data="weightedOCData" titleKey="City" idKey="zcta" :value="wcomValue" :extraValues="wcomExtraValues" geojsonIdKey="dpto" :geojson="ocGeojson" :colorScale="colorScale">
            <template slot-scope="info">
              <l-info-control :item="info.currentItem" :unit="info.unit" title="City" placeholder="Hover over a zip code"/>
              <l-reference-chart title="Average Distance to the nearest Health Clinic by Zip Code" :colorScale="colorScale" :min="info.min" :max="info.max" position="topright"/>
            </template>
          </l-choropleth-layer>
        </l-map>
      </div>
    </div>
    <div>
      <div v-if="selectedMap === 'map5'" style="text-align: left;">
        <l-map :center="[33.7175, -117.8311]" :zoom="10" style="height: 700px;" :options="mapOptions">
          <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
          <l-choropleth-layer :data="weightedOCData" titleKey="City" idKey="zcta" :value="weduValue" :extraValues="weduExtraValues" geojsonIdKey="dpto" :geojson="ocGeojson" :colorScale="colorScale">
            <template slot-scope="info">
              <l-info-control :item="info.currentItem" :unit="info.unit" title="City" placeholder="Hover over a zip code"/>
              <l-reference-chart title="Average Percent of People (Ages 16-19) Unemployed and Not in School by Zip Code" :colorScale="colorScale" :min="info.min" :max="info.max" position="topright"/>
            </template>
          </l-choropleth-layer>
        </l-map>
      </div>
    </div>
  <div style="overflow-x:auto;">
    <h1>The Social Determinants of Health</h1>
      <p>Social Determinants of Health are conditions in people's everyday environment which includes non-medical factors such as socioeconomic status, neighborhood environment, etc. </p>
      <table class="info-container">
        <tbody class="info-body">
          <tr class="info-row">
            <td><h3>Economic Stability</h3></td>
            <td><h3>Neighborhood and Physical Environment</h3></td>
            <td><h3>Education</h3></td>
            <td><h3>Food</h3></td>
            <td><h3>Community and Social Context</h3></td>
            <td><h3>Health Care System</h3></td>
          </tr>
          <tr class="info-row">
            <td>
              <div class="economic-info">
                <ul>
                  <li><b>Employment</b></li>
                  <li>Income</li>
                  <li>Expenses</li>
                  <li>Debt</li>
                  <li>Medical Bills</li>
                  <li>Support</li>
                </ul>
              </div>
            </td>
            <td class="economic-container">
              <div class="economic-info">
                <ul>
                   <li>Housing</li>
                   <li><b>Transportation</b></li>
                   <li>Safety</li>
                   <li>Parks</li>
                   <li>Playgrounds</li>
                   <li>Walkability</li>
                   <li>Zip Code / Geography</li>
                </ul>
              </div>
            </td>
            <td class="economic-container">
              <div class="economic-info">
                <ul>
                  <li>Literacy</li>
                  <li>Language</li>
                  <li>Early Childhood Education</li>
                  <li>Vocational Training</li>
                  <li><b>Higher Education</b></li>
                </ul>
              </div>
            </td>
            <td class="economic-container">
              <div class="economic-info">
                <ul>
                  <li><b>Hunger</b></li>
                  <li>Access to Healthy Options</li>
                </ul>
              </div>
            </td>
            <td class="economic-container">
              <div class="economic-info">
                <ul>
                  <li>Social Integration</li>
                  <li><b>Support Systems</b></li>
                  <li>Community Engagement</li>
                  <li>Discrimination</li>
                  <li>Stress</li>
                </ul>
              </div>
            </td>
            <td class="economic-container">
              <div class="economic-info">
                <ul>
                  <li><b>Health Coverage</b></li>
                  <li>Provider Availability</li>
                  <li>Provider Linguistic and Cultural Competency</li>
                  <li>Quality of Care</li>
                </ul>
              </div>
            </td>
          </tr>
        </tbody>
      </table>
    <p><i>* Data is weighted according to the population of each Zip Code Tabulation Area (ZCTA)</i></p>
    <h1>About the Map</h1>
    <p style="text-align: left; padding-left: 10px;">Our map visually portrays the Mental Health Index which takes into account: </p>
    <p style="text-align: left; padding-left: 20px;"><b>Mental Health Index: </b>A measure of socioeconomic and health factors correlated with self-reported poor mental health.</p>
    <p style="text-align: left; padding-left: 20px;"><b>Economic Stability: </b>In the United States, 1 in 10 people live in poverty, and many people can't afford things like healthy foods, health care, and housing.</p>
    <p style="text-align: left; padding-left: 20px;"><b>Health Care and Quality: </b>Many people in the United States don't get the health care services they need.</p>
    <p style="text-align: left; padding-left: 20px;"><b>Social and Community Context: </b>People's relationships and interactions with family, friends, co-workers, and community members can have a major impact on their health and well-being.</p>
    <p style="text-align: left; padding-left: 20px;"><b>Neighborhood and Environment: </b>The neighborhoods people live in have a major impact on their health and well-being.</p>
    <p style="text-align: left; padding-left: 20px;"><b>Education and Access Quality: </b>People with higher levels of education are more likely to be healthier and live longer.</p>
    <p style="text-align: left; padding-left: 10px;">The higher the Mental Index Score, the worse.</p>

  </div>
  </div>
  </template>
  
  <script>
  /* must import vue components, as well as any data and geojson files needed*/

  import { InfoControl, ReferenceChart, ChoroplethLayer } from 'vue-choropleth'
  //import { geojson } from './data/py-departments-geojson'
  import ocGeojson from '../data/ocGeoZipCode.json'
  import { weightedOCData } from '../data/weighted_data_city'
  import { LMap, LTileLayer, LGeoJson } from 'vue2-leaflet'
 
  export default {
    name: "app",
    components: { 
      LMap,
      LTileLayer,
      LGeoJson,
      'l-info-control': InfoControl, 
      'l-reference-chart': ReferenceChart, 
      'l-choropleth-layer': ChoroplethLayer,
    },
    data() {
        return {
          /* assign values to all variables that are used above in the map parts, if using OpenStreetMap,
             copy the values for url and attribution to new map */
          url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
          position: 'topright',
          attribution: '&copy; <a target="_blank" href="http://osm.org/copyright">OpenStreetMap</a> contributors',
          weightedOCData,
          ocGeojson,  
          selectedMap: 'map1',
          mhiColorScale: ['#e34a33', '#fdbb84','#fee8c8'],
          colorScale: ['#fee8c8','#fdbb84','#e34a33'],
          /* each map has its own value that they represent, if making a new map, create a new
             variable as shown here and assign it a key (the data you want to display from the data file),
             and a metric to represent the data in */
        ocValue: {
          key: "w_mhi_avg",
          metric: " - <FONT COLOR=#FE7F9C><b>Weighted Mental Health Index Average</FONT COLOR></b>"
        },
        ocExtraValues: [
          {
            key: "w_unemployed",
            metric: "% Unemployed - <b>Econ</b>"
          },
          {
            key: "w_no_food_stamps",
            metric: "% eligible families with no foodstamps - <b>Food</b>"
          },
          {
            key: "distance_clinic",
            metric: "miles to nearest health clinic - <b>Community</b>"
          },
          {
            key: "w_no_school_job",
            metric: "% of people ages 16-19 unemployed and not in school - <b>Education</b>"
          },
          {
            key: "zcta_pop",
            metric: "Population of Zip Code"
          }
        ],
        wfoodValue: {
          key: "w_no_food_stamps",
          metric: "% eligible families with no foodstamps - <FONT COLOR=#960018><b>Food</FONT COLOR></b>"
        },
        wfoodExtraValues: [
          {
            key: "w_unemployed",
            metric: "% Unemployed - <b>Econ</b>"
          },
          {
            key: "distance_clinic",
            metric: "miles to nearest health clinic - <b>Community</b>"
          },
          {
            key: "w_no_school_job",
            metric: "% of people ages 16-19 unemployed and not in school - <b>Education</b>"
          },
          {
            key: "w_mhi_avg",
            metric: "<b>Weighted Mental Health Index Average</b>"
          },
          {
            key: "zcta_pop",
            metric: "Population of Zip Code"
          }
        ],
        weconValue: {
          key: "w_unemployed",
          metric: "% of people unemployed - <FONT COLOR=#80800><b>Econ</b></FONT COLOR>"
        },
        weconExtraValues: [
          {
            key: "w_no_food_stamps",
            metric: "% eligible families with no foodstamps - <b>Food</b>"
          },
          {
            key: "distance_clinic",
            metric: "miles to nearest health clinic - <b>Community</b>"
          },
          {
            key: "w_no_school_job",
            metric: "% of people ages 16-19 unemployed and not in school - <b>Education</b>"
          },
          {
            key: "w_mhi_avg",
            metric: "<b>Weighted Mental Health Index Average</b>"
          },
          {
            key: "zcta_pop",
            metric: "Population of Zip Code"
          }
        ],
        wcomValue: {
          key: "distance_clinic",
          metric: "miles to nearest health clinic - <FONT COLOR=#008080><b>Community</b></FONT COLOR>"
        },
        wcomExtraValues: [
          {
            key: "w_no_food_stamps",
            metric: "% eligible families with no foodstamps <b>Food</b>"
          },
          {
            key: "w_unemployed",
            metric: "% of people unemployed - <b>Econ</b>"
          },
          {
            key: "w_no_school_job",
            metric: "% of people ages 16-19 unemployed and not in school - <b>Education</b>"
          },
          {
            key: "w_mhi_avg",
            metric: "<b>Weighted Mental Health Index Average</b>"
          },
          {
            key: "zcta_pop",
            metric: "Population of Zip Code"
          }
        ],
        weduValue: {
          key: "w_no_school_job",
          metric: "% of people ages 16-19 unemployed and not in school - <FONT COLOR=purple><b>Education</b></FONT COLOR>"
        },
        weduExtraValues: [
          {
            key: "w_no_food_stamps",
            metric: "% eligible families with no foodstamps - <b>Food</b>"
          },
          {
            key: "w_unemployed",
            metric: "% of people unemployed - <b>Econ</b>"
          },
          {
            key: "distance_clinic",
              metric: "miles to nearest health clinic - <b>Community</b>"
          },
          {
            key: "w_mhi_avg",
            metric: "<b>Weighted Mental Health Index Average</b>"
          },
          {
            key: "zcta_pop",
            metric: "Population of Zip Code"
          }
        ],
          /* if using OpenStreetMap, leave as true to give credit */
          mapOptions: {
            attributionControl: true
          },
          currentStrokeColor: '3d3213'
        }
      
    },
    methods: {
      onChoroplethLayerClick(event) {
        console.log('Choropleth layer clicked!', event);
        onClick();
      },
      onClick(){
        console.log("joe");
      },
      onHover(event) {
      console.log('joe')
    }
    }
  }
  </script>
  <style>
  body {
    background-color: #e7d090;
    margin-left: 0px;
    margin-right: 0px;
  }
  #map {
    background-color: rgb(0, 0, 0);
  }
  .info-control-wrapper {
    background-color: blueviolet;
  }
  template {
    text-align: left;
  }
  ul {
    list-style-type: none;
    padding-left:0;
  }
  table {
    width: 100%;
    border-collapse:collapse;
    border: 1px solid white;
  }
  tr,td {
    text-align: center;
    border: 1px solid white;
    vertical-align:top;
  }
  </style>
  