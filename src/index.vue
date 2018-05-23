<template>
    <div class="kb" :style="'background:'+bgcolor">
        <div class="kb-input row-center">
            <text class="row-center kb-input__text">{{title}}</text>
        </div>
        <div class="row-center">
            <ul class="kb-input kb-input__ul row-around" @tap="showKeyboard">
                <li v-for="(item , index) in textBaseArr" :key="index" class="row-center kb-input__li" :style="index == textArr.length ? 'border-color:'+activeBorColor : 'border-color:'+baseBorColor" :class="index === 7 && textBaseArr[7] === ''? 'kb-input__new-energy' : '' ">{{item}}</li>
            </ul>
        </div>

        <div class="kb-keyboard">
            <div class="kb-keyboard__over row-wrap" v-if="isKeyboard" @tap="closeKeyboard"></div>
            <div class="kb-keyboard__panle row-wrap" v-if="isKeyboard" :animation="animationData">
                <!--省份简写键盘-->
                <div v-if="!isAlph" @tap="tapKeyboard" :data-index="i" :data-val="proItem" class="kb-keyboard__td kb-keyboard__td-theme row-center" v-for="(proItem , i) in keyboardValue.province" :key="proItem" hover-class="kb-keyboard__td-tap-theme" hover-start-time="0" hover-stay-time="80">{{proItem}}</div>

                <!--数字字母键盘-->
                <div v-if="isAlph && isNum" @tap="tapKeyboard" :data-index="j" :data-val="numItem" class="kb-keyboard__td-num kb-keyboard__td-theme row-center" v-for="(numItem, j) in keyboardValue.number" :key="numItem" hover-class="kb-keyboard__td-tap-theme" hover-start-time="0" hover-stay-time="80">{{numItem}}</div>
                <div v-if="isAlph && !isNum" class="kb-keyboard__td-num kb-keyboard__td-tap-theme row-center" v-for="(numItem, j) in keyboardValue.number" :key="numItem">{{numItem}}</div>

                <!--字母键盘-->
                <div v-if="isAlph" @tap="tapKeyboard" :data-index="k" :data-val="alItem" class="kb-keyboard__td-num kb-keyboard__td-theme row-center" v-for="(alItem , k) in keyboardValue.alph" :key="alItem" hover-class="kb-keyboard__td-tap-theme" hover-start-time="0" hover-stay-time="80">
                    <!-- 添加删除icon -->
                    <div :data-index="k" :data-val="alItem" v-if="k === 19" class="kb-keyboard__del"></div>
                    <div :data-index="k" :data-val="alItem" v-else="k !== 19">
                        {{alItem}}
                    </div>
                </div>

                <!-- 完成按钮 -->
                <div v-if="isAlph && textArr.length > 6" @tap="tapFinished" class="kb-keyboard__td kb-keyboard__td-theme kb-keyboard__finished row-center" hover-class="kb-keyboard__td-tap-fin-theme" hover-start-time="0" hover-stay-time="80">完成</div>
                <div v-if="isAlph" class="kb-keyboard__td kb-keyboard__finished-base row-center">完成</div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    props: {
        title: {
            type: String,
            default: '输入或拍照录入车牌'
        },
        bgcolor: {
            type: String,
            default: '#ffffff'
        },
        baseBorColor: {
            type: String,
            default: '#cccccc'
        },
        activeBorColor: {
            type: String,
            default: '#ff7149'
        }
    },
    data() {
        return {
            isKeyboard: true,
            isNum: false,
            isAlph: false,
            animationData: {},
            animation: {},
            keyboardValue: {
                province:
                    '京津沪冀豫云辽黑湘皖鲁新苏浙赣鄂桂甘晋蒙陕吉闽贵粤川青藏琼宁渝',
                number: '1234567890',
                alph: 'QWERTYUIOPASDFGHJKL巛ZXCVBNM'
            },
            textBaseArr: ['', '', '', '', '', '', '', ''],
            textArr: []
        };
    },
    computed: {},
    methods: {
        showKeyboard() {
            this.isKeyboard = true;
            this.animation.height('488rpx').step();
            this.animationData = this.animation.export();
        },
        closeKeyboard() {
            this.animation.height(0).step();
            this.animationData = this.animation.export();
            setTimeout(() => {
                this.isKeyboard = false;
            });
        },
        tapKeyboard(e) {
            let tapVal = e.target.dataset.val;

            if (tapVal === '巛') {
                this.textArr.pop();
                this.textBaseArr.splice(this.textArr.length, 1, '');
            } else {
                if (this.textArr.length > 7) return false;
                this.textArr.push(tapVal);
                for (let index = 0; index < this.textArr.length; index++) {
                    const element = this.textArr[index];
                    this.textBaseArr.splice(index, 1, element);
                }
            }

            if (this.textArr.length === 1) {
                this.isAlph = true;
                this.isNum = false;
            } else if (this.textArr.length === 0) {
                this.isAlph = false;
                this.isNum = false;
            } else {
                this.isNum = true;
            }
        },
        tapFinished(e) {
            let textValue = this.textArr.join('');
            this.$emit('keyboard', textValue);
        }
    },
    mounted() {
        let animation = wx.createAnimation({
            transformOrigin: '50% 50%',
            duration: 200,
            timingFunction: 'linear',
            delay: 0
        });
        this.animation = animation;
    }
};
</script>
<style lang="scss">
@import 'node_modules/sass-bem/bem';
$bem-component-namespace: 'kb';
.kb {
    width: 100%;
    height: 100%;
}

