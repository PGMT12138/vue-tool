<script setup>
import {ref, onMounted} from 'vue';

// 假期列表
const holidays = ref([
  {
    name: '元旦',
    date: '2025-01-01',
    endDate: '2025-01-01'
  },
  {
    name: '春节',
    date: '2025-01-28',
    endDate: '2025-02-04'
  },
  {
    name: '清明节',
    date: '2025-04-04',
    endDate: '2025-04-06'
  },
  {
    name: '劳动节',
    date: '2025-05-01',
    endDate: '2025-05-05'
  },
  {
    name: '端午节',
    date: '2025-05-31',
    endDate: '2025-06-02'
  },
  {
    name: '中秋节和国庆节',
    date: '2025-10-01',
    endDate: '2025-10-08'
  }
])

onMounted(() => {
  const now = new Date();
  timeClock.value = {
    year: now.getFullYear().toString().substring(2),
    month: now.getMonth() + 1,
    day: now.getDate(),
    hour: now.getHours(),
    minute: now.getMinutes(),
    second: now.getSeconds()
  }
  initHoliday(now);
  initWeekend(now);
  initWages(now);
  setInterval(() => {
    const today = new Date();
    timeClock.value.year = today.getFullYear().toString().substring(2);
    timeClock.value.month = today.getMonth() + 1;
    timeClock.value.day = today.getDate();
    timeClock.value.hour = today.getHours();
    timeClock.value.minute = today.getMinutes();
    timeClock.value.second = today.getSeconds();
    calcHoliday(today);
    calcWeekend(today);
    calcWages(today);
  }, 1000);
})
const timeClock = ref({
  year: '',
  month: 0,
  day: 0,
  hour: 0,
  minute: 0,
  second: 0
});
const holidayCountDown = ref({
  day: 0,
  hour: 0,
  minute: 0,
  second: 0,
  title: ''
});
const weekendCountDown = ref({
  day: 0,
  hour: 0,
  minute: 0,
  second: 0,
  title: ''
});
const wagesCountDown = ref({
  day: 0,
  hour: 0,
  minute: 0,
  second: 0,
  title: ''
})

function initHoliday(now) {
  // 遍历假期列表，计算最近的假期倒计时
  for (let holiday of holidays.value) {
    const startDate = new Date(holiday.date + ' 00:00:00');
    const endDate = new Date(holiday.endDate + ' 23:59:59');
    let title = holiday.name + "开始 " + holiday.date;
    let diff = startDate - now;
    if (diff <= 0) {
      diff = endDate - now;
      title = holiday.name + "结束 " + holiday.endDate;
    }
    if (diff > 0) {
      holidayCountDown.value.title = title;
      holidayCountDown.value.day = Math.floor(diff / (24 * 3600 * 1000));
      holidayCountDown.value.hour = Math.floor(diff % (24 * 3600 * 1000) / (3600 * 1000));
      holidayCountDown.value.minute = Math.floor(diff % (3600 * 1000) / (60 * 1000));
      holidayCountDown.value.second = Math.floor(diff % (60 * 1000) / 1000);
      break;
    }
  }
}

function calcHoliday(now) {
  if (holidayCountDown.value.second === 0) {
    holidayCountDown.value.second = 59;
    if (holidayCountDown.value.minute === 0) {
      holidayCountDown.value.minute = 59;
      if (holidayCountDown.value.hour === 0) {
        holidayCountDown.value.hour = 23;
        if (holidayCountDown.value.day === 0) {
          initHoliday(now);
        } else {
          holidayCountDown.value.day--;
        }
      } else {
        holidayCountDown.value.hour--;
      }
    } else {
      holidayCountDown.value.minute--;
    }
  } else {
    holidayCountDown.value.second--;
  }
}

