<template>
  <view class="container">
    <view class="center-container">
      <status-bar
        background-color="black"
        bar-style="light-content"
      />

      <text class="text-color-primary">{{message}}</text>
      
      <text-input
        :style="inputStyle"
        v-model="query"
        placeholder="Search by Country Name"
        :onSubmitEditing="loadCountryDetails"
      />

      <view class="line" :style="lineStyle"></view>
    </view>

    <view class="row-container">
      <text v-if="searchingFor && searching" :numberOfLines="1" :ellipsizeMode="'tail'" :style="searchingForStyle">Results For "{{searchingFor}}":</text>
      <text v-if="searchingFor && searching && result.length > 0" :style="matchCountStyle">{{result.length}} Found</text>
    </view>

    <view 
        v-if="searching && !refreshing && !searched"
        :style="{flex: 1, justifyContent: 'center'}">
        <activity-indicator size="large" color="#0000ff" />
    </view>

    <scroll-view :style="{marginTop: 10, flex: 1, overflow: 'hidden'}"
      :refreshControl="refreshControl()"
    >
      <!-- <view render-prop-fn="refreshControl" >
        <refresh-control :refreshing="this.refreshing" :onRefresh="this.refresh" />
      </view> -->
      <!-- If Results Found, Loop and show -->
      <view v-if="searched && result.length > 0">
        <touchable-opacity class="row-container" v-on:press="countryDetail(item)" :style="listItem" v-for="item in result" :key="item['alpha2Code']">
          <text>{{item.name}}</text>
        </touchable-opacity>
      </view>
      <text :style="{fontSize: 25}" v-if="searched && result.length <= 0">No Result</text>
    </scroll-view>

    <!-- <touchable-opacity @press="this.props.navigation.openDrawer()">
        <view :style = "{borderRadius: 2, backgroundColor: '#4488ee', alignItems: 'center', justifyContent: 'center', width: 50, paddingVertical: 7}">
            <text :style = "{color: 'white', fontSize: 15}">≡</text>
        </view>
    </touchable-opacity> -->

    <view :style="{width: 50, marginTop: 5}">
        <button :title="'≡'" @press="this.props.navigation.openDrawer()"/>
    </view>
  </view>
</template>

<script>
  import React from 'react';
  import {Dimensions, Alert, AsyncStorage, RefreshControl} from "react-native";

  export default {
    components: {RefreshControl},
    props: {
        navigation: {
            type: Object
        }
    },
    data: function(){
      return {
        message: "Country Details",
        query: "",
        searching: false,
        searchingFor: "",
        searched: false,
        result: [],
        refreshing: false,
        inputStyle: {
          borderColor: 'gray',
          borderWidth: 1,
          height: 40,
          width: Dimensions.get("window").width - 100,
          maxWidth: 500,
          borderRadius: 20,
          padding: 10,
        },
        lineStyle: {
          borderWidth: 1,
          width: Dimensions.get("window").width - 50,
          borderColor: '#eee',
          margin: 20,
        },
        searchingForStyle: {
          flexShrink: 2,
          maxWidth: Dimensions.get("window").width / 1.5
        },
        matchCountStyle: {
          color:'#999',
          fontSize: 10
        },
        listItem: {
          paddingVertical: 10,
          paddingHorizontal: 8,
          backgroundColor: '#eee'
        }
      }
    },
    methods: {
      simpleAlert: function(title, message){
        Alert.alert(
          title, message,
          [
            {text: 'OK'},
          ],
          { cancelable: true }
        );
      },

      loadCountryDetails: async function() {
        if(this.query.slice().trim().length <= 0){
          this.simpleAlert('Search for Country', 'By Name and view details')
          return false;
        }

        this.searching = true;
        this.searched = false;
        this.error = '';
        this.searchingFor = this.query.slice();
        this.result = [];

        try {
            // Search in Cache "query:search"
            // const value = await AsyncStorage.getItem(this.searchingFor+':search');
            const value = null;
            // If value exists in cache return it
            if (value !== null) {
                this.result = JSON.parse(value);
                this.searched = true;
                return true;
            }
            // Else fetch from rest api
            else{
                try {
                    let success = await fetch('https://restcountries.eu/rest/v2/name/'+this.searchingFor)
                        .then((res) => { // Gets response & returns json()
                            if(res.status >= 200 && res.status < 300) {
                                return res.json()
                            }
                            else if(res.status == 404){
                                // throw new Error('No Result Found.')
                                return []
                            }
                            else{
                                throw new Error('Unable to Connect to the Server. Check your Internet Status.')
                            }
                        })
                        .then(async (json) => {
                            this.result = json;
                            this.searched = true;
                            await AsyncStorage.setItem((this.searchingFor+':search'), JSON.stringify(json));
                            return true;
                        })
                        .catch((err) => {
                            if(err.message){
                                this.simpleAlert('Failed to Fetch Detail', err.message)
                            }else{
                                this.simpleAlert('Failed to Fetch Detail', 'Something went wrong')
                            }
                            this.searched = true;
                            return false;
                        })
                    
                    return success;
                } catch (err) {
                    this.simpleAlert('Failed to Fetch Detail', 'Something went wrong')
                    this.searched = true;
                    return false;
                }
            }
        } catch (error) {
            console.log(error)
            console.log('Failing to fetch cache :search data with error')
            return false;
        }
      },

      countryDetail: function(item){
        this.navigation.navigate("Country", {"data": item});
      },

      refreshControl: function(){
        return (
          <RefreshControl refreshing={this.refreshing} onRefresh={this.refresh}/>
        )
      },

      refresh: async function(){
        this.refreshing = true;
        try{
          await this.loadCountryDetails()
          this.refreshing = false
        }catch{
          this.refreshing = false
        }
      }
    }
  }
</script>

<style>
.container{
    flex: 1;
    margin: 30px;
    margin-bottom: 10px;
}
.center-container {
    background-color: white;
    align-items: center;
    justify-content: flex-start;
    overflow: hidden;
}
.text-color-primary {
    color: rgba(11, 88, 139, 0.514);
    font-size: 25;
    font-weight: 700;
    margin-bottom: 20;
}
.row-container{
    align-items: center;
    justify-content: space-between;
    flex-direction: row;
}
</style>
