<template>
  <div class="kb">
    <div class="flex-center">
      <ul class="kb-input kb-input__ul" @tap="showKeyboard">
        <li
          v-for="(item, index) in textBaseArr"
          :key="item"
          @tap="energy(index)"
          :style="index === textArr.length ? 'border-color:'+activeBorder : 'border-color:'+baseBorder"
          :class="[index === 7 && isEnergy? energyClass : '' , inputClass]"
        >{{item}}</li>
      </ul>
    </div>

    <div class="kb-keyboard">
      <div
        class="kb-keyboard__over row-wrap"
        v-show="show"
        @tap="closeKeyboard"
      ></div>
      <div
        class="kb-keyboard__panle row-wrap"
        :class="keyboardClass"
        v-show="show"
      >
        <!--省份简写键盘-->
        <div
          v-show="!isAlph"
          v-for="(proItem, i) in province "
          :key="i"
          @tap="tapKeyboard(proItem)"
          class="kb-keyboard__td"
          :class="keycapClass"
          hover-class="kb-keyboard__td-tap-theme"
          hover-start-time="0"
          hover-stay-time="40"
        >{{proItem}}</div>

        <!--数字字母键盘-->
        <button
          v-show="isAlph"
          v-for="(numItem, j) in natural "
          :key="j"
          @tap="tapKeyboard(numItem)"
          class="kb-keyboard__td-num"
          :class="naturalClass"
          hover-class="kb-keyboard__td-tap-theme"
          hover-start-time="0"
          hover-stay-time="40"
          :disabled="!isNum"
        >{{numItem}}</button>

        <!--字母键盘-->
        <div
          v-show="isAlph"
          v-for="(alItem , k) in 'QWERTYUPASDFGHJKLZX巛CVBNM' "
          :key="k"
          @tap="tapKeyboard(alItem)"
          class="kb-keyboard__td-num"
          :class="alphClass"
          hover-class="kb-keyboard__td-tap-theme"
          hover-start-time="0"
          hover-stay-time="40"
        >
          <text v-if="alItem === '巛'" :class="delClass"></text>
          <text v-else-if="k !== '巛'">{{alItem}}</text>
        </div>

        <!-- 完成按钮 -->
        <div
          v-if="isAlph && textArr.length > 6"
          @tap="tapFinished"
          class="kb-keyboard__td-finished"
          :class="finishedClass"
          hover-class="kb-keyboard__td-tap-fin-theme"
          hover-start-time="0"
          hover-stay-time="60"
        >{{extraKey}}</div>
        <div
          v-if="isAlph && textArr.length < 7"
          class="kb-keyboard__td-finished"
          :class="finishedBaseClass"
        >{{extraKey}}</div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    show: {
      type: Boolean,
      default: false
    },
    inputClass: {
      type: String,
      default: "kb-input__li flex-center"
    },
    energyClass: {
      type: String,
      default: "kb-input__new-energy"
    },
    keyboardClass: {
      type: String,
      default: "kb-keyboard__panle-style"
    },
    province: {
      type: String,
      default: "京津沪冀豫云辽黑湘皖鲁新苏浙赣鄂桂甘晋蒙陕吉闽贵粤川青藏琼宁渝"
    },
    keycapClass: {
      type: String,
      default: "kb-keyboard__td-theme"
    },
    natural: {
      type: String,
      default: "1234567890"
    },
    naturalClass: {
      type: String,
      default: "kb-keyboard__td-theme"
    },
    alphClass: {
      type: String,
      default: "kb-keyboard__td-theme"
    },
    delClass: {
      type: String,
      default: "kb-keyboard__del"
    },
    baseBorder: {
      type: String,
      default: "#cccccc"
    },
    activeBorder: {
      type: String,
      default: "#ff7149"
    },
    extraKey: {
      type: String,
      default: "完成"
    },
    plateNum: {
      type: String,
      default: ""
    },
    finishedClass: {
      type: String,
      default: "kb-keyboard__finished"
    },
    finishedBaseClass: {
      type: String,
      default: "kb-keyboard__finished-base"
    }
  },
  data() {
    return {
      isNum: false,
      isAlph: false,
      isEnergy: true,
      textBaseArr: ["", "", "", "", "", "", "", ""],
      textArr: [],
      tapVal: ""
    };
  },
  computed: {},
  methods: {
    showKeyboard() {
      this.show = true;
      this.$emit("update:show", true);
    },
    closeKeyboard() {
      this.show = false;
      this.$emit("update:show", false);
    },
    tapKeyboard(tapval) {
      this.tapVal = tapval;

      if (this.tapVal === "巛") {
        this.textArr.pop();
        this.textBaseArr.splice(this.textArr.length, 1, "");
        if (this.textArr.length === 7 && !this.isEnergy && this.tapVal !== "") {
          this.isEnergy = true;
          this.tapVal = "";
        }
      } else {
        if (this.textArr.length > 7) return false;
        if (this.textArr.length > 6 && this.isEnergy) {
          this.textBaseArr.splice(6, 1, this.tapVal);
          this.textArr.splice(6, 1, this.tapVal);
          this.$emit("update:plateNum", this.textArr.join(""));
          return false;
        }
        this.textArr.splice(this.textArr.length, 0, this.tapVal);
        this.textBaseArr.splice(this.textArr.length - 1, 1, this.tapVal);
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
      this.$emit("update:plateNum", this.textArr.join(""));
    },
    tapFinished(e) {
      let textValue = this.textArr.join("");
      this.$emit("keyboard", textValue);
    },
    energy(index) {
      if (index === 7 && this.isEnergy && this.textArr.length === 7) {
        this.isEnergy = false;
      }
    }
  },
  mounted() {}
};
</script>
<style>
.kb {
  width: 100%;
  height: 100%;
}
.kb-input {
  width: 100%;
  height: 60px;
  font-size: 18px;
  position: relative;
  top: 50px;
}
.kb-input__ul {
  display: flex;
  display: -webkit-flex;
  justify-content: space-around;
  -webkit-justify-content: space-around;
  width: 92%;
  height: 61px;
  z-index: 999;
}
.kb-input__li {
  width: 36px;
  height: 52px;
  border: 2px solid #cccccc;
  border-radius: 4px;
  background-color: #ffffff;
}
.kb-input__active {
  border: 1px solid #24c6dc;
}
.kb-input__new-energy {
  width: 42px;
  height: 54px;
  background: url(data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEEAAABQCAYAAABGfRh7AAAAGXRFWHRTb2Z0d2FyZQBBZG9iZSBJbWFnZVJlYWR5ccllPAAAA4RpVFh0WE1MOmNvbS5hZG9iZS54bXAAAAAAADw/eHBhY2tldCBiZWdpbj0i77u/IiBpZD0iVzVNME1wQ2VoaUh6cmVTek5UY3prYzlkIj8+IDx4OnhtcG1ldGEgeG1sbnM6eD0iYWRvYmU6bnM6bWV0YS8iIHg6eG1wdGs9IkFkb2JlIFhNUCBDb3JlIDUuNi1jMTM4IDc5LjE1OTgyNCwgMjAxNi8wOS8xNC0wMTowOTowMSAgICAgICAgIj4gPHJkZjpSREYgeG1sbnM6cmRmPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5LzAyLzIyLXJkZi1zeW50YXgtbnMjIj4gPHJkZjpEZXNjcmlwdGlvbiByZGY6YWJvdXQ9IiIgeG1sbnM6eG1wTU09Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC9tbS8iIHhtbG5zOnN0UmVmPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvc1R5cGUvUmVzb3VyY2VSZWYjIiB4bWxuczp4bXA9Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC8iIHhtcE1NOk9yaWdpbmFsRG9jdW1lbnRJRD0ieG1wLmRpZDphNWFhZmQ5YS05YjgzLTdlNDgtOWVkOS1iNGEzMjNmNmE1NGUiIHhtcE1NOkRvY3VtZW50SUQ9InhtcC5kaWQ6NEY0MjlCNjlFMEQ1MTFFOEExQjZFNUUzOEYyQjMzODgiIHhtcE1NOkluc3RhbmNlSUQ9InhtcC5paWQ6NEY0MjlCNjhFMEQ1MTFFOEExQjZFNUUzOEYyQjMzODgiIHhtcDpDcmVhdG9yVG9vbD0iQWRvYmUgUGhvdG9zaG9wIENDIDIwMTcgKFdpbmRvd3MpIj4gPHhtcE1NOkRlcml2ZWRGcm9tIHN0UmVmOmluc3RhbmNlSUQ9InhtcC5paWQ6YWRhMmJkZGUtZmE0Ny00MjQ5LWIwNjYtMWUwZTMxNzhkNDc4IiBzdFJlZjpkb2N1bWVudElEPSJhZG9iZTpkb2NpZDpwaG90b3Nob3A6Y2NjYmIwZGYtY2JhNS1hNjRjLWEwNWMt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);
  background-size: 100% 100%;
  border: none;
  border-radius: 5px;
}

