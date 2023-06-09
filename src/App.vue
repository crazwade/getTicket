<template>
  <div>
    <a href="https://klyscwdyg.com.tw" target="_blank">苓雅運動中心</a>
    <el-alert
      title="請確保你已經登入 苓雅運動中心"
      effect="dark"
      type="success"
      show-icon
      class="alert-message"
      :closable="false"
    />
    <el-alert
      title="建議在搶票前五分鐘登入並使用"
      effect="dark"
      type="success"
      show-icon
      class="alert-message"
      :closable="false"
    />
    <el-alert
      title="※在session有效的情形下，該搶票才會是有效的"
      effect="dark"
      type="success"
      show-icon
      class="alert-message"
      :closable="false"
    />
  </div>
  <el-button type="primary" @click="getTicket">TICKET</el-button>
  <div class="ticket-times">
    <div style="margin-top: 10px" v-for="(time, index) in ticketTimes" :key="index" class="time-list">
      <span class="item">運行紀錄: {{ time.count }}</span>
      <br>
      <span class="item">{{ time.orderTimeTitle }}:</span>
      <span class="item">{{ time.orderTimeDate }}</span>
      <br>
      <span class="item">{{ time.bookTimeTitle }}:</span>
      <span class="item">{{ time.bookTimeDate }}</span>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, Ref } from 'vue';

interface Time {
  count: number;
  orderTimeTitle: string;
  orderTimeDate: string;
  bookTimeTitle: string;
  bookTimeDate: string | null;
}

const ticketTimes: Ref<Time[]> = ref([]);

const getTicket = (): void => {
  let count: number = 1;
  // 正式要搶的場地
  // 籃球全場 第一場
  const bookDate: string | null = prompt('哪一天要打球? (自動選下午3-5) \n \n 例如: 2022/01/01');
  const bookingUrl: string = `https://bwd.xuanen.com.tw/wd16.aspx?module=net_booking&files=booking_place&StepFlag=25&QPid=15&QTime=15&PT=2&D=${bookDate}`;
  // 籃球全場 第二場
  const bookingUrl2: string = `https://bwd.xuanen.com.tw/wd16.aspx?module=net_booking&files=booking_place&StepFlag=25&QPid=15&QTime=16&PT=2&D=${bookDate}`;

  /** 訂場地 */
  function orderPlace(bookingUrl: string): void {
    postData(bookingUrl)
      .then(data => console.log(data)) // JSON from `response.json()` call
      .catch(error => console.error(error));
  }

  const goTicket = setInterval(() => {
    // 如果現在時間是凌晨12點整就執行
    if (
      new Date().getHours() === 0 &&
      new Date().getMinutes() === 0 &&
      new Date().getSeconds() === 0
    ) {
      setTimeout(() => {
        console.log('執行');
        orderPlace(bookingUrl);
        orderPlace(bookingUrl2);
      }, 200);
      clearInterval(goTicket);
    } else {
      const currentTime = new Date().toLocaleTimeString();
      const ticketTime = { count, orderTimeTitle: '現在時間', orderTimeDate: currentTime, bookTimeTitle: '預定時間', bookTimeDate: bookDate };
      ticketTimes.value.unshift(ticketTime);
    }
    count ++;
  }, 200);

  function postData(url: string): Promise<any> {
    return fetch(url, {
      cache: 'no-cache',
      credentials: 'same-origin',
      headers: {
        'user-agent': 'Mozilla/4.0 MDN Example',
        'content-type': 'application/json',
      },
      method: 'GET',
      mode: 'cors',
      redirect: 'follow',
      referrer: 'no-referrer',
    });
  }
}
</script>

<style scoped>

.item {
  margin: 0 10px;
}

.ticket-times {
  max-height: calc(100vh - 246px); /* 螢幕最大高度減去頂部和底部的空間 */
  overflow-y: auto; /* 添加垂直卷軸 */
}
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}

.alert-message {
  margin-bottom: 10px;
}
</style>
