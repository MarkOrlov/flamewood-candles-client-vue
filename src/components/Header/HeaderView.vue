<template>
    <div>
        Header
        {{ userName }}
    </div>
</template>

<script>

import { useTelegram } from "../../hooks/useTelegram";

const { user } = useTelegram();

// const tg = window.Telegram.WebApp;
// const user = tg.initDataUnsafe?.user;

console.log(user);
console.log('user');

export default {
    data() {
        return {
            userName: user?.first_name || 'UserName',
        }
    },
}

</script>
<style>
.header {
    width: 100%;
    height: 50px;
    display: flex;
    align-items: center;
    padding: 0 20px;

}

.username {
    margin-left: auto;
}
</style>