function initWeekend(today) {
  var dayOfWeek = today.getDay(); // 获取今天是周几，0是周日，6是周六
  var saturday = new Date(today);
  var sunday = new Date(today);
  // 计算周六的日期
  saturday.setDate(today.getDate() + (6 - dayOfWeek));
  saturday = new Date(saturday.getFullYear(), saturday.getMonth(), saturday.getDate());
  // 计算周日的日期
  sunday.setDate(today.getDate() + (7 - dayOfWeek));
  sunday = new Date(sunday.getFullYear(), sunday.getMonth(), sunday.getDate(), 23, 59, 59);
  // 计算周末的倒计时
  let title = "周末开始 " + saturday.getFullYear() + "-" + String((saturday.getMonth() + 1)).padStart(2, '0') + "-" + String(saturday.getDate()).padStart(2, '0');
  let diff = saturday - today;
  if (diff <= 0) {
    diff = sunday - today;
    title = "周末结束 " + sunday.getFullYear() + "-" + String((sunday.getMonth() + 1)).padStart(2, '0') + "-" + String(sunday.getDate()).padStart(2, '0');
  }
  if (diff > 0) {
    weekendCountDown.value.title = title;
    weekendCountDown.value.day = Math.floor(diff / (24 * 3600 * 1000));
    weekendCountDown.value.hour = Math.floor(diff % (24 * 3600 * 1000) / (3600 * 1000));
    weekendCountDown.value.minute = Math.floor(diff % (3600 * 1000) / (60 * 1000));
    weekendCountDown.value.second = Math.floor(diff % (60 * 1000) / 1000);
  }
}

function calcWeekend(now) {
  if (weekendCountDown.value.second === 0) {
    weekendCountDown.value.second = 59;
    if (weekendCountDown.value.minute === 0) {
      weekendCountDown.value.minute = 59;
      if (weekendCountDown.value.hour === 0) {
        weekendCountDown.value.hour = 23;
        if (weekendCountDown.value.day === 0) {
          initWeekend(now);
        } else {
          weekendCountDown.value.day--;
        }
      } else {
        weekendCountDown.value.hour--;
      }
    } else {
      weekendCountDown.value.minute--;
    }
  } else {
    weekendCountDown.value.second--;
  }
}

function initWages(today) {
  // 获取当前年份和月份
  var year = today.getFullYear();
  var month = today.getMonth();
  // 创建一个新的日期对象，设置为下个月的第一天
  var startDate = new Date(year, month + 1, 1);
  var lastDayOfMonth = new Date(year, month + 1, 1);
  // 将日期减去一天，得到当月最后一天
  lastDayOfMonth.setDate(lastDayOfMonth.getDate() - 1);
  let diff = startDate - today;
  if (diff > 0) {
    wagesCountDown.value.title = "发粮 " + lastDayOfMonth.getFullYear() + "-" + String((lastDayOfMonth.getMonth() + 1)).padStart(2, '0') + "-" + String(lastDayOfMonth.getDate()).padStart(2, '0');
    wagesCountDown.value.day = Math.floor(diff / (24 * 3600 * 1000));
    wagesCountDown.value.hour = Math.floor(diff % (24 * 3600 * 1000) / (3600 * 1000));
    wagesCountDown.value.minute = Math.floor(diff % (3600 * 1000) / (60 * 1000));
    wagesCountDown.value.second = Math.floor(diff % (60 * 1000) / 1000);
  }
}

function calcWages(today) {
  if (wagesCountDown.value.second === 0) {
    wagesCountDown.value.second = 59;
    if (wagesCountDown.value.minute === 0) {
      wagesCountDown.value.minute = 59;
      if (wagesCountDown.value.hour === 0) {
        wagesCountDown.value.hour = 23;
        if (wagesCountDown.value.day === 0) {
          initWages(today);
        } else {
          wagesCountDown.value.day--;
        }
      } else {
        wagesCountDown.value.hour--;
      }
    } else {
      wagesCountDown.value.minute--;
    }
  } else {
    wagesCountDown.value.second--;
  }
}

</script>

