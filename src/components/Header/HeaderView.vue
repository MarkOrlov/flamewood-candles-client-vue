<template>
    <div>
        Header
        {{ user }}
    </div>
</template>
<script>

import { useTelegram } from "../../hooks/useTelegram";

const { user } = useTelegram();
console.log(user);


export default {
    data() {
        return {
            user: user,
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