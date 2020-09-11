<template>
    <div class="container">
        <div class="row">
            <div class="col">
                <navbar-component></navbar-component>
            </div>
        </div>
        <div class="row">
            <div class="col">
                <head-component></head-component>
            </div>
        </div>
        <div class="row">
            <div class="col">
                <tweet-component></tweet-component>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        mounted() {
            console.log('Component mounted.')
        }
    }
</script>
