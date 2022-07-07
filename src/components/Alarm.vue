<template>
  <div class="alarm">
    <!-- <div class="alarm__getdefault" @click="setData()">Get default</div> -->
    <div class="alarm__date">{{ today }}</div>
    <div class="alarm__main">
      <div class="alarm__clock alarm__hour">
        {{ hour }}
      </div>
      <div class="alarm__clock alarm__min">
        {{ min }}
      </div>
      <div class="alarm__clock alarm__sec">
        {{ sec }}
      </div>
    </div>
    <div class="alarm__setting">
      <div class="alarm__list">
        <div v-for="(alarm, index) in alarms" class="alarm__item" :key="index">
          <div style="display: flex; flex-direction: column">
            <div class="alarm__message">{{ alarm.message }}</div>
            <div class="alarm__time">
              {{ formatDateInList(getTimeFromDate(alarm.time)) }}
            </div>
          </div>
          <div class="alarm__delete">
            <i @click="deleteAlarm(index)" class="fa-solid fa-circle-xmark"></i>
          </div>
        </div>
      </div>
      <div class="alarm__add">
        <div class="label">Message</div>
        <textarea class="alarm__input-message" rows="5" v-model="message" />
        <div class="label">Time</div>
        <input class="alarm__input-time" type="datetime-local" v-model="time" />
        <div class="submit" @click="addAlarm(message, time)">Submit</div>
      </div>
    </div>
    <div class="alarm__label">
      Alarm by
      <i
        ><a
          target="_blank"
          href="https://www.facebook.com/dung.nguyentien.eddie"
          >EddieOnTheCode</a
        ></i
      >
    </div>
    <Bell v-if="showBell" :message="messageProp" :time="timeProp" />
  </div>
</template>

<script>
import axios from "axios";
import Bell from "./Bell";
export default {
  name: "Alarm",
  components: {
    Bell,
  },
  data() {
    return {
      year: 2000,
      month: 12,
      date: 31,
      hour: 12,
      min: 33,
      sec: 44,
      alarms: [],
      message: null,
      time: null,
      messageProp: "Init message",
      showBell: false,
      timeProp: null,
    };
  },
  async mounted() {
    this.setData();
    // this.resetPermission();
    this.requestPermission();
  },
  computed: {
    today() {
      return `${this.date}, ${this.numberToStringMonth(this.month)} ${
        this.year
      }`;
    },
  },
  methods: {
    resetPermission() {
      const permissionToRemove = {
        permissions: ["notification"],
      };

      browser.permissions.remove(permissionToRemove).then((result) => {
        console.log(result);
      });
    },
    async requestPermission() {
      // check notification permission
      this.granted = false;

      if (Notification.permission === "granted") {
        this.granted = true;
      } else if (Notification.permission !== "denied") {
        let permission = await Notification.requestPermission();
        this.granted = permission === "granted" ? true : false;
      }
    },
    /**
     * Cai dat du lieu
     * created by ntdung 04.03.2022
     */
    setData() {
      this.getTimeData();
      setInterval(() => {
        this.getTimeData();
      }, 1000);
    },
    /**
     * Lay du lieu moi giay
     * created by ntdung 04.03.2022
     */
    getTimeData() {
      var currTime = this.getTimeFromDate(new Date());
      this.year = currTime.year;
      this.month = currTime.month;
      this.date = currTime.date;
      this.hour = currTime.hour;
      this.min = currTime.min;
      this.sec = currTime.sec;
    },
    /**
     * Chuyen so thanh chu cho thang
     * created by ntdung 04.03.2022
     */
    numberToStringMonth(number) {
      number = +number;
      switch (number) {
        case 1:
          return "January";
        case 2:
          return "Febnuary";
        case 3:
          return "March";
        case 4:
          return "April";
        case 5:
          return "May";
        case 6:
          return "June";
        case 7:
          return "July";
        case 8:
          return "August";
        case 9:
          return "September";
        case 10:
          return "October";
        case 11:
          return "November";
        case 12:
          return "December";
        default:
          break;
      }
    },
    /**
     * Format ngay tu ngay truyen vao
     * created by ntdung 04.03.2022
     */
    getTimeFromDate(date) {
      var newDate = new Date(date);
      var resultDate = {};
      resultDate.year = newDate.getFullYear();
      resultDate.month = this.addZero(newDate.getMonth() + 1);
      resultDate.date = this.addZero(newDate.getDate());
      resultDate.hour = this.addZero(newDate.getHours());
      resultDate.min = this.addZero(newDate.getMinutes());
      resultDate.sec = this.addZero(newDate.getSeconds());
      return resultDate;
    },
    /**
     * Them so 0 vao neu nho hon 10
     * created by ntdung 04.03.2022
     */
    addZero(number) {
      return number < 10 ? "0" + number : number;
    },
    /**
     * Dinh dang ngay tren danh sach
     * created by ntdung 04.03.2022
     */
    formatDateInList(date) {
      return `${date.hour}:${date.min} ${date.date}/${date.month}/${date.year}`;
    },
    /**
     * Tao moi bao thuc
     * created by ntdung 04.03.2022
     */
    addAlarm(message, time) {
      if (!message) {
        alert("Message is required");
        return;
      }
      if (!time) {
        alert("Time is required");
        return;
      }
      if (new Date(time) < new Date()) {
        alert("Time must be over now");
        return;
      }
      var ids = this.alarms.map((alarm) => alarm.id);
      var maxid = Math.max(...ids);
      this.alarms.unshift({
        message: message,
        time: time,
        id: maxid,
      });
      this.$nextTick(() => {
        document.querySelector(".alarm__item").scrollIntoView();
        this.alarms[0].timeout = setTimeout(() => {
          this.messageProp = message;
          this.timeProp = this.formatDateInList(this.getTimeFromDate(time));
          this.showBell = true;
          var img =
            "https://st.depositphotos.com/1980975/3419/v/600/depositphotos_34193161-stock-illustration-golden-christmas-bell-on-white.jpg";
          var notification = new Notification("Notify from EddieAlarm", {
            body: message,
            icon: img,
          });
          notification.addEventListener("click", () => {
            window.open("https://www.facebook.com/dung.nguyentien.eddie");
          });
          setTimeout(() => {
            this.showBell = false;
            notification.close();
          }, 10000);
        }, this.getSecondFromTwoDates(new Date(), time) * 1000);
        this.time = null;
        this.message = null;
      });
    },
    /**
     * Lay so giay giua 2 ngay
     * created by ntdung 04.03.2022
     */
    getSecondFromTwoDates(startDate, endDate) {
      return (
        (new Date(endDate).getTime() - new Date(startDate).getTime()) / 1000
      );
    },
    /**
     * Xoa bao thuc
     * created by ntdung 04.03.2022
     */
    deleteAlarm(index) {
      clearTimeout(this.alarms[index].timeout);
      this.alarms.splice(index, 1);
    },
  },
};
</script>

