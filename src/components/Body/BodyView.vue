<template>
    <div class="body" v-if="!orderAnswer">
        <div class="summ" v-if="summ">
            Сумма заказа: {{ summ }}&#8381;
        </div>
        <div class="cart-items" v-for="item in cartItems" :key="item.id">
            <div class="select-wrapper">
                <div>Продукт:</div>
                <select v-model="item.product" class="select" v-on:change="fetchSmell(item)">
                    <option v-for="option in productOptions" :value="option.id" :key="option.id">
                        {{ option.name }} ({{ option.price }}&#8381;)
                    </option>
                </select>
            </div>
            <div v-if="item.product" class="select-wrapper">
                <div>Аромат:</div>
                <select v-model="item.smell" class="select">
                    <option v-for="option in item.smellOptions" :value="option.id" :key="option.id">
                        {{ option.name }}
                    </option>
                </select>
            </div>
        </div>
        <button class="button" type="button" v-on:click="increaseProdItems">Добавить товар</button>
        <div class="information">
            <label for="name">ФИО</label>
            <input type="text" name="name" id="name" v-model="customerInformation.name">

            <label for="postIndex">Почтовый индекс</label>
            <input type="text" name="postIndex" id="postIndex" v-model="customerInformation.postIndex">

            <label for="address">Адрес доставки</label>
            <input type="text" name="address" id="address" v-model="customerInformation.address">

        </div>
        <button class="button" type="button" v-if="!user" :disabled="!validate" v-on:click="onSendDataFromBrowser">Оформить
            заказ</button>


    </div>
    <div class="body" v-else>
        <div>

            Ваш заказ № <b>{{ orderAnswer[0].id }}</b> на сумму <b>{{ summ }}&#8381;</b>
        </div>
        <ul class="order-item-list">
            <li class="order-item" v-for="orderItem in orderAnswer" :key="orderItem.orderItemId">
                {{ orderItem.product }} - {{ orderItem.smell }}
            </li>
        </ul>
        <b style="margin: 20px 0">
            Информация о заказе:
        </b>
        <div class="order-info">
            <div>ФИО: {{ orderAnswer[0].customername }}</div>
            <div>Адрес: {{ orderAnswer[0].address }}</div>
            <div>Почтовый индекс: {{ orderAnswer[0].postindex }}</div>
        </div>
        <b style="margin: 20px 0">
            Информация об оплате:
        </b>
        <div>
            Оплата заказа осуществляется переводом на карту по номеру телефона: <br>
            Номер телефона: <b>+7(916)838-12-26</b> <br>
            Сумма: <b>{{ summ }}</b> <br>
            Комментарий: <b>{{ orderAnswer[0].id }}</b> <br>
        </div>
    </div>
</template>

<script>

import { useTelegram } from "../../hooks/useTelegram";

// import { toRaw } from 'vue';
import axios from 'axios';
import global from '../../../globalVar.js';

const { user, tg, queryId } = useTelegram();

export default {
    data() {
        return {
            userName: user?.first_name || 'Дорогой гость',
            product: undefined,
            productOptions: [],
            smell: undefined,
            cartItems: [{
                id: 1,
                smellOptions: [],
                smell: undefined,
                product: undefined
            }],
            customerInformation: {
                name: '',
                address: '',
                postIndex: ''
            },
            summ: 0,
            orderAnswer: undefined,
            validate: true
        }
    },
    mounted() {
        tg.MainButton.show();
        tg.MainButton.setParams({
            text: `Оформить заказ`
        });
        tg.onEvent('mainButtonClicked', this.onSendData);

        this.fetchProduct();
    },
    methods: {
        async fetchProduct() {

            try {
                const response = await axios.get(global.serverAdress + '/product/'); // url on back
                this.productOptions = response.data;
            } catch (error) {
                console.log(error);
                alert(`Ошибка: ` + error)
            }
        },

        async summCount() {
            this.summ = 0;
            this.cartItems.forEach(element => {
                this.productOptions.forEach(opt => {
                    if (opt.id == element.product) {
                        this.summ += opt.price;
                    }
                })
            });

        },
        async fetchSmell(item) {
            this.summCount();

            try {
                const response = await axios.get(global.serverAdress + '/smell/' + item.product); // url on back
                item.smellOptions = response.data;
            } catch (error) {
                alert(`Ошибка: ` + error)
            }
        },

        increaseProdItems() {
            let lastItem = this.cartItems[this.cartItems.length - 1].id;
            this.cartItems.push({
                id: lastItem + 1,
                smell: undefined,
                product: undefined
            })
        },

        async validateData() {
            this.cartItems.forEach(item => {
                if (item.product && item.smell) {
                    this.validate = true;
                }
                else {
                    this.validate = false;
                }
            });
            if (this.customerInformation.address && this.customerInformation.name && this.customerInformation.postIndex && this.validate) {
                this.validate = true;
            }
            else {
                this.validate = false;
            }
        },

        onSendData() {
            const data = {
                user: user,
                queryId,
                cartItems: this.cartItems,
                customerInformation: this.customerInformation,
                summ: this.summ
            }

            fetch(global.serverAdress + '/newOrder', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                },
                body: JSON.stringify(data)
            })
        },

        async onSendDataFromBrowser() {
            let me = this;
            me.validate = false;
            await me.validateData();

            const data = {
                cartItems: me.cartItems,
                customerInformation: me.customerInformation,
                summ: me.summ
            }

            if (me.validate) {
                axios.post(global.serverAdress + '/newOrderFromBrowser', data)
                    .then(function (response) {
                        me.orderAnswer = response.data.order;
                        console.log(me.orderAnswer);
                    })
                    .catch(function (error) {
                        console.log(error);
                    });
            }
            else {
                alert('Заполните все поля');
                me.validate = true;
            }


        },
    },
}

</script>
<style>
.body {
    width: 100%-20px;
    display: flex;
    align-items: center;
    padding: 10px;
    display: flex;
    flex-direction: column;
    /* background-color: #7a6b64; */
    background: rgb(59, 45, 27);
    background: linear-gradient(180deg, rgba(59, 45, 27, 1) 0%, rgba(122, 107, 100, 1) 5%, rgba(122, 107, 100, 1) 95%, rgba(59, 45, 27, 1) 100%);
    color: #ede2cf;
}

.select-wrapper {
    width: 100%;
    border-color: #ede2cf;
}

.username {
    margin-left: auto;
}

.select,
.button {
    width: 100%;
    background-color: #2d2620;
    color: #ede2cf;
    border-radius: 10px;
    border-color: #af844a;
    padding: 7px;
}

.information {
    display: flex;
    flex-direction: column;
    margin: 10px;
    width: 100%;
}

.information input {
    background-color: #2d2620;
    color: #ede2cf;
    border-radius: 10px;
    border-color: #af844a;
    padding: 7px;
}

.cart-items {
    display: flex;
    flex-direction: column;
    margin: 10px;
    width: 100%;
}

.order-item {
    margin-top: 10px;
}

.order-info {
    display: flex;
    flex-direction: column;
}

.order-item-list,
.order-info {
    width: 100%;
}

button:disabled {
    background-color: grey;
}
</style>