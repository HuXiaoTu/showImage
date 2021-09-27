<template>
    <div id="app">
        <show-images
            :imgList="imgList"
            @error="errorMessage"
            @cancel="cancel"
        ></show-images>
    </div>
</template>

<script>
import ShowImages from './components/plugin.vue';
export default {
    name: 'app',
    data() {
        return {
            // imgList: ['http://www.qxxwss.ltd:8080/a.jpg', 'http://www.qxxwss.ltd:8080/b.png', 'http://www.qxxwss.ltd:8080/c.jpg']
            imgList: []
        }
    },
    methods: {
        errorMessage({ message }) {
            alert(message)
        },
        cancel() {
            console.info('ws >>>> ⚡⚡ 取消显示',);
        },
    },
    components: {
        ShowImages,
    }
}
</script>

<style lang="scss">
body,
html {
    width: 100%;
    height: 100%;
    margin: 0;
    padding: 0;
}
#app {
    width: 100%;
    height: 100%;
    // background-color: #42b983;
    text-align: center;
}
</style>
