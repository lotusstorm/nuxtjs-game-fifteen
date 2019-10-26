<template>
    <div class="operations">
        <h3 class="counter">{{ counter }}</h3>
    </div>
</template>

<script>
    export default {
        name: "AppCounter",
        props: {
            'counter': Number,
        }
    }
</script>

<style scoped lang="scss">
    .operations {
        display: flex;
        width: 100px;
        height: 50px;
        align-items: center;
        background-color: $operation-bg-color;
        padding: 3px;
        border-radius: 5px;
        @include large-text;
        font-size: 2rem;
    }
</style>
