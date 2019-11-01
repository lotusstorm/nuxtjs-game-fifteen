<template>
    <div class="wrapper">
        <transition name="menu">
            <app-confirm-menu
                v-show="showConfirmMenu"
                :msg="msg"
            >
                <template slot="control-buttons">
                    <input
                        type="button"
                        @click="confirm"
                        value="yes"
                        class="confirm-button">
                    <input
                        type="button"
                        @click="cancel"
                        value="no"
                        class="confirm-button">
                </template>
            </app-confirm-menu>
        </transition>
        <div :class="['content', {'no-action': showConfirmMenu}]">
            <header class="header">
                <h1 class="header__content">15</h1>
                <input
                    type="button"
                    value="restart"
                    @click="restart"
                    class="confirm-button"
                >
                <app-display
                    :counter="count"
                />
            </header>
            <div class="screen">
                <div
                    v-for="(col, key) in view"
                    :key="key"
                    @click="swap(key)"
                    :class="['col', {'disable' : !col}]"
                >{{ col }}</div>
            </div>
        </div>
    </div>
</template>

<script>
    import Display from '@/components/AppDisplay.vue'
    import ConfirmMenu from '@/components/AppConfirmMenu.vue'

    export default {
        name: "Fifteen",
        components: {
            'app-display': Display,
            'app-confirm-menu': ConfirmMenu,
        },
        data() {
            return {
                view: [...Array(16).keys()].sort(() => Math.random()-.5),
                count: 0,
                showConfirmMenu: false,
                valid: [...[...Array(16).keys()].slice(1), 0],
                msg: null,
            }
        },
        methods: {
            /**
             * Получает левую и правую границу поля
             * @param arr: массив с элементами
             * @param count: шаг
             * */
            getBorders(arr, count) {
                let borders = {left: [], right: []};
                let border = 0;
                for (let i=0; i<Math.ceil(arr.length/count); i++) {
                    if (border <= arr.length) {
                        border += count;
                        borders.left.push(border);
                        borders.right.push(border-1);
                    }
                }
                return borders
            },
            /**
             * Валидирует координаты что бы не выйти за пределы игрового поля
             * @param x: index елемента
             * @param arr: массив
             * */
            validCoords(x, arr) {
                let {left, right} = this.getBorders(arr, 4);
                return {
                    top: x - 4 >= 0 ? x - 4 : x,
                    bottom: x + 4 < arr.length ? x + 4 : x,
                    right: x - 1 >= 0 && !right.includes(x - 1) ? x - 1 : x,
                    left: x + 1 < arr.length && !left.includes(x + 1) ? x + 1 : x
                }
            },
            /**
             * Показывает скрывает меню
             * */
            toggleMenu() {
                this.showConfirmMenu = !this.showConfirmMenu;
            },
            /**
             * Проверяет победил игрок или нет
             * при победе предлагает начать заного
             * */
            isWinner() {

                if (this.view.map((el, ind) => el === this.valid[ind]).every(x => x)) {
                    this.msg = 'You win congratulations !!!! restart ?';
                    this.toggleMenu();
                }
            },
            /**
             * При нажатии на yes начинает игру заного
             * */
            confirm() {
                this.view = this.view.sort(() => Math.random()-.5);
                this.count = 0;
                this.toggleMenu();
            },
            /**
             * При нажатии на no возвращает на игровое поле
             * */
            cancel() {
                this.toggleMenu();
            },
            /**
             * При нажатии на restart пердлагает начать заного
             * */
            restart() {
                this.msg = 'Restart ?';
                this.toggleMenu();
            },
            /**
             * Меняет местами значения пустой ячейки и выбранного элемента
             * @param x: index елемента
             * */
            swap(x) {
                let coords = this.validCoords(x, this.view);
                for (let key of Object.values(coords)) {
                    if (!this.view[key]) {
                        this.count++;
                        this.view.splice(key, 1, this.view[x]);
                        this.view.splice(x, 1, 0);
                    }
                }
                this.isWinner();
            },
        }
    }
</script>

<style scoped lang="scss">
    .content {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: flex-end;
        border-radius: 5px;
        height: 500px;
        background-color: $wrapper-bg-color;
        padding: 5px;

        .header {
            display: flex;
            width: 100%;
            justify-content: space-between;
            margin-bottom: 5px;

            &__content {
                @include large-text;
                font-size: 3rem;
                color: $header-text-color;
            }
        }

        .screen {
            display: flex;
            justify-content: center;
            flex-flow: wrap;
            width: 420px;

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
        }
    }

    .disable {
        visibility: hidden;
    }

    .no-action {
        pointer-events: none;
        opacity: .5 !important;
        transition: opacity .5s linear;
    }

    .menu-enter-active {
        animation: menu-in .5s;
    }

    .menu-leave-active {
        animation: menu-in .5s reverse;
    }

    @keyframes menu-in {
        0% {
            opacity: 0;
            top: 1%;
        }
        100% {
            opacity: 1;
            top: 10%;
        }
    }

    .confirm-button {
        display: flex;
        align-items: center;
        justify-content: center;
        @include large-text;
        font-size: 2rem;
        border-radius: 5px;
        margin: 3px;
        background-color: $btn-bg-color;
        border-top: none;
        border-left: none;
        color: $btn-text-color;
        position: relative;
        bottom: 3px;
        box-shadow: 2px 2px 2px 1px rgba(0, 0, 255, .2);

        &:hover {
            background-color: $btn-bg-hover-color;
            color: $btn-text-hover-color;
            bottom: 1px;
            box-shadow: 1px 1px 1px 1px rgba(0, 0, 255, .2);
        }
    }
</style>
