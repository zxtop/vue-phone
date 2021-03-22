<template>
  <div class="hello" id="hello">

    <div>手机拍照应用</div>

    <br>
    <label>照相机</label> <input type="file" id='image' accept="image/*" capture='user'> <br> 
    <label>摄像机</label> <input type="file" id='video' accept="video/*" capture='camcorder'>
    <input type="file" id="file" multiple @change="fileChoose">

  </div>
</template>

<script>
// import func from 'vue-editor-bridge'
export default {
  name: 'HelloWorld',
  data () {
    return {

      msg: 'Welcome to Your Vue.js App',

      copydata:[
        124,
        {
          a:1,
          b:{
            c:2
          }
        },
        {
          arr:['3',4,'5']
        }
      ],

      tree:{
        name: '电脑',
        children: [
          {
            name: 'F盘',
            children: [
              {
                name: '照片',
                children: []
              },
              {
                name: '文件',
                children: [
                  {
                    name: '工作文件',
                    children: [
                      {
                        name: '报告',
                        children: []
                      }
                    ]
                  }
                ]
              }
            ]      
          },
          {
            name: 'E盘',
            children: [
              {
                name: '视频',
                children: [
                  {
                    name: 'js教程',
                    children: []
                  }
                ]
              }
            ]
          }
        ]

      },
      obj:{
        a: '1',
        b: {
            c: '2',
            D: {
                E: '3'
            }
        }
      }

    }
  },
  created () {
    let obj = this.deepClone(this.copydata)
    console.log("copyobj======",obj)

    const root = document.getElementById('hello');
    this.getChildNodes(root,function(node){
      console.log(node,"node=====")
      node.setAttribute('data-index','123')
    })

    //遍历树形结构
    this.deepTraversal(this.tree,function(node){
      console.log("打印名字=====",node.name)
    })

    //数组对象格式化
    console.log(this.keysLower(this.obj),"数组对象格式化==========")

    // 阶乘
    console.log(this.factorial(5,1),"阶乘=======")



    // 在各个机型都可以点击 file 调用相册 和 摄像头拍照 
    // 1. 在老版本的安卓中，必须加上capture，否则只能调用相册 
    // 2. 在IOS中 加了capture，就只能调用摄像头不能调用相册
    // 判断ios，如果是ios就去掉capture属性.

    console.log(navigator.userAgent,"机器。。。。")
    let info=navigator.userAgent;
    if(info.match(/iPhone\sOS/i)){
    // 　　dom.removeAttribute("capture")　　
    }
    
    

  },
  methods: {

    // 读取图片(fileChoose即为input)
    // fileChoose:()=>{
    // 　　let file=fileChoose.files[0],
    // 　　reader=new FileReader();
    // 　　reader.onLoad=()=>{
    // 　　　　img.src=reader.result
    // 　　};
    // 　　reader.readAsDataURL(file)
    // },
    fileChoose(e){
      console.log("选择文件====",e)
    },

    //判断是否是对象
    isObject(obj){
      var type = typeof obj;
      return type === 'function' || type === 'object' && !!obj;
    },

    //用递归的方式进行深拷贝
    deepClone(obj){
      if(!this.isObject(obj)) return obj;         //判断是否是对象或者是数组，如果不是则返回原始值

      var result = new obj.constructor();         //创建一个对象返回；巧妙避免对当前数据是数组还是对象的判断
      
      for(var i in obj){
        if(obj.hasOwnProperty(i)){
          result[i] = this.deepClone(obj[i]);
        }
      }
      return result;
    },

    //递归遍历元素的所有子节点 给每个元素节点添加'data-index'属性
    /**
     * @param {节点或元素} root 要递归的节点或元素
     * @param {function} callback 每一次遍历的回调函数
     * 
     */
    getChildNodes(root,callback){
      // 判断是否存在子元素
      if(root && root.children && root.children.length){
        //将子元素转换成可遍历的数组
        Array.from(root.children).forEach(node=>{
          callback && typeof callback === 'function' && callback(node);
          //递归子元素，重复上面操作
          this.getChildNodes(node,callback)
        })
      }
    },


    // 树形结构递归  遍历树形结构深度优先原则：就是顺着一个节点延伸下去，先遍历它的第一个子节点，
    // 然后是第一个孙节点，然后重孙节点，直到没有子节点为止。即先纵深遍历完之后在遍历同级的其他节点。
    deepTraversal(root,cb){
      let that = this;
      if(!root) return;
      cb && typeof cb === 'function' && cb(root);
      if(root.children && root.children.length){
        var i = 0,node;
        for(;node=root.children[i++];){
          that.deepTraversal(node,cb);
        }
      }
    },

    keysLower(obj){
      let reg = new RegExp("([A-Z]+)", "g");
      for (let key in obj) {
          if (obj.hasOwnProperty(key)) {

              let temp = obj[key];

              if (reg.test(key.toString())) {

                  // 将修改后的属性名重新赋值给temp，并在对象obj内添加一个转换后的属性
                  temp = obj[key.replace(reg, function (result) {
                      return result.toLowerCase()
                  })] = obj[key];

                  // 将之前大写的键属性删除
                  delete obj[key];
              }

              // 如果属性是对象或者数组，重新执行函数
              if (typeof temp === 'object' || Object.prototype.toString.call(temp) === '[object Array]') {
                  this.keysLower(temp);
              }

          }
      }
      return obj;
    },

    factorial(n, total) {
      if (n === 1) return total;
      return this.factorial(n - 1, n * total);
    }
    
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