@include c('input') {
    width: 100%;
    height: 120rpx;
    font-size: 36rpx;
    position: relative;
    top: 100rpx;

    @include e('text') {
        width: 90%;
        height: 100rpx;
        padding: 20rpx;
        border-radius: 5rpx;
        color: #bdc3c7;
        z-index: 10;
    }

    @include e('ul') {
        width: 90%;
        height: 72rpx;
    }
    @include e('li') {
        width: 52rpx;
        height: 68rpx;
        border: 5rpx solid #cccccc;
    }

    @include e('active') {
        border-color: #24c6dc;
    }
    @include e('new-energy') {
        width: 52rpx;
        height: 68rpx;
        background: url(data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAACcAAAAvCAIAAADvkkQPAAAAGXRFWHRTb2Z0d2FyZQBBZG9iZSBJbWFnZVJlYWR5ccllPAAAAyZpVFh0WE1MOmNvbS5hZG9iZS54bXAAAAAAADw/eHBhY2tldCBiZWdpbj0i77u/IiBpZD0iVzVNME1wQ2VoaUh6cmVTek5UY3prYzlkIj8+IDx4OnhtcG1ldGEgeG1sbnM6eD0iYWRvYmU6bnM6bWV0YS8iIHg6eG1wdGs9IkFkb2JlIFhNUCBDb3JlIDUuNi1jMTM4IDc5LjE1OTgyNCwgMjAxNi8wOS8xNC0wMTowOTowMSAgICAgICAgIj4gPHJkZjpSREYgeG1sbnM6cmRmPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5LzAyLzIyLXJkZi1zeW50YXgtbnMjIj4gPHJkZjpEZXNjcmlwdGlvbiByZGY6YWJvdXQ9IiIgeG1sbnM6eG1wPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvIiB4bWxuczp4bXBNTT0iaHR0cDovL25zLmFkb2JlLmNvbS94YXAvMS4wL21tLyIgeG1sbnM6c3RSZWY9Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC9zVHlwZS9SZXNvdXJjZVJlZiMiIHhtcDpDcmVhdG9yVG9vbD0iQWRvYmUgUGhvdG9zaG9wIENDIDIwMTcgKFdpbmRvd3MpIiB4bXBNTTpJbnN0YW5jZUlEPSJ4bXAuaWlkOkM2MUE5QjNENTRCRDExRTdBMUIwRkM2MDIzNkY5MkY2IiB4bXBNTTpEb2N1bWVudElEPSJ4bXAuZGlkOkM2MUE5QjNFNTRCRDExRTdBMUIwRkM2MDIzNkY5MkY2Ij4gPHhtcE1NOkRlcml2ZWRGcm9tIHN0UmVmOmluc3RhbmNlSUQ9InhtcC5paWQ6QzYxQTlCM0I1NEJEMTFFN0ExQjBGQzYwMjM2RjkyRjYiIHN0UmVmOmRvY3VtZW50SUQ9InhtcC5kaWQ6QzYxQTlCM0M1NEJEMTFFN0ExQjBGQzYwMjM2RjkyRjYiLz4gPC9yZGY6RGVzY3JpcHRpb24+IDwvcmRmOlJERj4gPC94OnhtcG1ldGE+IDw/eHBhY2tldCBlbmQ9InIiPz6FoHPwAAAEMklEQVR42uyXV0tjURSF58ZYYu+xYcPeUCEiiIhCXiwv+g/8d77pg74IIhZU7Bi7aOy9dxOT+cyWY6YwmIwjjNz7cDn3nH3W2nvtfcrV3G73t09/NJ1VZ9VZddavw3p3d8fbZDJpmvZ5rB0dHS6Xq7W1NTg42I/pRv8ken5+/pvUGPybZjQaAwICvOX1yQkfYnU6nQra7XkcDocQGwwGnKDnnWl+b17JIrnc3t4mkUAzCyY6aTw+Pubm5pLjj68mzBYWFq6urgIDAwlramoK7tLSUslxTExMfn7+xysMR1FRkfpcW1sj0LKysqCgIOXW+1eRD3n1BiW+Xzv/+cp5eHiQpPq5BPybVlxcLOtH3/11Vp1VZ/2KrLKvcor9wZqjFAPZCOVI/2lTBIRz1wfWm5sbu91eUFAgQE9PTycnJ1tbW5GRkeqM6+npqaioiIuL45RdXl4ODw9PS0sTdyHjPTExERERwSG/vr5+fX0tdwzeDs8DOGgvGzimFxcXs7OzjM3Pz5+enoaEhCQmJo6OjmIRFRVFWyhnZmbkHO3t7W1qauLYwZLbBZYlJSWFhYWEnpGR0d3djcd0wgEm8mCJKxyO3IHeYr2/v5+enmYMOy4JmZmZWBABYSlNDg4Ourq6amtraQ8MDNTU1DCRs51YzWYzbwjkrKV/f38fQKvVenl5SSQIk5eXxzs6OloO/xcx+c7OzkYx7kToQHtjYwNiXAYLBzFAzPr6et70NDc34yIC4hYugrK0tIQwpAbZhIA2Muzt7SUkJNAmU4eHh42NjeLZCytj5APTzs5OpEDA1NTUyclJm80G5e3tbUNDQ05ODjSbm5tMQ4a6urrV1dWRkZGdnR38W1xcbGtrY6i/v5+MWiwWmDCjJywsTP4VlLyvrMnJyeDi/tzcHFch/iPAqqqqysrKGh8fJ2f4y7SVlZXq6mrgBgcHEQNVAQIahJaWFkl/e3v77u6ulDd15PQ8qKos31iRBUTGjo6OiACdgcZH6KkXGnwiLG+qGiyCYwj0lJSU2NhY5lIy6naOWpQkaKSDz/Pzc+yZLtX7xspKkGRw4UtPT4+Pj0cxufcqB5kJSnl5OZ1nZ2eknKGxsTEZRQOF2NfXR2XgLjh4QBgUFwb0/MBKDSMaqZJVi+9YIKD3tkWIXIaHh4elTPCSfONZZWUl6GQHEAQgRxQqkVEH8NGPrwRzfHxMvqlHYnv9zwGOfYBvGoiWlJSERPKzpi6CxMpkXCbfaAMc/axO7t/kjKApY9YJC4Y6QFWIWRQYUBAIg4Qkm6BfY2Wy2fMMDQ0RB9ykCgvCBU79QvGGFQOiIYvYA83CBRqHcIKgIWAtERzVx/bCeiX3+EFemMun7F+ayhwN5hAfNLKXSojeN3rQGYUAM9lsoScOKZzfbr+M4npoaKj3n4sm/0k+/Sz8xyfddwEGACwdBlNWk8W0AAAAAElFTkSuQmCC);
        background-size: 100% 100%;
        border: 2px dashed #cccccc;
    }
}