<style lang="scss">
.alarm {
  display: flex;
  flex-direction: column;
  align-items: center;
  .alarm__label {
    margin-top: 20px;
  }
  .alarm__date {
    font-size: 30px;
  }
  .alarm__getdefault {
    margin-top: 10px;
    padding: 10px 20px;
    background: radial-gradient(#fff, #888);
    font-weight: bold;
    cursor: pointer;
  }
  .alarm__main {
    margin-top: 10px;
    height: 200px;
    width: 600px;
    /* border: 1px solid #ccc; */
    border-style: double;
    border-radius: 20px;
    border-width: 10px;
    border-color: #fff;
    background: rgba(2, 0, 36, 0.8);
    font-size: 120px;
    display: flex;
    justify-content: center;
    align-items: center;
    color: #fff;
    .alarm__clock {
      flex-basis: 33.3333%;
    }
    .alarm__hour {
      text-align: right;
    }
    .alarm__min {
      position: relative;
      &:after {
        content: ":";
        position: absolute;
      }
      &:before {
        content: ":";
        position: absolute;
        left: -8px;
      }
    }
    .alarm__sec {
      text-align: left;
    }
  }
  .alarm__setting {
    margin-top: 20px;
    width: 500px;
    display: flex;
    padding: 10px;
    border: 1px solid #ccc;
    border-radius: 14px;
    height: 200px;
    .alarm__list {
      width: 100%;
      display: flex;
      flex-direction: column;
      flex: 1;
      height: 100%;
      border: 1px solid #ccc;
      overflow-y: auto;
      overflow-x: hidden;
      /* width */
      &::-webkit-scrollbar {
        width: 8px;
      }

      /* Track */
      &::-webkit-scrollbar-track {
        background: #f1f1f1;
        border-radius: 10px;
      }

      /* Handle */
      &::-webkit-scrollbar-thumb {
        background: #888;
        border-radius: 10px;
      }

      /* Handle on hover */
      &::-webkit-scrollbar-thumb:hover {
        background: #555;
      }
      .alarm__item {
        text-align: left;
        padding: 10px 12px;
        display: flex;
        justify-content: space-between;
        align-items: center;
        &:hover {
          background: #ddd;
        }
        &:not(&:last-child) {
          border-bottom: 1px solid #ccc;
        }
        &:nth-child(4n + 1) {
          border-left: 8px solid rgb(98, 168, 98);
        }
        &:nth-child(4n + 2) {
          border-left: 8px solid rgb(223, 90, 90);
        }
        &:nth-child(4n + 3) {
          border-left: 8px solid rgb(110, 110, 231);
        }
        &:nth-child(4n + 4) {
          border-left: 8px solid rgb(238, 238, 58);
        }
        .alarm__message {
          font-weight: bold;
          margin-bottom: 8px;
        }
        .alarm__time {
        }
        .alarm__delete {
          &:hover {
            color: rgb(224, 77, 77);
            cursor: pointer;
          }
        }
      }
    }
    .alarm__add {
      border: 1px solid #ccc;
      margin-left: 10px;
      display: flex;
      flex-direction: column;
      padding: 10px;
      input,
      textarea {
        resize: none;
        outline: none;
        border: 1px solid #888;
        border-radius: 2px;
        &:focus {
          border: 1px solid green;
        }
      }
      .label {
        display: flex;
        justify-content: flex-start;
        font-size: 14px;
        font-weight: bold;
        &:not(&:first-child) {
          margin-top: 10px;
        }
      }
      .submit {
        margin-top: 10px;
        padding: 10px 20px;
        border: 1px solid #888;
        cursor: pointer;
        background: #ccc;
        transition: 0.3s all ease;
        &:hover {
          background: #ddd;
        }
        &:active {
          background: #888;
          color: #fff;
        }
      }
      .alarm__input-message {
      }
      .alarm__input-time {
      }
    }
  }
}
</style>