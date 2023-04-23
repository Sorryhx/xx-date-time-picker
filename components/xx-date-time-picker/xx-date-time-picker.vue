<script>
import {ref, computed} from 'vue'
export default {
  name: 'DateTimePicker',
  props: {
    years: {
      type: Array,
      default: () => {
        return [2000, new Date().getFullYear()]
      }
    },
    /**
     * 暗黑模式
     */
    mode: {
      type: String,
      default: 'light'
    }
  },
  emits: ['confirm'],
  setup (props, { emit }) {
    const show = ref(true)
    const dateTime = ref(new Date())
    const pickerValue = computed(() => {
      const now = dateTime.value
      return [now.getFullYear() - 2000, now.getMonth(), now.getDate() - 1, now.getHours(), now.getMinutes(), now.getSeconds()]
    })
    function geYears () {
      return rangeArr(props.years[0], props.years[1])
    }
    function geMonth () {
      return rangeArr(1, 12)
    }
    function geDays (month, year) {
      let end = 30
      if (month === 1) { // 2月
        end = isLeapYear(year) ? 29 : 28
      }
      if ([0, 2, 4, 6, 7, 9, 11].includes(month)) {
        end = 31
      }
      return rangeArr(1, end)
    }
    function isLeapYear(year) {
      return (year % 4 == 0 && year % 100 != 0) || (year % 400 == 0)
    }

    function getHours() {
      return rangeArr(0, 23)
    }
    function geMinute() {
      return rangeArr(0, 59)
    }
    function getSeconds() {
      return rangeArr(0, 59)
    }

    function rangeArr (start, end) {
      const re = []
      for (let i = start; i <= end; i++) {
        re.push(i)
      }
      return re
    }

    const dateObj = computed(() => {
      const now = dateTime.value
      const year = now.getFullYear()
      const month = now.getMonth()
      return {
        years: geYears(),
        months: geMonth(),
        days: geDays(month, year),
      }
    })
    const timeObj = {
      hours: getHours(),
      minutes: geMinute(),
      seconds: getSeconds()
    }

    function onChange (e) {
      const [year, month, day, hour, minute, second] = e.detail.value
      const dtMp = dateObj.value
      const tM = timeObj
      dateTime.value = new Date(`${dtMp.years[year]}/${dtMp.months[month]}/${dtMp.days[day]} ${tM.hours[hour]}:${tM.minutes[minute]}:${tM.seconds[second]}`)
    }

    function open(time = new Date()) {
      dateTime.value = time
      show.value = true
    }

    function onConfirm () {
      emit('confirm', dateTime.value)
      show.value = false
    }

    function onCancel() {
      show.value = false
    }


    return {
      show,
      dateTime,
      dateObj,
      timeObj,
      pickerValue,
      onChange,
      open,
      onConfirm,
      onCancel,
    }
  }
}
</script>

<template>
  <view
    v-if="show"
    :class="['date-time-picker-container', mode === 'dark' ? 'dark-date-time-picker' : 'light-date-time-picker']"
  >
    <view class="action">
      <text @click="onCancel">
        取消
      </text>
      <text @click="onConfirm">
        确认
      </text>
    </view>
    <picker-view
      class="date-time-picker"
      :value="pickerValue"
      @change="onChange"
    >
      <picker-view-column>
        <view
          v-for="(item,index) in dateObj.years"
          :key="index"
          class="item"
        >
          {{ item }}年
        </view>
      </picker-view-column>
      <picker-view-column>
        <view
          v-for="(item,index) in dateObj.months"
          :key="index"
          class="item"
        >
          {{ item }}月
        </view>
      </picker-view-column>
      <picker-view-column>
        <view
          v-for="(item,index) in dateObj.days"
          :key="index"
          class="item"
        >
          {{ item }}日
        </view>
      </picker-view-column>
      <picker-view-column>
        <view
          v-for="(item,index) in timeObj.hours"
          :key="index"
          class="item"
        >
          {{ item }}时
        </view>
      </picker-view-column>
      <picker-view-column>
        <view
          v-for="(item,index) in timeObj.minutes"
          :key="index"
          class="item"
        >
          {{ item }}分
        </view>
      </picker-view-column>
      <picker-view-column>
        <view
          v-for="(item,index) in timeObj.seconds"
          :key="index"
          class="item"
        >
          {{ item }}秒
        </view>
      </picker-view-column>
    </picker-view>
  </view>
</template>

<style lang="scss" scoped>
.dark-date-time-picker {
  color: #fff;
  background-color: #1B1C23;

  :deep(.uni-picker-view-mask) {
    background-image: linear-gradient(
      180deg,
      hsla(233,13%,12%, 0.95),
      hsla(233,13%,12%, 0.6)
      ),
      linear-gradient(0deg, hsla(233,13%,12%, 0.95), hsla(233,13%,12%, 0.6));
  }
}
.light-date-time-picker {
  color: #333;
  background-color: #fff;
}
.action {
  display: flex;
  justify-content: space-between;
  padding: 12px 10px;
  color: $uni-color-primary;
}
.date-time-picker-container {
  position: fixed;
  bottom: var(--window-bottom);
  left: 0;
  right: 0;
  height: 200px;
  padding: 0 10px 20px 10px;
  border-radius: 4px 4px 0 0;
}
.date-time-picker {
  height: 100%;
  width: 100%;
}

.item {
  line-height: 100rpx;
  text-align: center;
}
</style>