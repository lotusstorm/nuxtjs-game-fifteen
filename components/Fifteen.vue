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
        <div class="content">
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
            <div class="screen" id="main">
                <ul
                    v-for="(row, key) in render"
                    :key="key"
                    class="row"
                >
                    <app-item
                        v-for="(col, key) in row"
                        :key="key"
                        :id="col"
                        @event="swap"
                        :class="[{'disable' : !col}]"
                    >{{ col }}</app-item>
                </ul>
            </div>
        </div>
    </div>
</template>

<script>
    import Display from '@/components/AppDisplay.vue'
    import Item from '@/components/AppItem.vue'
    import ConfirmMenu from '@/components/AppConfirmMenu.vue'

    export default {
        name: "Fifteen",
        components: {
            'app-display': Display,
            'app-item': Item,
            'app-confirm-menu': ConfirmMenu,
        },
        data() {
            return {
                view: this.arrayToMatrix([...Array(16).keys()].sort(() => Math.random()-.5)),
                count: 0,
                showConfirmMenu: false,
                valid: '1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,0',
                msg: null,
            }
        },
        computed: {
            render() {
                return this.view;
            },
        },
        methods: {
            /**
             * Превращает массив в многомерную матрицу 4x4
             * @param arr: массив с элементами
             * */
            arrayToMatrix(arr) {
                let newArr = [];
                let start = 0;
                let end = 4;
                for (let i=0; i<arr.length/4; i++) {
                    if (end <= arr.length) {
                        newArr.push(arr.slice(start, end));
                        start += 4;
                        end += 4;
                    }
                }
                return newArr
            },

            /**
             * Валидирует координаты что бы не выйти за пределы игрового поля
             * @param x: воя координата
             * @param y: своя координата
             * @param matrix: игровое поле
             * */
            validCoords(x, y, matrix) {
                return {
                    top: x - 1 >= 0 ? x - 1 : x,
                    bottom: x + 1 < matrix.length ? x + 1 : x,
                    left: y - 1 >= 0 ? y - 1 : y,
                    right: y + 1 < matrix[0].length ? y + 1 : y
                }
            },
            /**
             * Возвращает координаты пустой ячейки если она рядом или свои координаты
             * @param top: корректные координаты верхней ячейки
             * @param bottom: корректные координаты нижней ячейки
             * @param left: корректные координаты левой ячейки
             * @param right: корректные координаты правой ячейки
             * @param x: своя координата
             * @param y: своя координата
             * @param matrix: игровое поле
             * */
            getCoords({top, bottom, left, right}, x, y, matrix) {
                let coords = {
                    [`${top}.${y}`]: matrix[top][y],
                    [`${bottom}.${y}`]: matrix[bottom][y],
                    [`${x}.${left}`]: matrix[x][left],
                    [`${x}.${right}`]: matrix[x][right],
                };

                for (let i in coords) {
                    if (!coords[i]) {
                        return {x: i.split('.')[0], y: i.split('.')[1]}
                    }
                }
                return {x, y}
            },

            /**
             * Показывает скрывает меню
             * */
            togleMenu() {
                this.showConfirmMenu = !this.showConfirmMenu;
                let content = document.getElementById('main');
                content.classList.toggle('no-action');
            },

            /**
             * Проверяет победил игрок или нет
             * при победе предлагает начать заного
             * */
            isWinner() {
                if (this.valid === this.view.join(',')) {
                    this.msg = 'You win congratulations !!!! restart ?';
                    this.togleMenu();
                }
            },
            /**
             * При нажатии на yes начинает игру заного
             * */
            confirm() {
                this.view = this.arrayToMatrix(this.valid.split(',').map(i => Number(i)).sort(() => Math.random()-.5));
                this.count = 0;
                this.togleMenu();
            },
            /**
             * При нажатии на no возвращает на игровое поле
             * */
            cancel() {
                this.togleMenu();
            },
            /**
             * При нажатии на restart пердлагает начать заного
             * */
            restart() {
                this.msg = 'Restart ?';
                this.togleMenu();
            },
            /**
             * Меняет местами значения пустой ячейки и выбранного элемента по событию
             * @param e событие клика
             * */
            swap(e) {
                outer: for (let i=0; i<this.view.length; i++) {
                    for (let j=0; j<this.view[i].length; j++) {
                        if (Number(e.target.getAttribute('data-id')) === this.view[i][j]) {
                            let coords = this.validCoords(i, j, this.view);
                            let {x, y} = this.getCoords(coords, i, j, this.view);
                            if (x !== i && y !== j) {
                                this.count++;
                                [this.view[x][y], this.view[i][j]]= [this.view[i][j], this.view[x][y]];
                            }
                            break outer;
                        }
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
            .row {
                display: flex;
                flex-flow: row;
                padding: 0;
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
