<template>
  <view class="container">
    <image
        :source="require('./../assets/icon.png')"
        :style="{width: 100, height: 100}"
    />
    <text :style="{fontSize:30, marginTop:10, marginBottom: 10}">{{app}}</text>
    <text>Created By: Niush Sitaula</text>
    <text>With: Vue Native</text>
    <text>😊</text>
    <text :style="{fontSize: 8, marginTop: 10}">Disclamer: Some Country Flags might load improperly because they are SVG</text>
    <text :style="{fontSize: 8}">and Android deals with SVG weirdly</text>

    <view :style="{width: 50, marginTop: 5, position: 'absolute', left: 0, right: 0, bottom: 0, alignSelf: 'flex-start', justifySelf: 'flex-end'}">
        <button :title="'≡'" @press="this.props.navigation.openDrawer()"/>
    </view>
    <view :style="{width: 120, marginTop: 5, position: 'absolute', right: 0, bottom: 0, alignSelf: 'flex-start', justifySelf: 'flex-end'}">
        <button :title="'Clear Data'" @press="clearData"/>
    </view>
  </view>
</template>

<script>
  import React from 'react';
  import {Dimensions, Alert, AsyncStorage} from "react-native";

  export default {
    props: {
        navigation: {
            type: Object
        }
    },
    data: function(){
      return {
        app: "Country Details App",
      }
    },
    methods: {
      clearData: async function(){
        try {
            let allkeys = await AsyncStorage.getAllKeys();
            if(allkeys){
                allkeys = allkeys.filter((k) => {
                    return k.search(':search') >= 0
                })
                if(allkeys && allkeys.length > 0){
                    let removed = await AsyncStorage.multiRemove(allkeys);
                }
                Alert.alert(
                    'Cache Data Cleared', (allkeys.length + ' items removed'),
                    [
                        {text: 'OK'},
                    ],
                    { cancelable: true }
                );
            }
        } catch (error) {
            console.log(error)
            Alert.alert(
                'Failed to Clear Data', 'Something went wrong',
                [
                    {text: 'OK'},
                ],
                { cancelable: true }
            );
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
        align-items: center;
        justify-content: flex-start;
    }
</style>
