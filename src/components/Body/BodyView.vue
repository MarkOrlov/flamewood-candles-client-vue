<template>
    <div class="body">
        Это бодик
        <button @click="onSendData">Send</button>
    </div>
</template>

<script>

import { useTelegram } from "../../hooks/useTelegram";

const { user, tg, queryId } = useTelegram();

const onSendData = () => {
    const data = {
        a: '1',
        b: '2',
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


// tg.sendData(JSON.stringify({ a: 'a', b: 'b' }));


export default {
    data() {
        return {
            userName: user?.first_name || 'UserNamePlaceholder',
        }
    },
    methods: {
        onSendData() {
            this.userName = 'asd';

            const data1 = {
                a: '1',
                b: '2',
                user: user
            };

            fetch('http://localhost:8000', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                },
                body: JSON.stringify(data1)
            })
        }
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