@include c('keyboard') {
    @include e('over') {
        z-index: 8888;
        position: fixed;
        top: 0;
        width: 100%;
        height: 100%;
        overflow: hidden;
    }

    @include e('panle') {
        position: absolute;
        bottom: 0;
        height: 488rpx;
        width: 100%;
        z-index: 9999;
        box-sizing: border-box;
        padding: 0 10rpx;
        border-bottom: 15rpx solid #eaf1f9;
        border-top: 20rpx solid #eaf1f9;

        --from: #eaf1f9;
        --to: #eaf1f9;

        background: linear-gradient(130deg, var(--from), var(--to));
    }

    @include e('td') {
        flex-grow: 1;
        -webkit-flex-grow: 1;
        font-size: 36rpx;
        height: 100rpx;
        flex: 1 1 10%;
        -webkit-flex: 1 1 10%;
        margin: 5rpx;
        border-radius: 10rpx 10rpx 10rpx 10rpx;
    }

    @include e('td-num') {
        flex-grow: 1;
        -webkit-flex-grow: 1;
        font-size: 36rpx;
        height: 100rpx;
        flex: 1 1 8%;
        -webkit-flex: 1 1 8%;
        margin: 5rpx;
        border-radius: 10rpx 10rpx 10rpx 10rpx;
    }

    @include e('del') {
        width: 100%;
        height: 100%;
        background: url(data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAakAAAKuCAYAAAAbweN+AAAACXBIWXMAAAsTAAALEwEAmpwYAAAKTWlDQ1BQaG90b3Nob3AgSUNDIHByb2ZpbGUAAHjanVN3WJP3Fj7f92UPVkLY8LGXbIEAIiOsCMgQWaIQkgBhhBASQMWFiApWFBURnEhVxILVCkidiOKgKLhnQYqIWotVXDjuH9yntX167+3t+9f7vOec5/zOec8PgBESJpHmomoAOVKFPDrYH49PSMTJvYACFUjgBCAQ5svCZwXFAADwA3l4fnSwP/wBr28AAgBw1S4kEsfh/4O6UCZXACCRAOAiEucLAZBSAMguVMgUAMgYALBTs2QKAJQAAGx5fEIiAKoNAOz0ST4FANipk9wXANiiHKkIAI0BAJkoRyQCQLsAYFWBUiwCwMIAoKxAIi4EwK4BgFm2MkcCgL0FAHaOWJAPQGAAgJlCLMwAIDgCAEMeE80DIEwDoDDSv+CpX3CFuEgBAMDLlc2XS9IzFLiV0Bp38vDg4iHiwmyxQmEXKRBmCeQinJebIxNI5wNMzgwAABr50cH+OD+Q5+bk4eZm52zv9MWi/mvwbyI+IfHf/ryMAgQAEE7P79pf5eXWA3DHAbB1v2upWwDaVgBo3/ldM9sJoFoK0Hr5i3k4/EAenqFQyDwdHAoLC+0lYqG9MOOLPv8z4W/gi372/EAe/tt68ABxmkCZrcCjg/1xYW52rlKO58sEQjFu9+cj/seFf/2OKdHiNLFcLBWK8ViJuFAiTcd5uVKRRCHJleIS6X8y8R+W/QmTdw0ArIZPwE62B7XLbMB+7gECiw5Y0nYAQH7zLYwaC5EAEGc0Mnn3AACTv/mPQCsBAM2XpOMAALzoGFyolBdMxggAAESggSqwQQcMwRSswA6cwR28wBcCYQZEQAwkwDwQQgbkgBwKoRiWQRlUwDrYBLWwAxqgEZrhELTBMTgN5+ASXIHrcBcGYBiewhi8hgkEQcgIE2EhOogRYo7YIs4IF5mOBCJhSDSSgKQg6YgUUSLFyHKkAqlCapFdSCPyLXIUOY1cQPqQ28ggMor8irxHMZSBslED1AJ1QLmoHxqKxqBz0XQ0D12AlqJr0Rq0Hj2AtqKn0UvodXQAfYqOY4DRMQ5mjNlhXIyHRWCJWBomxxZj5Vg1Vo81Yx1YN3YVG8CeYe8IJAKLgBPsCF6EEMJsgpCQR1hMWEOoJewjtBK6CFcJg4Qxwicik6hPtCV6EvnEeGI6sZBYRqwm7iEeIZ4lXicOE1+TSCQOyZLkTgohJZAySQtJa0jbSC2kU6Q+0hBpnEwm65Btyd7kCLKArCCXkbeQD5BPkvvJw+S3FDrFiOJMCaIkUqSUEko1ZT/lBKWfMkKZoKpRzame1AiqiDqfWkltoHZQL1OHqRM0dZolzZsWQ8ukLaPV0JppZ2n3aC/pdLoJ3YMeRZfQl9Jr6Afp5+mD9HcMDYYNg8dIYigZaxl7GacYtxkvmUymBdOXmchUMNcyG5lnmA+Yb1VYKvYqfBWRyhKVOpVWlX6V56pUVXNVP9V5qgtUq1UPq15WfaZGVbNQ46kJ1Bar1akdVbupNq7OUndSj1DPUV+jvl/9gvpjDbKGhUaghkijVGO3xhmNIRbGMmXxWELWclYD6yxrmE1iW7L57Ex2Bfsbdi97TFNDc6pmrGaRZp3mcc0BDsax4PA52ZxKziHODc57LQMtPy2x1mqtZq1+rTfaetq+2mLtcu0W7eva73VwnUCdLJ31Om0693UJuja6UbqFutt1z+o+02PreekJ9cr1Dund0Uf1bfSj9Rfq79bv0R83MDQINpAZbDE4Y/DMkGPoa5hpuNHwhOGoEctoupHEaKPRSaMnuCbuh2fjNXgXPmasbxxirDTeZdxrPGFiaTLbpMSkxeS+Kc2Ua5pmutG003TMzMgs3KzYrMnsjjnVnGueYb7ZvNv8jYWlRZzFSos2i8eW2pZ8ywWWTZb3rJhWPlZ5VvVW16xJ1lzrLOtt1ldsUBtXmwybOpvLtqitm63Edptt3xTiFI8p0in1U27aMez87ArsmuwG7Tn2YfYl9m32zx3MHBId1jt0O3xydHXMdmxwvOuk4TTDqcSpw+lXZxtnoXOd8zUXpkuQyxKXdpcXU22niqdun3rLleUa7rrStdP1o5u7m9yt2W3U3cw9xX2r+00umxvJXcM970H08PdY4nHM452nm6fC85DnL152Xlle+70eT7OcJp7WMG3I28Rb4L3Le2A6Pj1l+s7pAz7GPgKfep+Hvqa+It89viN+1n6Zfgf8nvs7+sv9j/i/4XnyFvFOBWABwQHlAb2BGoGzA2sDHwSZBKUHNQWNBbsGLww+FUIMCQ1ZH3KTb8AX8hv5YzPcZyya0RXKCJ0VWhv6MMwmTB7WEY6GzwjfEH5vpvlM6cy2CIjgR2yIuB9pGZkX+X0UKSoyqi7qUbRTdHF09yzWrORZ+2e9jvGPqYy5O9tqtnJ2Z6xqbFJsY+ybuIC4qriBeIf4RfGXEnQTJAntieTE2MQ9ieNzAudsmjOc5JpUlnRjruXcorkX5unOy553PFk1WZB8OIWYEpeyP+WDIEJQLxhP5aduTR0T8oSbhU9FvqKNolGxt7hKPJLmnVaV9jjdO31D+miGT0Z1xjMJT1IreZEZkrkj801WRNberM/ZcdktOZSclJyjUg1plrQr1zC3KLdPZisrkw3keeZtyhuTh8r35CP5c/PbFWyFTNGjtFKuUA4WTC+oK3hbGFt4uEi9SFrUM99m/ur5IwuCFny9kLBQuLCz2Lh4WfHgIr9FuxYji1MXdy4xXVK6ZHhp8NJ9y2jLspb9UOJYUlXyannc8o5Sg9KlpUMrglc0lamUycturvRauWMVYZVkVe9ql9VbVn8qF5VfrHCsqK74sEa45uJXTl/VfPV5bdra3kq3yu3rSOuk626s91m/r0q9akHV0IbwDa0b8Y3lG19tSt50oXpq9Y7NtM3KzQM1YTXtW8y2rNvyoTaj9nqdf13LVv2tq7e+2Sba1r/dd3vzDoMdFTve75TsvLUreFdrvUV99W7S7oLdjxpiG7q/5n7duEd3T8Wej3ulewf2Re/ranRvbNyvv7+yCW1SNo0eSDpw5ZuAb9qb7Zp3tXBaKg7CQeXBJ9+mfHvjUOihzsPcw83fmX+39QjrSHkr0jq/dawto22gPaG97+iMo50dXh1Hvrf/fu8x42N1xzWPV56gnSg98fnkgpPjp2Snnp1OPz3Umdx590z8mWtdUV29Z0PPnj8XdO5Mt1/3yfPe549d8Lxw9CL3Ytslt0utPa49R35w/eFIr1tv62X3y+1XPK509E3rO9Hv03/6asDVc9f41y5dn3m978bsG7duJt0cuCW69fh29u0XdwruTNxdeo94r/y+2v3qB/oP6n+0/rFlwG3g+GDAYM/DWQ/vDgmHnv6U/9OH4dJHzEfVI0YjjY+dHx8bDRq98mTOk+GnsqcTz8p+Vv9563Or59/94vtLz1j82PAL+YvPv655qfNy76uprzrHI8cfvM55PfGm/K3O233vuO+638e9H5ko/ED+UPPR+mPHp9BP9z7nfP78L/eE8/sl0p8zAAAAIGNIUk0AAHolAACAgwAA+f8AAIDpAAB1MAAA6mAAADqYAAAXb5JfxUYAAA+6SURBVHja7N3NUVtpGobhxxE0jsAigoadd6OOwDiCbkfQEAE4gsERDB2B5Qha7L5dyxEYR9AiAvdCx1UeBo/1e3ilc11VWpgyuPwU6Ob8CJ59+fIlAFDRM5ECQKQAQKQAECkAECkARAoARAoARAoAkQIAkQJApABApABApAAQKQAQKQBECgBECgBECgCRAgCRAkCkAECkAECkABApABApAEQKAEQKAEQKAJECAJECQKQAQKQAECkAECkAECkARAoARAoAkQIAkQIAkQJApABApAAQKQAQKQAQKQBECgBECgCRAgCRAgCRAkCkAECkABApABApABApAEQKAEQKAJECAJECQKSsAIBIAYBIASBSACBSAIgUAIgUAIgUACIFACIFgEgBgEgBgEgBIFIAIFIAiBQAiBQAiBQAIgUAIgWASAGASAGASAEgUgAgUgCIFACIFAAiJVIAiBQAiBQAIgUAIgWASAGASAGASAEgUgAgUgCIFACIFACIFAAiBQAiBYBIAYBIAYBIASBSACBSAIgUAIgUAIgUACIFACIFgEgBgEgBgEgBIFIAIFIAiBQAiBQAIgUAIgUAIgWASAGASAEgUgAgUgAgUgCIFACIFAAiBQAiBQAiBYBIAYBIASBSACBSACBSAIgUAIgUACIFACIFACIFgEgBgEgBIFIAIFIAiJQVABApABApAEQKAEQKAJECAJECAJECQKQAQKQAECkAECkAECkARAoARAoAkQIAkQIAkQJApABApAAQKQAQKQAQKQBECgBECgCRAgCRAkCkRAoAkQIAkQJApABApAAQKQAQKQAQKQBECgBECgCRAgCRAgCRAkCkAECkABApABApABApAEQKAEQKAJECAJECAJECQKQAQKQAECkAECkAECkARAoARAoAkQIAkQJApABApABApAAQKQAQKQBECgBECgBECgCRAgCRAkCkAECkAECkABApABApAEQKAEQKAEQKAJECAJECQKQAQKQAQKQAECkAECkARAoARAoAkbICACIFACIFgEgBgEgBIFIAIFIAIFIAiBQAiBQAIgUAIgUAIgWASAGASAEgUgAgUgAgUtv28uVLI+zeUZJXSc6SnCQZmYTvmCa56x6zJB92+Y+11iwuUiI1YKMkl0l+MwUbhmvSBetOpERKpNiGyyTn3VEUbDNYN0n+ECmREinWcZTkfZKxKdihuy5W75LMRUqkRIplnCT509ETPZp3R+xrHVmJlEiJlEBBH6ZJLrK42UKkREqkEChKOs/iFKBIiZRIIVCUNEnyJktcqxIpkRIpgYKnMEvyy49CJVIiJVICBU9l3oVqJlIiJVICBXsXKpESKZESKKgQqtM88tMqREqkREqgoIJZHrlGJVIiJVICBVVMkrwWKZESKYGCqi6SXIuUSImUQEFVp+lupBApkRIpgYJqpllcnxIpkRIpgYKSLpJci5RIiZRAQUXzJMettbkpREqkBAoqettauzKDSImUQIGjKZFCpAQKVnTRWrs2g0iJlEBBRbPW2qkZREqkBAqqOm6t3ZlBpERKoKCid621czOIlEgJFFTklJ9IiZRAQWnP3eUnUiIlUFDV69baxAwiJVICBRV5Ya9IiZRAQVm3rbWxGURKpAQKKnLzhEiJlEBBXa21Z1YQKZESKBApkUKkBApESqRESqDgUPzSWpuaQaRESqBApESKAUZKoECkREqkBApECpESKYECkRIpkRIoQKRESqQECkRKpBhEpAQKREqkREqgQKQQKZESKBApkWKvIyVQIFIiJVICBSKFSImUQG3XfZJpklmSUZJxkhdmWdttt+eo+xz8WaQQKZESqPVcJLl+5O1nSW6S/GSipb1LcpVk/uDt427jQ42VSImUSAnUbp5cuu/4v2eUZDKAI4FteNNF/XuOuq0PcUuREimREqite9t91/8jh/zk2legvo3+J5FCpIYbKYFa3vP872kpodpdoL6aJHklUojU8CIlUMv72O21CqHaPFDpjl4vRQqRGlakBGo1t1lczF+VUG0WqGRxQ8p7kUKkhhMpgVrdLMnpmu8rVOsHypEUIjWwSAnU+o6T3AlVr4FKkr+y+qlWkRIp9jBSArWZSZLXG7z/EEO1aaDG3efsoREpkRIpgSr5pDukUG1jq08H+jkrUiIlUgIlVHu+0Z85vNN8IiVSIiVQQiVQIiVS7EOkBEqoBEqkRIqSkRIooRIokRIpSkZKoIRKoERKpCgZKYESKoESKZGiZKQESqgESqREipKREiihEiiREilKRkqghEqgREqkRKpkpARKqARKpBCpkpESKKESKJFCpEpGSqCESqBECpEqGSmBEiqBEilEqmSkBEqoBEqkEKmSkRIooeo7VAIlUiIlUgIlVCVDJVAiJVIiJVBCVTJUAiVSIiVSAiVUJUMlUCIlUiIlUJQMlUCJlEiJlEBRMlQCJVIiJVICRclQCZRIiZRILRUpgRKqvkMlUCIlUiwVKYESqr5DJVAiJVIsFSmBou9QCZRIiRRLRUqg6DtUAiVSIsVSkRIo+g6VQImUSLFUpASKvkMlUCIlUiwVKYGi71AJlEiJFEtFapTkL4Gix1CNk0wESqREih9Fyhc7TxGqTficFSmRGlCkbpL8ag32JFQCJVIiNaBIjbsveNiHUAmUSInUwCI1SfLKEuxBqARKpERqYIE6SvK3JdiDUAmUSInUACN1luS9JSgeKoESKZEaaKSuklxagsKhEiiREimRgpKhEiiREimREilKR2qa7f4qekRKpEQKgdrq6T6hEimRGmikzuLGCeoGSqhESqQGHqmjuAWd2oESKpESqYGHahIv5qV2oIRKpERqwJEax49Fon6ghEqkRGqgkUqS6yS/W4PigRIqkRKpgUbKFz37EiihEimRGmCkksUvPZwl+ckq9BCoo2znlx4KlUiJ1EAilSxe2T8VKnoI1NefJLHNX0WPSInUgUdKqOgzUNv8mEIlUiI1kEgJFX0GSqhESqRYOVJCRZ+BEiqREilWjpRQ0WeghEqkRIqVIyVU9BkooRIpkWLlSAmVQPUZKKESKZFi5UgJlUD1GSihEimRYuVICZVA9RkooRIpkWLlSAmVQPUZKKESKZESqZfrvJtQCVRfgRIqkRIpkVqLUAlUX4ESKpESKZESKoEqHSihEimREimhEqjSgRIqkRIpkRIqgSodKKESKZESKaESqNKBEiqREimREiqBKh0ooRIpkRIpoRKo0oESKpESKZESKoEqHSihEimREimhEqjSgRIqkRIpkRIqgSodKKESKZESKaESqMH8n4cYKpESqUFGSqgESqhESqQoHSmhEiihEimRonSkhEqghEqkRIrSkRIqgRIqkRIpSkdKqARKqERKpCgdKaESKKESKZGidKSESqD2datZkhcihUgdfqSEanMfkpwJVK+hGnebiRQiNYBICdVmjpPcCVTvoZom+ZdIIVLDiJRQredzkpFAPUmorpJcihQiNZxICdXqbrM49SRQ/YfqLMl7kUKkhhUpoVrNLMmpQD1JqBxJIVIDjZRQrWaVa1ICtb1QTZK8EilEapiREqrlve2+qxeo/kI1SvLpAP//IiVSIiVUu3ly6XYSqN2H6pC3FCmREimh2pmr7qjqobMk/+meXFnOdbfl/MHbx0n+fcCxFymREimh2ql5t9Msi1NS46x/izqLLafdhicDOBIVKZESKaECkRIpDjFSQgUiJVIiJVQgUoiUSAkViJRIcZCREioQKZESKaECkUKkREqoQKREioOMlFCBSImUSAkViBQiJVJCBSIlUhxkpIQKREqkREqoQKQQKZESKuhJa+2ZFURKpIQKREqkECmhApESKZESKjgEH1trJ2YQKZESKqjotrU2NoNIiZRQQUVvW2tXZhApkRIqqOh1a21iBpESKaGCip631uZmECmREiqoxk0TIiVSQgVlvWutnZtBpERKqKCi49banRlESqSECqpxqk+kREqooKw3rbUbM4iUSAkVVHOfZOSuPpESKaGCiryAV6RESqjAUZRIIVJCBat4k+SmtWYJkRIpoYJSbpOMk0SkREqkhAqqOU0yEymREimhgmouklx//YNIiZRICRVU8SHJ2bdvECmREimhggo+ZnEdai5SIiVSQgWV3CcZPQyUSImUSAkVVAjUON2NEiIlUiIlVFDF5yyuQc2+9xdESqRESqjgKTx6DUqkREqkhAqe2ockv/0oUCIlUiIlVNC3/3odlEiJlEghVFRwm+Q8/+f6k0iJlEghVPTtvovTzTrvLFIiJVLDc9SF6mdTsEOfk1wlmWSJa08iJVIixUPXSX43A1t22x013Wzjg4mUSInUsI2673Z/NQUbhmnSPe62+YFFSqREimRxCvCse5wkeWESHnGfxY0Pd91j2j12RqRECgCRAgCRAgCRAkCkAECkABApABApABApAEQKAEQKAJECAJECAJECQKQAQKQAECkAECkAECkARAoARAoAkQIAkQIAkQJApABApAAQKQAQKQBEygoAiBQAiBQAIgUAIgWASAGASAGASAEgUgAgUgCIFACIFACIFAAiBQAiBYBIAYBIAYBIASBSACBSAIgUAIgUAIgUACIFACIFgEgBgEgBIFIiBYBIAYBIASBSACBSAIgUAIgUAIgUACIFACIFgEgBgEgBgEgBIFIAIFIAiBQAiBQAiBQAIgUAIgWASAGASAGASAEgUgAgUgCIFACIFACIFAAiBQAiBYBIAYBIASBSACBSACBSAIgUAIgUACIFACIFACIFgEgBgEgBIFIAIFIAIFIAiBQAiBQAIgUAIgUAIgWASAGASAEgUgAgUgAgUgCIFACIFAAiBQAiBYBIWQEAkQIAkQJApABApAAQKQAQKQAQKQBECgBECgCRAgCRAgCRAkCkAECkABApABApABApAEQKAEQKAJECAJECAJECQKQAQKQAECkAECkAREqkABApABApAEQKAEQKAJECAJECAJECQKQAQKQAECkAECkAECkARAoARAoAkQIAkQIAkQJApABApAAQKQAQKQAQKQBECgBECgCRAgCRAgCRAkCkAECkABApABApAEQKAEQKAEQKAJECAJECQKQAQKQAQKQAECkAECkARAoARAoARAoAkQIAkQJApABApABApAAQKQAQKQBECgBECgBECgCRAgCRAkCkAECkABApKwAgUgAgUgCIFACIFAAiBQAiBQAiBYBIAYBIASBSACBSACBSAIgUAIgUACIFACIFACIFgEgBgEgBIFIAIFIAIFIAiBQAiBQAIgUAIgWASIkUACIFACIFgEgBgEgBIFIAIFIAIFIAiBQAiBQAIgUAIgUAIgWASAGASAEgUgAgUgAgUgCIFACIFAAiBQAiBQDf+GcAz3gyaMWLPusAAAAASUVORK5CYII=);
        background-size: 100% 100%;
    }

    @include e('finished') {
        position: relative;
        top: 5rpx;
        flex: 1 1 23%;
        -webkit-flex: 1 1 23%;
    }

    @include e('finished-base') {
        position: relative;
        top: 5rpx;
        flex: 1 1 23%;
        -webkit-flex: 1 1 23%;
        border: 1rpx solid #cdd0d5;
        color: #1e1e1e;
        --from: #e5e5e5;
        --to: #e5e5e5;

        background: linear-gradient(130deg, var(--from), var(--to));
    }

    @include e('td-theme') {
        border: 1rpx solid #cdd0d5;
        color: #1e1e1e;
        box-shadow: 5rpx 5rpx 2rpx #cdd0d5;
        background: #fff;
    }

    @include e('td-tap-theme') {
        border: 1rpx solid #cdd0d5;
        color: #1e1e1e;
        --from: #e5e5e5;
        --to: #e5e5e5;

        background: linear-gradient(130deg, var(--from), var(--to));
    }

    @include e('td-tap-fin-theme') {
        border: 1rpx solid #cdd0d5;
        color: #fff;
        --from: #cdd0d5;
        --to: #b8b8b9;

        background: linear-gradient(130deg, var(--from), var(--to));
    }
}
</style>
