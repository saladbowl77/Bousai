<template>
  <main>
    <p>{{ newestEathqwakeText }}</p>
    <table>
      <tr>
        <td>場所</td>
        <td>{{newestEathqwakeAreaData.Name}}</td>
      </tr>
      <tr>
        <td>マグニチュード</td>
        <td>{{newestEathqwakeMagnitudeData}}</td>
      </tr>
      <tr>
        <td>最大震度</td>
        <td>{{newestEathqwakeIntensityMaxInt}}</td>
      </tr>
    </table>
    <table>
      {{ newestEathqwakeIntensityPref }}
      <tr v-for="EathqwakePref in newestEathqwakeIntensityPref" :key="EathqwakePref">
        <td>{{ EathqwakePref.Name }}</td>
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
      newestEathqwake: null,
      newestEathqwakeText: "",
      newestEathqwakeAreaData: [],
      newestEathqwakeMagnitudeData : "",
      newestEathqwakeIntensityMaxInt : "",
      newestEathqwakeIntensityPref : {}
    }
  },
  //axiosによるデータ取得処理
  mounted: function(){
    axios.get('https://www.jma.go.jp/bosai/quake/data/list.json')
    .then(function(response){
        //デバッグ用にconsoleに出力
        console.log(response.data)
        this.eathqwakeList = response.data

        axios.get('https://www.jma.go.jp/bosai/quake/data/' + response.data[0].json)
            .then(function(response){
                //デバッグ用にconsoleに出力
                console.log(response.data)
                this.newestEathqwake = response.data
                this.newestEathqwakeText = response.data.Head.Headline.Text
                this.newestEathqwakeAreaData = response.data.Body.Earthquake.Hypocenter.Area
                this.newestEathqwakeMagnitudeData = response.data.Body.Earthquake.Magnitude
                this.newestEathqwakeIntensityMaxInt = response.data.Body.Intensity.Observation.MaxInt
                this.newestEathqwakeIntensityPref = response.data.Body.Intensity.Observation.Pref
            }.bind(this))

    }.bind(this))
    .catch(function(error){
        console.log(error)
    })
  }
}
</script>

<style>
</style>
