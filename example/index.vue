<template>
    <keyboard :plateNum.sync="plateNum" @keyboard="keyboardChange" :show.sync="show" extraKey="立即支付" active-border="#6f85ff" base-border="#6f85ff"></keyboard>
    <button @click="bandChange">绑定无感支付</button>
</template>

<script>
import Keyboard from 'mpvue-keyboard';
export default {
    components: {
        keyboard: Keyboard
    },
    data() {
        return {
            plateNum: ''
        };
    },
    computed: {},
    methods: {
        keyboardChange(plate) {
            this.plateNum = plate;
        },
        async bandChange() {
            console.log(this.plateNum);
            if (this.plateNum && this.plateNum.length >= 7) {
                await this.navCar();
            } else {
                this.show = true;
            }
        },
        async navCar() {
            const { result } = await api.getAutoParkingInfo();
            const miniParams = {
                appId: 'wxbcad394b3d99dac9',
                path: 'pages/route/index',
                extraData: {
                    appid: result.appid,
                    sub_appid: result.sub_appid,
                    mch_id: result.mch_id,
                    sub_mch_id: result.sub_mch_id,
                    nonce_str: result.nonce_str,
                    sign_type: 'HMAC-SHA256',
                    sign: result.sign,
                    trade_scene: 'PARKING',
                    plate_number: this.plateNum,
                    sub_openid: result.sub_openid
                }
            };
            navigateToMiniProgram(miniParams).then(minires => {
                console.log(minires);
            });
        }
    },
    mounted() {}
};
</script>