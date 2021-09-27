<template>
    <div class="moreImage">
        <!-- 关闭按钮 -->
        <div class="moreImageTop">
            <span class="iconfont icon-guanbi" @click="$emit('cancel')"></span>
        </div>
        <!-- 展示部分 -->
        <div class="moreImageCont">
            <!-- 图片展示 盒子 -->
            <div class="moreImageContBox"></div>
            <!-- 左右切换 -->
            <span
                class="iconfont"
                :class="[form.isLoading ? 'icon-Loading' : 'icon-zuo']"
                @click="leftOrRight('left')"
            ></span>
            <span
                class="iconfont"
                :class="[form.isLoading ? 'icon-Loading' : 'icon-you']"
                @click="leftOrRight('right')"
            ></span>
        </div>
        <!-- 底部按钮 -->
        <div class="moreImageBot">
            <div class="moreImageBotBtn">
                <div class="moreImageBotBtnM">
                    当前第{{ this.form.showIndex + 1 }}张/共{{
                        this.imgList.length
                    }}张
                </div>
                <!-- 按钮 条件控制 区域 -->
                <div class="moreImageBotBtnBox">
                    <!-- 按钮区域 -->
                    <div class="moreImageBotBtnBoxB">
                        <!-- 上一张 -->
                        <span
                            class="iconfont"
                            :class="[
                                form.isLoading ? 'icon-Loading' : 'icon-zuo',
                            ]"
                            @click="leftOrRight('left')"
                        ></span>
                        <!-- 暂停 -->
                        <span
                            v-if="!form.isPlay"
                            class="iconfont"
                            :class="[
                                form.isLoading ? 'icon-Loading' : 'icon-bofang',
                            ]"
                            @click="playPause('pause')"
                        ></span>
                        <!-- 播放 -->
                        <span
                            v-else
                            class="iconfont"
                            :class="[
                                form.isLoading
                                    ? 'icon-Loading'
                                    : 'icon-bofang1',
                            ]"
                            @click="playPause('play')"
                        ></span>
                        <!-- 下一张 -->
                        <span
                            class="iconfont"
                            :class="[
                                form.isLoading ? 'icon-Loading' : 'icon-you',
                            ]"
                            @click="leftOrRight('right')"
                        ></span>
                        <!-- 下载 -->
                        <span
                            class="iconfont"
                            :class="[
                                form.isLoading ? 'icon-Loading' : 'icon-xiazai',
                            ]"
                            @click="downLoadGIF"
                        ></span>
                    </div>
                    <!-- 播放间隔 -->
                    <div class="moreImageBotBtnBoxJ">
                        <span>播放间隔：</span>
                        <select v-model="form.intervalTime">
                            <option
                                v-for="i in list.intervalTimeData"
                                :key="i"
                                :value="i"
                            >
                                {{ i + "s" }}
                            </option>
                        </select>
                    </div>
                    <!-- 是否重复播放 -->
                    <div class="moreImageBotBtnBoxJ">
                        <span>重复播放：</span>
                        <input
                            class="switch-component"
                            type="checkbox"
                            v-model="form.isRepeat"
                            :disabled="form.isLoading"
                        />
                    </div>
                    <!-- 跳转至 -->
                    <div class="moreImageBotBtnBoxJ">
                        <span>跳转至：</span>
                        <input
                            v-model="form.jumpNumber"
                            size="mini"
                            placeholder="number"
                            class="jumpInput"
                            type="number"
                            style="width: 55px"
                            @blur="jumpNumber"
                            :disabled="form.isLoading"
                        />
                    </div>
                </div>
            </div>
        </div>
        <!-- loading -->
        <div class="loadingBox" v-if="form.pageLoading">
            <span class="loadingBtn iconfont icon-Loading"></span>
        </div>
    </div>
</template>

