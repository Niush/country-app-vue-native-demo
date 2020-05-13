<template>
  <view class="container">
    <scroll-view v-if="data">
        <view :style="{flex:1, justifyContent: 'center', alignItems: 'center'}">
            <view :style="{width: 100, height: 100}">
              <svg-uri :width="'100'" :height="'100'" :uri="data.flag"></svg-uri>
            </view>
            <text :style="{fontSize: 25, fontWeight: 'bold', marginTop: 5}">{{data.name}}</text>
            <text>Capital: {{data.capital}}</text>
            <text>Region: {{data.region}}</text>
            <text v-if="data.latlng && data.latlng.length >= 2">Lat, Lng: {{data.latlng[0]}}, {{data.latlng[1]}}</text>

            <view class="line" :style="lineStyle"></view>

            <text>Native Name: {{data.nativeName}}</text>
            <text v-if="data.languages && data.languages.length >= 1">Language: {{data.languages[0].name}} / {{data.languages[0].nativeName}}</text>
        
            <view class="line" :style="lineStyle"></view>

            <text>Population: {{populationComma}}</text>
            <text>Area: {{areaComma}}</text>
        
        </view>
    </scroll-view>
    <view v-else>
        <text :style="{fontSize: 25, fontWeight: 'bold', marginTop: 5, marginBottom: 10}">Invalid Data</text>
        <button :title="'Go Back'" @press="this.props.navigation.goBack()"/>
    </view>
  </view>
</template>

<script>
  import Vue from 'vue-native-core'
  import { SvgUri } from 'react-native-svg';

  Vue.component('svg-uri', SvgUri)

  export default {
    props: {
        navigation: {
            type: Object
        }
    },
    data: function(){
      return {
        app: "Country",
        data: (this.navigation.state.params ? this.navigation.state.params.data : null),
        lineStyle: {
          borderWidth: 1,
          width: 300,
          borderColor: '#eee',
          margin: 20,
        },
      }
    },
    methods: {
      
    },
    computed: {
        populationComma: function () {
            return this.data.population.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
        },
        areaComma: function () {
            return (this.data.area.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",") + ' km²');
        }
    }
  }
</script>

<style>
    .container{
        flex: 1;
        margin: 30px;
        margin-bottom: 10px;
        justify-content: flex-start;
    }
</style>