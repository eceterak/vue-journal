<template>
    <div id="app">
        <div class="container">
            <journal></journal>
        </div>
    </div>
</template>

<script>
    import Journal from './components/Journal.vue'

    export default {
        name: 'app',
        components: {
            Journal
        }
    }
</script>

<style lang="scss">

    @import url('https://fonts.googleapis.com/css?family=Raleway&display=swap');

    body {
        font-family: 'Raleway', Arial, Helvetica, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        text-align: center;
        color: #2c3e50;
        background-color: #D6DED4;
        font-size: 13px;
    }

    *, *::before, *::after {
        box-sizing: border-box;
    }
    
    .container {
        max-width: 600px;
        margin-left: auto;
        margin-right: auto;
    }

</style>
