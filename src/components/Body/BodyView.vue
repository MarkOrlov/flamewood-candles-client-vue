<template>
    <div class="body">
        <select v-model="product">
            <option v-for="option in prodOptions" :value="option.value" :key="option.id">
                {{ option.text }}
            </option>
        </select>
    </div>
</template>

<script>

import { useTelegram } from "../../hooks/useTelegram";

const { user, tg, queryId } = useTelegram();

const onSendData = () => {
    const data = {
        product: this.product,
        user: user,
        queryId
    }

    fetch('http://localhost:8000', {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json',
        },
        body: JSON.stringify(data)
    })
};

tg.MainButton.show();
tg.MainButton.setParams({
    text: `Купить`
});
tg.onEvent('mainButtonClicked', onSendData);


export default {
    data() {
        return {
            userName: user?.first_name || 'UserNamePlaceholder',
            product: '',
            prodOptions: [
                { text: 'Один', value: 'A', id: 1 },
                { text: 'Два', value: 'B', id: 2 },
                { text: 'Три', value: 'C', id: 3 }
            ]
        }
    },
    methods: {
        onSendData() {
            const data = {
                product: this.product,
                user: user,
                queryId
            }

            fetch('http://localhost:8000', {
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
    height: 50px;
    display: flex;
    align-items: center;
    padding: 0 20px;
    margin: 10px 0;
    border: 2px solid black;

}

.username {
    margin-left: auto;
}
</style>