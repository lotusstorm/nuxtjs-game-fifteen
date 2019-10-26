<template>
    <div class="confirm-menu">
        <h1 class="confirm-menu__content">{{ msg }}</h1>
        <div class="confirm-menu__controllers-wrapper">
            <slot name="control-buttons"></slot>
        </div>
    </div>
</template>

<script>
    export default {
        name: "AppConfirmMenu",
        props: {
            'msg': String
        }
    }
</script>

<style scoped lang="scss">
    .confirm-menu {
        display: flex;
        position: absolute;
        flex-direction: column;
        top: 10%;
        align-items: center;
        justify-content: center;
        width: 450px;
        height: 250px;
        background: #F5F5F5;
        z-index: 1;
        border-radius: 2px;

        &__content {
            @include large-text;
            font-size: 2rem;
        }

        &__controllers-wrapper {
            display: flex;
            flex-flow: row;
            justify-content: space-between;
        }
    }

</style>
