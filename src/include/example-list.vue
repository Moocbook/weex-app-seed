<template>
  <list>
    <cell v-for="item in items">
      <example-list-item :title="item.title" :url="item.url" :keyword="item.keyword" :desc="item.desc" :imgsrc="item.imgsrc"></example-list-item>
    </cell>
  </list>
</template>

<script>
  import exampleListItem from './example-list-item.vue';
  module.exports = {
    components: {
      'example-list-item': exampleListItem
    },
    props: {
      items: {
        default: []
      }
    },
    created: function() {
      const bundleUrl = weex.config.bundleUrl;
      var host = '';
      var path = '';
      var nativeBase;
      var isAndroidAssets = bundleUrl.indexOf('your_current_IP') >= 0 || bundleUrl.indexOf('file://assets/')>=0;
      var isiOSAssets = bundleUrl.indexOf('file:///') >= 0 && bundleUrl.indexOf('WeexDemo.app') > 0;
      if (isAndroidAssets) {
        nativeBase = 'file://assets/';
      }
      else if (isiOSAssets) {
        // file:///var/mobile/Containers/Bundle/Application/{id}/WeexDemo.app/
        // file:///Users/{user}/Library/Developer/CoreSimulator/Devices/{id}/data/Containers/Bundle/Application/{id}/WeexDemo.app/
        nativeBase = bundleUrl.substring(0, bundleUrl.lastIndexOf('/') + 1);
      }
      else {
        
        var matches = /\/\/([^\/]+?)\//.exec(bundleUrl);
        var matchFirstPath = /\/\/.+\/([^\/]+?)\//.exec(bundleUrl);
        if (matches && matches.length >= 2) {
          host = matches[1];
        }
        if (matchFirstPath && matchFirstPath.length >= 2) {
          path = matchFirstPath[1];
        }
        nativeBase = 'http://' + host + '/';
      }
      var h5Base = './weex.html?page=';
      // in Native
      var base = nativeBase;
      if (typeof navigator!=='undefined'&& (navigator.appCodeName === 'Mozilla' || navigator.product === 'Gecko') ) {
        // check if in weexpack project
        if (path === 'web') {
          base = h5Base + '/dist/';  
        } else {
          base = h5Base;
        }
      } else {
        base = nativeBase + path + '/';
      }
      for (var i in this.items) {
        var item = this.items[i];
        if (!item.url) {
          item.url = base + item.name + '.js';
          console.log(item.url);
        }
      }
      // see log in Android Logcat
      if (this.items.length) console.log('hit', this.items[0].url);
    }
  }
</script>

<style>
</style>