.kb-keyboard {
  width: 100vw;
  background-color: #ffffff;
}

.kb-keyboard__over {
  z-index: 888;
  position: fixed;
  top: 0;
  width: 100%;
  height: 100%;
  overflow: hidden;
}
.kb-keyboard__panle {
  display: flex;
  display: -webkit-flex;
  align-items: center;
  -webkit-align-items: center;
  justify-content: center;
  -webkit-justify-content: center;
  box-sizing: border-box;
  -moz-box-sizing: border-box;
  -webkit-box-sizing: border-box;
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100vw;
  z-index: 999;
}
.kb-keyboard__panle-style {
  border-bottom: 8px solid #eaf1f9;
  border-top: 10px solid #eaf1f9;

  --from: #eaf1f9;
  --to: #eaf1f9;

  background: linear-gradient(130deg, var(--from), var(--to));
  background: -webkit-linear-gradient(130deg, var(--from), var(--to));
}
.kb-keyboard__td {
  display: flex;
  display: -webkit-flex;
  align-items: center;
  -webkit-align-items: center;
  justify-content: center;
  -webkit-justify-content: center;
  width: calc(100vw / 10);
  font-size: 18px;
  height: 48px;
  margin: 2px;
  border-radius: 5px;
}
.kb-keyboard__td-num {
  display: flex;
  display: -webkit-flex;
  align-items: center;
  -webkit-align-items: center;
  justify-content: center;
  -webkit-justify-content: center;
  flex-grow: 1;
  -webkit-flex-grow: 1;
  flex: 1 1 10%;
  -webkit-flex: 1 1 10%;
  max-width: 8.6vw;
  font-size: 18px;
  height: 48px;
  margin: 2px;
  border-radius: 5px;
}
.kb-keyboard__td-finished {
  display: flex;
  display: -webkit-flex;
  align-items: center;
  -webkit-align-items: center;
  justify-content: center;
  -webkit-justify-content: center;
  width: 110px;
  height: 48px;
  margin-top: 2px;
  margin-left: 10px;
  font-size: 18px;
  border-radius: 5px;
}
.kb-keyboard__del {
  width: 100%;
  height: 100%;
  background: url(data:image/png;base64,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0p8zAAAAIGNIUk0AAHolAACAgwAA+f8AAIDpAAB1MAAA6mAAADqYAAAXb5JfxUYAAA+6SURBVHja7N3NUVtpGobhxxE0jsAigoadd6OOwDiCbkfQEAE4gsERDB2B5Qha7L5dyxEYR9AiAvdCx1UeBo/1e3ilc11VWpgyuPwU6Ob8CJ59+fIlAFDRM5ECQKQAQKQAECkAECkARAoARAoARAoAkQIAkQJApABApABApAAQKQAQKQBECgBECgBECgCRAgCRAkCkAECkAECkABApABApAEQKAEQKAEQKAJECAJECQKQAQKQAECkAECkAECkARAoARAoAkQIAkQIAkQJApABApAAQKQAQKQAQKQBECgBECgCRAgCRAgCRAkCkAECkABApABApABApAEQKAEQKAJECAJECQKSsAIBIAYBIASBSACBSAIgUAIgUAIgUACIFACIFgEgBgEgBgEgBIFIAIFIAiBQAiBQAiBQAIgUAIgWASAGASAGASAEgUgAgUgCIFACIFAAiJVIAiBQAiBQAIgUAIgWASAGASAGASAEgUgAgUgCIFACIFACIFAAiBQAiBYBIAYBIAYBIASBSACBSAIgUAIgUAIgUACIFACIFgEgBgEgBgEgBIFIAIFIAiBQAiBQAIgUAIgUAIgWASAGASAEgUgAgUgAgUgCIFACIFAAiBQAiBQAiBYBIAYBIASBSACBSACBSAIgUAIgUACIFACIFACIFgEgBgEgBIFIAIFIAiJQVABApABApAEQKAEQKAJECAJECAJECQKQAQKQAECkAECkAECkARAoARAoAkQIAkQIAkQJApABApAAQKQAQKQAQKQBECgBECgCRAgCRAkCkRAoAkQIAkQJApABApAAQKQAQKQAQKQBECgBECgCRAgCRAgCRAkCkAECkABApABApABApAEQKAEQKAJECAJECAJECQKQAQKQAECkAECkAECkARAoARAoAkQIAkQJApABApABApAAQKQAQKQBECgBECgBECgCRAgCRAkCkAECkAECkABApABApAEQKAEQKAEQKAJECAJECQKQAQKQAQKQAECkAECkARAoARAoAkbICACIFACIFgEgBgEgBIFIAIFIAIFIAiBQAiBQAIgUAIgUAIgWASAGASAEgUgAgUgAgUtv28uVLI+zeUZJXSc6SnCQZmYTvmCa56x6zJB92+Y+11iwuUiI1YKMkl0l+MwUbhmvSBetOpERKpNiGyyTn3VEUbDNYN0n+ECmREinWcZTkfZKxKdihuy5W75LMRUqkRIplnCT509ETPZp3R+xrHVmJlEiJlEBBH6ZJLrK42UKkREqkEChKOs/iFKBIiZRIIVCUNEnyJktcqxIpkRIpgYKnMEvyy49CJVIiJVICBU9l3oVqJlIiJVICBXsXKpESKZESKKgQqtM88tMqREqkREqgoIJZHrlGJVIiJVICBVVMkrwWKZESKYGCqi6SXIuUSImUQEFVp+lupBApkRIpgYJqpllcnxIpkRIpgYKSLpJci5RIiZRAQUXzJMettbkpREqkBAoqettauzKDSImUQIGjKZFCpAQKVnTRWrs2g0iJlEBBRbPW2qkZREqkBAqqOm6t3ZlBpERKoKCid621czOIlEgJFFTklJ9IiZRAQWnP3eUnUiIlUFDV69baxAwiJVICBRV5Ya9IiZRAQVm3rbWxGURKpAQKKnLzhEiJlEBBXa21Z1YQKZESKBApkUKkBApESqRESqDgUPzSWpuaQaRESqBApESKAUZKoECkREqkBApECpESKYECkRIpkRIoQKRESqQECkRKpBhEpAQKREqkREqgQKQQKZESKBApkWKvIyVQIFIiJVICBSKFSImUQG3XfZJpklmSUZJxkhdmWdttt+eo+xz8WaQQKZESqPVcJLl+5O1nSW6S/GSipb1LcpVk/uDt427jQ42VSImUSAnUbp5cuu/4v2eUZDKAI4FteNNF/XuOuq0PcUuREimREqite9t91/8jh/zk2legvo3+J5FCpIYbKYFa3vP872kpodpdoL6aJHklUojU8CIlUMv72O21CqHaPFDpjl4vRQqRGlakBGo1t1lczF+VUG0WqGRxQ8p7kUKkhhMpgVrdLMnpmu8rVOsHypEUIjWwSAnU+o6T3AlVr4FKkr+y+qlWkRIp9jBSArWZSZLXG7z/EEO1aaDG3efsoREpkRIpgSr5pDukUG1jq08H+jkrUiIlUgIlVHu+0Z85vNN8IiVSIiVQQiVQIiVS7EOkBEqoBEqkRIqSkRIooRIokRIpSkZKoIRKoERKpCgZKYESKoESKZGiZKQESqgESqREipKREiihEiiREilKRkqghEqgREqkRKpkpARKqARKpBCpkpESKKESKJFCpEpGSqCESqBECpEqGSmBEiqBEilEqmSkBEqoBEqkEKmSkRIooeo7VAIlUiIlUgIlVCVDJVAiJVIiJVBCVTJUAiVSIiVSAiVUJUMlUCIlUiIlUJQMlUCJlEiJlEBRMlQCJVIiJVICRclQCZRIiZRILRUpgRKqvkMlUCIlUiwVKYESqr5DJVAiJVIsFSmBou9QCZRIiRRLRUqg6DtUAiVSIsVSkRIo+g6VQImUSLFUpASKvkMlUCIlUiwVKYGi71AJlEiJFEtFapTkL4Gix1CNk0wESqREih9Fyhc7TxGqTficFSmRGlCkbpL8ag32JFQCJVIiNaBIjbsveNiHUAmUSInUwCI1SfLKEuxBqARKpERqYIE6SvK3JdiDUAmUSInUACN1luS9JSgeKoESKZEaaKSuklxagsKhEiiREimRgpKhEiiREimREilKR2qa7f4qekRKpEQKgdrq6T6hEimRGmikzuLGCeoGSqhESqQGHqmjuAWd2oESKpESqYGHahIv5qV2oIRKpERqwJEax49Fon6ghEqkRGqgkUqS6yS/W4PigRIqkRKpgUbKFz37EiihEimRGmCkksUvPZwl+ckq9BCoo2znlx4KlUiJ1EAilSxe2T8VKnoI1NefJLHNX0WPSInUgUdKqOgzUNv8mEIlUiI1kEgJFX0GSqhESqRYOVJCRZ+BEiqREilWjpRQ0WeghEqkRIqVIyVU9BkooRIpkWLlSAmVQPUZKKESKZFi5UgJlUD1GSihEimRYuVICZVA9RkooRIpkWLlSAmVQPUZKKESKZESqZfrvJtQCVRfgRIqkRIpkVqLUAlUX4ESKpESKZESKoEqHSihEimREimhEqjSgRIqkRIpkRIqgSodKKESKZESKaESqNKBEiqREimREiqBKh0ooRIpkRIpoRKo0oESKpESKZESKoEqHSihEimREimhEqjSgRIqkRIpkRIqgSodKKESKZESKaESqMH8n4cYKpESqUFGSqgESqhESqQoHSmhEiihEimRonSkhEqghEqkRIrSkRIqgRIqkRIpSkdKqARKqERKpCgdKaESKKESKZGidKSESqD2datZkhcihUgdfqSEanMfkpwJVK+hGnebiRQiNYBICdVmjpPcCVTvoZom+ZdIIVLDiJRQredzkpFAPUmorpJcihQiNZxICdXqbrM49SRQ/YfqLMl7kUKkhhUpoVrNLMmpQD1JqBxJIVIDjZRQrWaVa1ICtb1QTZK8EilEapiREqrlve2+qxeo/kI1SvLpAP//IiVSIiVUu3ly6XYSqN2H6pC3FCmREimh2pmr7qjqobMk/+meXFnOdbfl/MHbx0n+fcCxFymREimh2ql5t9Msi1NS46x/izqLLafdhicDOBIVKZESKaECkRIpDjFSQgUiJVIiJVQgUoiUSAkViJRIcZCREioQKZESKaECkUKkREqoQKREioOMlFCBSImUSAkViBQiJVJCBSIlUhxkpIQKREqkREqoQKQQKZESKuhJa+2ZFURKpIQKREqkECmhApESKZESKjgEH1trJ2YQKZESKqjotrU2NoNIiZRQQUVvW2tXZhApkRIqqOh1a21iBpESKaGCip631uZmECmREiqoxk0TIiVSQgVlvWutnZtBpERKqKCi49banRlESqSECqpxqk+kREqooKw3rbUbM4iUSAkVVHOfZOSuPpESKaGCiryAV6RESqjAUZRIIVJCBat4k+SmtWYJkRIpoYJSbpOMk0SkREqkhAqqOU0yEymREimhgmouklx//YNIiZRICRVU8SHJ2bdvECmREimhggo+ZnEdai5SIiVSQgWV3CcZPQyUSImUSAkVVAjUON2NEiIlUiIlVFDF5yyuQc2+9xdESqRESqjgKTx6DUqkREqkhAqe2ockv/0oUCIlUiIlVNC3/3odlEiJlEghVFRwm+Q8/+f6k0iJlEghVPTtvovTzTrvLFIiJVLDc9SF6mdTsEOfk1wlmWSJa08iJVIixUPXSX43A1t22x013Wzjg4mUSInUsI2673Z/NQUbhmnSPe62+YFFSqREimRxCvCse5wkeWESHnGfxY0Pd91j2j12RqRECgCRAgCRAgCRAkCkAECkABApABApABApAEQKAEQKAJECAJECAJECQKQAQKQAECkAECkAECkARAoARAoAkQIAkQIAkQJApABApAAQKQAQKQBEygoAiBQAiBQAIgUAIgWASAGASAGASAEgUgAgUgCIFACIFACIFAAiBQAiBYBIAYBIAYBIASBSACBSAIgUAIgUAIgUACIFACIFgEgBgEgBIFIiBYBIAYBIASBSACBSAIgUAIgUAIgUACIFACIFgEgBgEgBgEgBIFIAIFIAiBQAiBQAiBQAIgUAIgWASAGASAGASAEgUgAgUgCIFACIFACIFAAiBQAiBYBIAYBIASBSACBSACBSAIgUAIgUACIFACIFACIFgEgBgEgBIFIAIFIAIFIAiBQAiBQAIgUAIgUAIgWASAGASAEgUgAgUgAgUgCIFACIFAAiBQAiBYBIWQEAkQIAkQJApABApAAQKQAQKQAQKQBECgBECgCRAgCRAgCRAkCkAECkABApABApABApAEQKAEQKAJECAJECAJECQKQAQKQAECkAECkAREqkABApABApAEQKAEQKAJECAJECAJECQKQAQKQAECkAECkAECkARAoARAoAkQIAkQIAkQJApABApAAQKQAQKQAQKQBECgBECgCRAgCRAgCRAkCkAECkABApABApAEQKAEQKAEQKAJECAJECQKQAQKQAQKQAECkAECkARAoARAoARAoAkQIAkQJApABApABApAAQKQAQKQBECgBECgBECgCRAgCRAkCkAECkABApKwAgUgAgUgCIFACIFAAiBQAiBQAiBYBIAYBIASBSACBSACBSAIgUAIgUACIFACIFACIFgEgBgEgBIFIAIFIAIFIAiBQAiBQAIgUAIgWASIkUACIFACIFgEgBgEgBIFIAIFIAIFIAiBQAiBQAIgUAIgUAIgWASAGASAEgUgAgUgAgUgCIFACIFAAiBQAiBQDf+GcAz3gyaMWLPusAAAAASUVORK5CYII=);
  background-size: 100% 100%;
}
.kb-keyboard__finished {
  border: 1px solid #2f62ed;
  background-color: #2f62ed;
  color: #ffffff;
}
.kb-keyboard__finished-base {
  border: 1px solid #cdd0d5;
  background-color: #cdd0d5;
  color: #1e1e1e;
}
.kb-keyboard__td-theme {
  border: 1px solid #cdd0d5;
  color: #1e1e1e;
  box-shadow: 3px 3px 1px #cdd0d5;
  -webkit-box-shadow: 3px 3px 1px #cdd0d5;
  background: #fff;
}
.kb-keyboard__td-tap-theme {
  border: 1px solid #cdd0d5;
  color: #1e1e1e;
  --from: #e5e2e2;
  --to: #e5e5e5;

  background: linear-gradient(130deg, var(--from), var(--to));
  background: -webkit-linear-gradient(130deg, var(--from), var(--to));
}
.kb-keyboard__td-tap-fin-theme {
  border: 1px solid #cdd0d5;
  color: #fff;
  --from: #cdd0d5;
  --to: #b8b8b9;

  background: linear-gradient(130deg, var(--from), var(--to));
  background: -webkit-linear-gradient(130deg, var(--from), var(--to));
}

.flex-center {
  display: flex;
  display: -webkit-flex;
  flex-direction: row;
  -webkit-flex-direction: row;
  align-items: center;
  -webkit-align-items: center;
  justify-content: center;
  -webkit-justify-content: center;
}

.row-wrap {
  display: flex;
  display: -webkit-flex;
  flex-direction: row;
  -webkit-flex-direction: row;
  flex-wrap: wrap;
  -webkit-flex-wrap: wrap;
}
</style>
