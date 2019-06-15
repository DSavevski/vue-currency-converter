<template>
    <div id="app">
        <img id="logo" alt="Vue logo" width="150" height="150" src="./assets/logo.png">
        <CurrencyConverter />
    </div>
</template>

<script>
    import CurrencyConverter from "./components/CurrencyConverter";

    export default {
        name: 'app',
        components: {
            CurrencyConverter
        }
    }
</script>

<style>
    #app {
        font-family: 'Avenir', Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        text-align: center;
    }

    #logo {
        margin-bottom: 50px;
    }

</style>
