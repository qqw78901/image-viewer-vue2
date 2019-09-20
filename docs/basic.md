基本用法

> 可以使用v-model方式或ref方式控制viewer，model值<0 时关闭viewer
> 或使用ref方式调用api来关闭

```vue
<template>
  <div>
    <div class="img-list">
      <ul >
        <li v-for="(item,index) in imgs" :key="index" @click="showImage(index)">
          <img :src="item"/>
        </li>
      </ul>
    </div>
   
    <image-viewer-vue2 :imgs="imgs" v-model="openIndex" ref="viewer"  @close="onClose"/>
  </div>
</template>

<script>
export default {
  data() {
    return {
      openIndex:-1,
      imgs:[
        'http://temp.im/1024x768/007AFF/fff',
        'http://temp.im/1024x768/4CD964/fff',
        'http://temp.im/1024x768/FF3B30/FFF',
        'http://temp.im/800x600/FF3B30/fff',
        'http://temp.im/1024x768/007AFF/fff',
        'http://temp.im/1024x768/FF3B30/FFF',
        'http://temp.im/1024x768/007AFF/fff',
        'http://temp.im/1024x768/007AFF/fff',
      ]
    }
  },
  methods:{
    onClose(){
      console.log('viewer close');
    },
    showImage(index){
      // use ref 
      // this.$refs.viewer.open(index);
      //use model 
      this.openIndex=index;
    }
  }
}
</script>
<style scoped lang="css">
.img-list{
  width: 320px;
  border:1px solid #c1bbbb;
}
.img-list ul{
  margin-top:0;
  margin-bottom:0;
  margin-right:-10px;
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  padding:0;
  justify-content: flex-start;
  list-style: none;
}
.img-list li{
  margin-right:10px;
  margin-bottom:10px;
  cursor:pointer;
}
.img-list li img{
  position: relative;
  width: 100px;
  display: block;
  height: 100px;
}

</style>
```

### 注意

 - list内容不会响应更新viewer，只会在下一次应用时更新
 - 在rem布局方式下，需要覆盖样式更新内部字号或webpack不对本组件的样式做rem转化处理