<template>
    <li
        class="col"
        @click="Event"
        :data-id="id"
    >{{ id }}</li>
</template>

<script>
    export default {
        name: "AppItem",
        props: {
            'id': Number
        },
        methods: {
            Event(e) {
                return this.$emit('event', e);
            }
        }
    }
</script>

<style scoped lang="scss">
    .col {
        display: flex;
        background-color: $col-bg-color;
        width: 100px;
        height: 100px;
        align-items: center;
        justify-content: center;
        margin: 2px;
        border-radius: 5px;
        cursor: pointer;
        @include large-text;

        &:hover {
            background-color: $col-bg-color-hover;
        }
    }
</style>