<script>
import "../../css/iconfont/iconfont.css";
import gifshot from 'gifshot';
export default {
    name: 'show-images',
    props: {
        // 展示图片列表
        imgList: {
            type: Array,
            default: () => {
                return []
            }
        },
        // 当前展示 第几张
        showIndex: {
            type: Number,
            default: 0,
        },
        // 图片 统一前缀 默认为空
        baseIp: {
            type: String,
            default: '',
        }
    },
    data() {
        return {
            // 选中 数据
            form: {
                // 播放 间隔 时间
                intervalTime: 1,
                // 是否 重复播放
                isRepeat: false,
                // 是否 在播放状态
                isPlay: false,
                // 是否 加载状态
                isLoading: false,
                // 图片缩放等级
                scale: 1,
                // 当前 展示 图片的Index
                showIndex: 0,
                // 跳转 至
                jumpNumber: 1,
                // 页面loading
                pageLoading: false,
            },

            // 数据列表
            list: {
                // 播放间隔列表
                intervalTimeData: [0.2, 0.4, 0.6, 0.8, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 30, 60, 90],
                // 数据缓存列表
                imageCacheList: [],
            },
        }
    },
    methods: {
        // 播放 暂停 操作
        playPause(type) {
            this.form.isPlay = !this.form.isPlay
            if (type === 'pause') {
                let animation = () => {
                    // 监听 播放状态为 false 时 终止执行
                    if (!this.form.isPlay) return;
                    // 当播放到最后一张时
                    if (this.form.showIndex === this.imgList.length - 1) {
                        // 如果不是 循环播放 将被终止
                        if (!this.form.isRepeat) return this.form.isPlay = false;
                        // 循环播放 重置为第一张
                        this.form.showIndex = 0;
                    }
                    else this.form.showIndex++;
                    // 渲染图片
                    this.loadingImg(this.form.showIndex, this.imgList, () => {
                        setTimeout(() => {
                            animation();
                        }, this.form.intervalTime * 1000);
                    });
                }
                // 启动任务
                animation();
            }
            else if (type === 'play') {
            }
        },
        // 左右切换 上一张下一张
        leftOrRight(type) {
            // 暂停动画播放
            this.form.isPlay = false;
            if (type === 'left') {
                if (this.form.showIndex === 0) return this.$emit('error', { message: '当前已是第一张' });
                this.form.showIndex--;
            }
            else if (type === 'right') {
                if (this.form.showIndex === this.imgList.length - 1) return this.$emit('error', { message: '当前已是最后一张' });
                this.form.showIndex++;
            }
            // 加载 当前index 图片
            this.loadingImg(this.form.showIndex, this.imgList);
        },
        // 跳转至 某张
        jumpNumber() {
            let num = Number(this.form.jumpNumber) - 1;
            if (!this.imgList[num]) return this.$emit('error', { message: '指定张数不存在，请重新输入！' })
            // 暂停动画播放
            this.form.isPlay = false;
            // 跳转至 指定张数
            this.form.showIndex = num;
            this.loadingImg(this.form.showIndex, this.imgList);
        },
        // 生成并 下载 GIF
        downLoadGIF() {
            if (!this.imgList || this.imgList.length === 0) return this.$emit('error', { message: '数据列表为空，无法生成GIF！' })

            /**
           * desc: base64对象转blob文件对象
           * @param urlData  ：数据的base64对象
           * @param type  ：类型 png,pdf,doc,mp3等;
           * @returns {Blob}：Blob文件对象
           */
            function base64ToBlob(urlData, type) {
                let arr = urlData.split(',');
                let array = arr[0].match(/:(.*?);/);
                let mime = (array && array.length > 1 ? array[1] : type) || type;
                // 去掉url的头，并转化为byte
                let bytes = window.atob(arr[1]);
                // 处理异常,将ascii码小于0的转换为大于0
                let ab = new ArrayBuffer(bytes.length);
                // 生成视图（直接针对内存）：8位无符号整数，长度1个字节
                let ia = new Uint8Array(ab);
                for (let i = 0; i < bytes.length; i++) {
                    ia[i] = bytes.charCodeAt(i);
                }
                return new Blob([ab], {
                    type: mime
                });
            }
            /**
             * desc: 下载导出文件
             * @param blob  ：返回数据的blob对象或链接
             * @param fileName  ：下载后文件名标记
             * @param fileType  ：文件类 word(docx) excel(xlsx) ppt等
             */
            function downloadExportFile(blob, fileName, fileType) {
                let downloadElement = document.createElement('a');
                let href = blob;
                if (typeof blob == 'string') {
                    downloadElement.target = '_blank';
                } else {
                    href = window.URL.createObjectURL(blob); //创建下载的链接
                }
                downloadElement.href = href;
                downloadElement.download = fileName + '.' + fileType; //下载后文件名
                document.body.appendChild(downloadElement);
                downloadElement.click(); //触发点击下载
                document.body.removeChild(downloadElement); //下载完成移除元素
                if (typeof blob != 'string') {
                    window.URL.revokeObjectURL(href); //释放掉blob对象
                }
            }
            this.gitLoadImg(0, this.imgList, (img) => {
                if (!img) return this.$emit('error', { message: '图片加载失败！' });
                this.form.pageLoading = true;
                gifshot.createGIF({
                    'images': this.imgList,
                    'interval': this.form.intervalTime,
                    'gifWidth': img.width,
                    'gifHeight': img.height,

                }, (obj) => {
                    this.form.pageLoading = false;
                    if (!obj.error) {
                        let image = obj.image;
                        downloadExportFile(base64ToBlob(image, 'gif'), 'a', 'gif')
                    }
                });
            })
        },
        // 加载图片
        loadingImg(index, list, cb = null) {
            let imgBox = document.querySelector('.moreImageContBox');
            let cacheImg = this.list.imageCacheList[index];
            // 先查看缓存中是否 有对应图片 如果有 直接添加
            if (cacheImg) {
                imgBox.innerHTML = '';
                imgBox.appendChild(cacheImg);
                if (cb) cb(cacheImg);
                return
            }
            // 进行图片加载
            this.form.isLoading = true;
            let img = new Image();
            img.onload = () => {
                // 设置 自定义事件
                this.imgMethods(img);
                // 结束加载
                this.form.isLoading = false;
                // 设置宽高
                let HW = this.imgWidthHeight(img, imgBox);
                img.style.height = HW.height;
                img.style.width = HW.width;
                img.style.position = 'absolute';
                // 先清空 在追加
                imgBox.innerHTML = '';
                // 加载 图片
                imgBox.appendChild(img);
                // 缓存
                this.list.imageCacheList[index] = img;
                // 执行回调
                if (cb) cb(img);
            }
            img.onerror = () => {
                // 结束加载
                this.form.isLoading = false;
                this.$emit('error', { message: '图片加载失败' })
            }
            img.src = this.baseIp + list[index];
        },
        // 加载 gif 图片生成规则
        gitLoadImg(index, list, cb = null) {
            let img = new Image();
            // 执行回调
            img.onload = () => {
                if (cb) cb(img);
            }
            img.onerror = () => cb(null);
            img.src = this.baseIp + list[index];
        },
        // 计算图片的宽高
        imgWidthHeight(target, box) {
            let imgHeight = target.height;
            let imgWidth = target.width;
            let imgBoxWt = box.offsetWidth;
            let imgBoxHt = box.offsetHeight;
            if (imgWidth / imgHeight > imgBoxWt / imgBoxHt) return { width: '100%', height: 'auto', }
            else return { width: 'auto', height: '100%', }
        },
        // 绑定 图片拖拽 缩放事件
        imgMethods(img) {
            // 放大 || 缩小
            img.onmousewheel = (event) => {
                event.preventDefault();
                if (event.wheelDelta > 0) {
                    this.form.scale += 0.1
                }
                else {
                    if (this.form.scale < 0.4) return
                    this.form.scale -= 0.1
                }
                event.target.style.transform = `scale(${this.form.scale}) `;
            }
            // 拖拽
            img.onmousedown = (e) => {
                e.preventDefault();
                let ele = e.target;
                let startX = e.clientX - ele.offsetLeft;
                let startY = e.clientY - ele.offsetTop;
                document.onmousemove = function (e) {
                    let endX = e.clientX - startX;
                    let endY = e.clientY - startY;
                    ele.style.left = endX + "px";
                    ele.style.top = endY + "px";
                };
                document.onmouseup = function (e) {
                    document.onmousemove = null;
                    document.onmouseup = null;
                };
            }
        },
    },
    mounted() {
        if (!Array.isArray(this.imgList) || this.imgList.length === 0) {
            this.$emit('error', { message: '图片列表为空！' })
            this.form.isLoading = true;
            return;
        }
        if (this.showIndex || this.showIndex === 0) {
            // 加载 当前index 图片
            this.loadingImg(this.showIndex, this.imgList);
            // 储存当前 index
            this.form.showIndex = this.showIndex;
        }
    },
}
</script>
<style lang="scss">
@keyframes loadingAnimation {
    0% {
        transform: rotate(0);
    }
    100% {
        transform: rotate(360deg);
    }
}
.moreImage {
    position: fixed;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
    z-index: 111;
    display: flex;
    flex-direction: column;
    background-color: #fff;
    .moreImageTop {
        height: 50px;
        text-align: right;
        .iconfont {
            margin: 10px;
            display: inline-block;
            font-size: 25px;
            cursor: pointer;
        }
    }
    .moreImageCont {
        flex: 1;
        position: relative;
        overflow: hidden;
        .moreImageContBox {
            width: 100%;
            height: 100%;
            display: flex;
            align-items: center;
            justify-content: center;
            position: relative;
            > image {
                display: inline-block;
            }
        }
        .iconfont {
            position: absolute;
            font-size: 50px;
            color: #606266;
            top: 50%;
            transform: translateY(-50%);
            display: none;
            cursor: pointer;
        }
        .icon-zuo {
            left: 30px;
        }
        .icon-you {
            right: 30px;
        }
    }
    .moreImageCont:hover {
        .iconfont {
            display: inline-block;
        }
    }
    .moreImageBot {
        height: 150px;
        text-align: center;
        .moreImageBotBtn {
            display: inline-block;
            .moreImageBotBtnM {
                color: #999;
                padding: 5px 0;
            }
            .moreImageBotBtnBox {
                display: flex;
                > div {
                    vertical-align: middle;
                }
                .moreImageBotBtnBoxB {
                    height: 50px;
                    line-height: 50px;
                    .iconfont {
                        margin: 10px;
                        font-size: 30px;
                        cursor: pointer;
                    }
                }
                .moreImageBotBtnBoxJ {
                    display: flex;
                    align-items: center;
                    margin-left: 5px;
                    > select,
                    .jumpInput {
                        outline: none;
                        height: 25px;
                        line-height: 25px;
                        border: 1px solid #ccc;
                        border-radius: 5px;
                    }
                    // switch组件
                    .switch-component {
                        position: relative;
                        width: 40px;
                        height: 20px;
                        background-color: #dadada;
                        border-radius: 20px;
                        border: none;
                        outline: none;
                        -webkit-appearance: none;
                        transition: all 0.2s ease;
                        border: 1px solid #ccc;
                        cursor: pointer;
                    }
                    // 按钮
                    .switch-component::after {
                        content: "";
                        position: absolute;
                        top: 0;
                        left: 0;
                        width: 50%;
                        height: 100%;
                        background-color: #fff;
                        border-radius: 50%;
                        transition: all 0.2s ease;
                    }
                    // checked状态时，背景颜色改变
                    .switch-component:checked {
                        background-color: #86c0fa;
                    }
                    // checked状态时，按钮位置改变
                    .switch-component:checked::after {
                        left: 50%;
                    }
                }
            }
        }
    }
    // loading
    .loadingBox {
        position: fixed;
        top: 0;
        left: 0;
        width: 100vw;
        height: 100vh;
        background-color: rgba(0, 0, 0, 0.479);
        display: flex;
        align-items: center;
        justify-content: center;
        .loadingBtn {
            width: 40px;
            height: 40px;
            font-size: 40px;
            color: #fff;
        }
    }
    // 动态loading
    .icon-Loading {
        animation: loadingAnimation 1s linear infinite;
    }
    // element 样式重置
    .el-select {
        height: 25px;
        .el-input {
            height: 25px;
            .el-input__inner {
                height: 25px;
            }
            .el-select__caret {
                line-height: 25px;
            }
        }
    }
    .el-input__inner {
        height: 25px;
        line-height: 25px;
        padding: 0 5px;
    }
}
</style>