<template>
    <div class="body">
        <div class="cart-items" v-for="item in cartItems" :key="item.id">
            <div class="select-wrapper">
                Продукт:
                <select v-model="item.product" class="select" v-on:change="fetchSmell(item)">
                    <option v-for="option in productOptions" :value="option.id" :key="option.id">
                        {{ option.name }}
                    </option>
                </select>
            </div>
            <div v-if="item.product" class="select-wrapper">
                Аромат:
                <select v-model="item.smell" class="select">
                    <option v-for="option in item.smellOptions" :value="option.id" :key="option.id">
                        {{ option.name }}
                    </option>
                </select>
            </div>
        </div>
        <button type="button" v-on:click="increaseProdItems">+</button>
        <div class="information">
            <label for="name">ФИО</label>
            <input type="text" name="name" id="name" v-model="customerInformation.name">

            <label for="postIndex">Почтовый индекс</label>
            <input type="text" name="postIndex" id="postIndex" v-model="customerInformation.postIndex">

            <label for="address">Адрес доставки</label>
            <input type="text" name="address" id="address" v-model="customerInformation.address">

        </div>

    </div>
</template>

<script>

import { useTelegram } from "../../hooks/useTelegram";

// import { toRaw } from 'vue';
import axios from 'axios';

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
            }
        }
    },
    mounted() {
        tg.MainButton.show();
        tg.MainButton.setParams({
            text: `Купить`
        });
        tg.onEvent('mainButtonClicked', this.onSendData);

        this.fetchProduct();
    },
    methods: {
        async fetchProduct() {

            try {
                const response = await axios.get('http://localhost:8000/product/'); // url on back
                this.productOptions = response.data;
            } catch (error) {
                alert(`Ошибка: ` + error)
            }
        },
        async fetchSmell(item) {
            console.log(item.product);

            try {
                const response = await axios.get('http://localhost:8000/smell/' + item.product); // url on back
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

        onSendData() {
            const data = {
                user: user,
                queryId,
                cartItems: this.cartItems,
                customerInformation: this.customerInformation
            }

            fetch('http://localhost:8000/newOrder', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                },
                body: JSON.stringify(data)
            })
        },
    },
}

</script>
<style>
.body {
    width: 100%-20px;
    display: flex;
    align-items: center;
    margin: 10px 0;
    border: 2px solid black;
    padding: 10px;
    display: flex;
    flex-direction: column;

}

.select-wrapper {
    width: 100%;
    margin: 10px;
}

.username {
    margin-left: auto;
}

.information {
    display: flex;
    flex-direction: column;
}

.cart-items {
    display: flex;
    flex-direction: column;
}
</style>