<template>
    <div class="header">
        Привет, {{ userName }}!
    </div>
</template>

<script>

import { useTelegram } from "../../hooks/useTelegram";

const { user } = useTelegram();

export default {
    data() {
        return {
            userName: user?.first_name || 'Дорогой гость',
        }
    },
}

</script>
<style>
.header {
    width: 100%-20px;
    height: 50px;
    display: flex;
    align-items: center;
    padding: 0 20px;
    background: rgb(59, 45, 27);
    background: linear-gradient(0deg, rgba(59, 45, 27, 1) 0%, rgba(122, 107, 100, 1) 30%);
    color: #ede2cf;

}

.username {
    margin-left: auto;
}
</style>