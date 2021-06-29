<template>
  <main>
    <h2>地震情報</h2>
    <p>{{ newestEarthquakeText }}</p>
    <table>
      <tr>
        <td>場所</td>
        <td>{{newestEarthquakeAreaData.Name}}</td>
      </tr>
      <tr>
        <td>マグニチュード</td>
        <td>{{newestEarthquakeMagnitudeData}}</td>
      </tr>
      <tr>
        <td>最大震度</td>
        <td>{{newestEarthquakeIntensityMaxInt}}</td>
      </tr>
    </table>
    <h2>震度情報</h2>
    <table>
      <tr>
        <th>震度</th>
        <th>場所</th>
      </tr>
      <tr v-for="EarthquakePref in newestEarthquakeListJson" :key="EarthquakePref">
        <td>{{ EarthquakePref.jpName }}</td>
        <td v-if="EarthquakePref.data.length">
          <p v-for="EarthquakeCity in EarthquakePref.data" :key="EarthquakeCity">
          {{ EarthquakeCity.name }}
          <span v-for="EarthquakeCityList in EarthquakeCity.list" :key="EarthquakeCityList">
            {{ EarthquakeCityList }},
          </span>
          </p>
        </td>
        <td v-else>-</td>
      </tr>
    </table>
  </main>
</template>

<script>
import axios from 'axios'

export default {
  name: 'App',
  components: {
  },
  //API格納用のプロパティ
  data() {
    return{
      newestEarthquake: null,
      newestEarthquakeText: "",
      newestEarthquakeAreaData: [],
      newestEarthquakeMagnitudeData : "",
      newestEarthquakeIntensityMaxInt : "",
      newestEarthquakeIntensityPref : {},
      newestEarthquakeListJson : {}
    }
  },
  //axiosによるデータ取得処理
  mounted: function(){
    axios.get('https://www.jma.go.jp/bosai/quake/data/list.json')
    .then(function(response){
        //デバッグ用にconsoleに出力
        console.log(response.data)
        this.EarthquakeList = response.data

        axios.get('https://www.jma.go.jp/bosai/quake/data/' + response.data[0].json)
            .then(function(response){
                //デバッグ用にconsoleに出力
                console.log(response.data)
                this.newestEarthquake = response.data
                this.newestEarthquakeText = response.data.Head.Headline.Text
                this.newestEarthquakeAreaData = response.data.Body.Earthquake.Hypocenter.Area
                this.newestEarthquakeMagnitudeData = response.data.Body.Earthquake.Magnitude
                this.newestEarthquakeIntensityMaxInt = response.data.Body.Intensity.Observation.MaxInt
                this.newestEarthquakeIntensityPref = response.data.Body.Intensity.Observation.Pref
                let responsePrefData = response.data.Body.Intensity.Observation.Pref;


                /*
                0 : {
                  {
                    "name" : "滋賀県",
                    "list" : ["hoge","huga"]
                  },
                  {
                    略
                  }
                }
                */
                let newestEarthquakeListJsonList = {
                    "0" : {"jpName":"震度0","data":[]},
                    "1" : {"jpName":"震度1","data":[]},
                    "2" : {"jpName":"震度2","data":[]},
                    "3" : {"jpName":"震度3","data":[]},
                    "4" : {"jpName":"震度4","data":[]},
                    "5" : {"jpName":"震度5弱","data":[]},
                    "6" : {"jpName":"震度5強","data":[]},
                    "7" : {"jpName":"震度6弱","data":[]},
                    "8" : {"jpName":"震度6強","data":[]},
                    "9" : {"jpName":"震度7","data":[]},
                }

                for (let i=0;i<responsePrefData.length;i++) {
                    console.log(responsePrefData[i]);
                  for(let j=0;j<responsePrefData[i].Area.length;j++){
                    console.log(responsePrefData[i].Area[j]);
                    for(let k=0;k<responsePrefData[i].Area[j].City.length;k++){
                      console.log(responsePrefData[i].Name)
                      if(newestEarthquakeListJsonList[responsePrefData[i].Area[j].City[k].MaxInt]["data"][i] == null){
                        newestEarthquakeListJsonList[responsePrefData[i].Area[j].City[k].MaxInt]["data"].push({"name" : responsePrefData[i].Name, "list" : [responsePrefData[i].Area[j].City[k].Name]})
                      } else {
                        newestEarthquakeListJsonList[responsePrefData[i].Area[j].City[k].MaxInt]["data"][i]["list"].push(responsePrefData[i].Area[j].City[k].Name);
                      }
                      console.log(responsePrefData[i].Area[j].City[k].Name);
                      console.log(responsePrefData[i].Area[j].City[k].Code);
                      console.log(responsePrefData[i].Area[j].City[k].MaxInt);
                    }
                  }
                }
                console.log(newestEarthquakeListJsonList)
                this.newestEarthquakeListJson = newestEarthquakeListJsonList
            }.bind(this))

    }.bind(this))
    .catch(function(error){
        console.log(error)
    })
  }
}
</script>
<style>
html,body,header,main,footer,section, div,table,tr,td,th,
h1,h2,h3,h4,h5,p,a,span,tr,td, ul,li {margin: 0; padding: 0; font-family: 'Noto Sans JP', sans-serif;}

main {width: 90%; margin: 0 auto;}

main table {width: 100%;}
main table, main th, main td { border-collapse: collapse; border: 1px solid #ccc; line-height: 1.5;}
main table th {width: 150px; padding: 10px; font-weight: bold; vertical-align: middle;}
main table td { width: 350px; padding: 10px; vertical-align: middle;}
main table td p {margin: 2px 0;}
main tr:nth-child(even) {background: #d9d9d9;}
</style>
