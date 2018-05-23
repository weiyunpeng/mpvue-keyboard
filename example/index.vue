<template>
    <keyboard :title="'输入车牌号'" @keyboard="change"></keyboard>
</template>

<script>
import Keyboard from '@/src/keyboard';
export default {
    components: {
        keyboard: Keyboard
    },
    data() {
        return {};
    },
    computed: {},
    methods: {
        change(msg) {
            console.log(msg);
        }
    },
    mounted() {}
};
</script>