<template>
  <div style="display: flex; justify-content: space-around; flex-wrap: wrap;">
    <div class="flex gap-6" style="margin-bottom: 30px; color: #f2f5e3;">
      <div>
        <span class="countdown font-mono text-4xl">
          <span :style="{ '--value': 20}"></span>
          <span :style="{ '--value': timeClock.year}"></span>
        </span>
        年
      </div>
      <div>
        <span class="countdown font-mono text-4xl">
          <span :style="{ '--value': timeClock.month}"></span>
        </span>
        月
      </div>
      <div>
    <span class="countdown font-mono text-4xl">
      <span :style="{ '--value': timeClock.day}"></span>
    </span>
        号
      </div>
      <div>
    <span class="countdown font-mono text-4xl">
      <span :style="{ '--value': timeClock.hour}"></span>
    </span>
        时
      </div>
      <div>
    <span class="countdown font-mono text-4xl">
      <span :style="{ '--value': timeClock.minute}"></span>
    </span>
        分
      </div>
      <div>
    <span class="countdown font-mono text-4xl">
      <span :style="{ '--value': timeClock.second}"></span>
    </span>
        秒
      </div>
    </div>

    <div style="display: flex; justify-content: space-around;">

      <div class="card bg-base-100 w-96 shadow-xl" style="margin-right: 10px;">
        <div class="card-body items-center text-center" style="padding: 20px 32px;">
          <h1 class="card-title">{{ holidayCountDown.title }}</h1>
          <!-- 倒计时开始 -->
          <div class="grid auto-cols-max grid-flow-col gap-5 text-center">
            <div class="bg-neutral rounded-box text-neutral-content flex flex-col p-2">
        <span class="countdown font-mono text-5xl">
         <span :style="{ '--value': holidayCountDown.day}"></span>
        </span>
              天
            </div>
            <div class="bg-neutral rounded-box text-neutral-content flex flex-col p-2">
        <span class="countdown font-mono text-5xl">
          <span :style="{ '--value': holidayCountDown.hour}"></span>
        </span>
              时
            </div>
            <div class="bg-neutral rounded-box text-neutral-content flex flex-col p-2">
        <span class="countdown font-mono text-5xl">
          <span :style="{ '--value': holidayCountDown.minute}"></span>
        </span>
              分
            </div>
            <div class="bg-neutral rounded-box text-neutral-content flex flex-col p-2">
        <span class="countdown font-mono text-5xl">
          <span :style="{ '--value': holidayCountDown.second}"></span>
        </span>
              秒
            </div>
          </div>
          <!-- 倒计时结束 -->
        </div>
      </div>

      <div class="card bg-base-100 w-96 shadow-xl" style="margin-right: 10px;">
        <div class="card-body items-center text-center" style="padding: 20px 32px;">
          <h1 class="card-title">{{ weekendCountDown.title }}</h1>
          <!-- 倒计时开始 -->
          <div class="grid auto-cols-max grid-flow-col gap-5 text-center">
            <div class="bg-neutral rounded-box text-neutral-content flex flex-col p-2">
        <span class="countdown font-mono text-5xl">
         <span :style="{ '--value': weekendCountDown.day}"></span>
        </span>
              天
            </div>
            <div class="bg-neutral rounded-box text-neutral-content flex flex-col p-2">
        <span class="countdown font-mono text-5xl">
          <span :style="{ '--value': weekendCountDown.hour}"></span>
        </span>
              时
            </div>
            <div class="bg-neutral rounded-box text-neutral-content flex flex-col p-2">
        <span class="countdown font-mono text-5xl">
          <span :style="{ '--value': weekendCountDown.minute}"></span>
        </span>
              分
            </div>
            <div class="bg-neutral rounded-box text-neutral-content flex flex-col p-2">
        <span class="countdown font-mono text-5xl">
          <span :style="{ '--value': weekendCountDown.second}"></span>
        </span>
              秒
            </div>
          </div>
          <!-- 倒计时结束 -->
        </div>
      </div>

      <div class="card bg-base-100 w-96 shadow-xl">
        <div class="card-body items-center text-center" style="padding: 20px 32px;">
          <h1 class="card-title">{{ wagesCountDown.title }}</h1>
          <!-- 倒计时开始 -->
          <div class="grid auto-cols-max grid-flow-col gap-5 text-center">
            <div class="bg-neutral rounded-box text-neutral-content flex flex-col p-2">
        <span class="countdown font-mono text-5xl">
         <span :style="{ '--value': wagesCountDown.day}"></span>
        </span>
              天
            </div>
            <div class="bg-neutral rounded-box text-neutral-content flex flex-col p-2">
        <span class="countdown font-mono text-5xl">
          <span :style="{ '--value': wagesCountDown.hour}"></span>
        </span>
              时
            </div>
            <div class="bg-neutral rounded-box text-neutral-content flex flex-col p-2">
        <span class="countdown font-mono text-5xl">
          <span :style="{ '--value': wagesCountDown.minute}"></span>
        </span>
              分
            </div>
            <div class="bg-neutral rounded-box text-neutral-content flex flex-col p-2">
        <span class="countdown font-mono text-5xl">
          <span :style="{ '--value': wagesCountDown.second}"></span>
        </span>
              秒
            </div>
          </div>
          <!-- 倒计时结束 -->
        </div>
      </div>

    </div>
  </div>


</template>

<style scoped>

</style>