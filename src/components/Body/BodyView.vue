<template>
    <div class="body">
        Привет, {{ userName }}!
        Это бодик
    </div>
</template>

<script>

import { useTelegram } from "../../hooks/useTelegram";

const { user, tg } = useTelegram();

tg.sendData(JSON.stringify({ a: 'a', b: 'b' }));

export default {
    data() {
        return {
            userName: user?.first_name || 'UserNamePlaceholder',
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