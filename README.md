# show-images
> 查看大图功能，支持在线gif播放、下载。

> [GitHub项目地址](https://github.com/HuXiaoTu/showImage "GitHub")

> 欢迎留言沟通: 13676373110@163.com

<!-- ## demo演示 -->
<!-- ![](http://www.qxxwss.cn/frame-selection.gif) -->

## 使用

- ``` npm install show-images ```  //安装包资源

### 全局注册方式
``` JavaScript
    //main.js中引入
    import ShowImages from 'show-images'
    Vue.use(ShowImages)
```
``` JavaScript
    //*.vue中 直接使用
    <show-images 
        :imgList="imgList" 
        :showIndex="showIndex" 
        @error="error"
        @cancel="cancel"
         >
    </show-images>
    export default {
        data() {
            return {
                // 图片地址
                imgList:[],
                // 当前所应当展示 index
                showIndex:0,
            }
        },
        methods:{
            // 当出现错误时抛出错误消息
            error({message}){
                console.log(message)
            },
            // 点击内部关闭按钮时 触发
            cancel(){
                console.log('关闭组件')
            }
        }
    }
```
### 单独使用
``` JavaScript
    //*.vue中 直接使用
    <template>
        <show-images 
            :imgList="imgList" 
            :showIndex="showIndex" 
            @error="error"
            @cancel="cancel"
            >
        </show-images>
    </template>
    // 引入组件
    import ShowImages from 'show-images'
    export default {
        data() {
            return {
                // 图片地址
                imgList:[],
                // 当前所应当展示 index
                showIndex:0,
            }
        },
        methods:{
            // 当出现错误时抛出错误消息
            error({message}){
                console.log(message)
            },
            // 点击内部关闭按钮时 触发
            cancel(){
                console.log('关闭组件')
            }
        },
        // 注册组件
        components:{
            ShowImages,
        }
    }
```

## Options && Methods

### Options

属性 | 说明 | 例子| 默认值 |
-|-|-|-
imgList | 图片地址列表 (Array) | ['a.jpg'] | [] |
showIndex | 默认展示第几张图片 (Number) | 0 | 0 |
baseIp | 图片统一前缀 (String) | 无 | '' |

### Methods

事件名称 | 说明 | 返回值 |
-|-|-
error | 当内部发生错误时触发 | 抛出错误信息(Object) |
cancel | 取消时触发 | 无 |

## 二次开发

-  ``` npm install ```          //安装node运行环境

-  ``` npm run dev ```          //启动项目

-  ``` npm run build ```        //打包项目

## 仓库地址

GitHub项目地址[https://github.com/HuXiaoTu/showImage](https://github.com/HuXiaoTu/showImage "GitHub")

## 目录结构描述
```js
    └─components    // 组件开发区域
```
