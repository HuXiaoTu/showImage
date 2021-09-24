<template>
    <div id="app">
        <plug-in :imgList="imgList" @error="errorMessage"></plug-in>
    </div>
</template>

<script>
import PlugIn from './components/plugin.vue';
export default {
    name: 'app',
    data() {
        return {
            imgList: ['http://www.qxxwss.ltd:8080/a.jpg', 'http://www.qxxwss.ltd:8080/b.png', 'http://www.qxxwss.ltd:8080/c.jpg']
        }
    },
    methods: {
        errorMessage({ message }) {
            alert(message)
        }
    },
    components: {
        PlugIn,
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
