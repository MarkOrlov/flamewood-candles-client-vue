<template>
    <div class="body">
        <div class="select-wrapper">
            Продукт:
            <select v-model="product" class="select">
                <option v-for="option in prodOptions" :value="option.value" :key="option.id">
                    {{ option.text }}
                </option>
            </select>
        </div>
        <div v-if="product" class="select-wrapper">
            Аромат:
            <select v-model="smell" class="select">
                <option v-for="option in smellOptions" :value="option.value" :key="option.id">
                    {{ option.text }}
                </option>
            </select>
        </div>

    </div>
</template>

<script>

import { useTelegram } from "../../hooks/useTelegram";

const { user, tg, queryId } = useTelegram();

export default {
    data() {
        return {
            userName: user?.first_name || 'UserNamePlaceholder',
            product: undefined,
            prodOptions: [
                { text: 'Свеча 50мл', value: 'A', id: 1 },
                { text: 'Аромадиффузор', value: 'B', id: 2 },
                { text: 'Румспрей', value: 'C', id: 3 }
            ],
            smell: undefined,
            smellOptions: [
                { text: 'Томатный лист', value: 'A', id: 1 },
                { text: 'Табак и ваниль', value: 'B', id: 2 },
                { text: 'Бэбра', value: 'C', id: 3 }
            ]
        }
    },
    mounted() {
        tg.MainButton.show();
        tg.MainButton.setParams({
            text: `Купить`
        });
        tg.onEvent('mainButtonClicked', this.onSendData);
    },
    methods: {
        onSendData() {
            const data = {
                user: user,
                queryId,
                product: this.product
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
</style>