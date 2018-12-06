<template>
  <div id="app">
    <div class="calendar-wrap">
      <div class="step-list">
        <div class="item" :class="{'active': step >= 1}"></div>
        <div class="item" :class="{'active': step >= 2}"></div>
        <div class="item" :class="{'active': step >= 3}"></div>
        <div class="item" :class="{'active': step == 4}"></div>
      </div>
      <h4>{{langs[lang]['Запись']}} ЖК GreenSide</h4>
      <div class="step1" v-if="step == 1">
        <p>{{langs[lang]['Просмотр-время']}}:</p>
        <table class="appointments-timetable">
          <tbody>
            <tr>
              <td class="small placeholder">Пн - Сб:</td>
              <td>08:30 - 20:00</td>
            </tr>
            <tr>
              <td class="small placeholder">{{langs[lang]['вс']}}:</td>
              <td>10:00 - 18:00</td>
            </tr>
          </tbody>
        </table>
        <p>{{langs[lang]['Выберете-день']}}:</p>
        <br>
        <FunctionalCalendar :configs="calendarConfigs" @choseDay="clickDay"></FunctionalCalendar>
      </div>

      <div class="step2" v-if="step == 2">
        <p>
          {{langs[lang]['Выберете-время']}}
          <b>{{nameDay}}</b>
        </p>
        <br>

        <div class="time-bl">
          <div class="t-mini">{{langs[lang]['утро']}}</div>
          <div class="time-list">
            <div
              class="item"
              :class="{'active': form.time == item}"
              v-for="(item, i) in hours.d1"
              :key="i"
              @click="form.time = item"
            >{{item}}</div>
          </div>
        </div>

        <div class="time-bl">
          <div class="t-mini">{{langs[lang]['обед']}}</div>
          <div class="time-list">
            <div
              class="item"
              :class="{'active': form.time == item}"
              v-for="(item, i) in hours.d2"
              :key="i"
              @click="form.time = item"
            >{{item}}</div>
          </div>
        </div>

        <div class="time-bl">
          <div class="t-mini">{{langs[lang]['вечер']}}</div>
          <div class="time-list">
            <div
              class="item"
              :class="{'active': form.time == item}"
              v-for="(item, i) in hours.d3"
              :key="i"
              @click="form.time = item"
            >{{item}}</div>
          </div>
        </div>
      </div>

      <div class="step3" v-if="step == 3">
        <p>
          {{langs[lang]['вы-выбрали']}}
          <b>{{nameDay}} {{form.time}}</b>
          <br>
          {{langs[lang]['Пожалуйста, внесите свои данные']}}
        </p>
        <br>
        <form action>
          <div class="p1" style="max-width: 300px">
            <div class="form-group">
              <label>{{langs[lang]['Ваше имя']}} *</label>
              <input type="text" v-model="form.name" class="form-control">
            </div>
            <div class="form-group">
              <label>Телефон *</label>
              <input
                type="text"
                id="phone"
                v-model="form.phone"
                placeholder="Телефон: +38(___)-__-__-___"
                class="form-control"
              >
            </div>
          </div>

          <div style="margin-bottom: 30px">
            <label class="cus-check">
              <input v-model="form.callback" type="checkbox">
              <span class="ch"></span>
              <span class="title">{{langs[lang]['Напомните']}}</span>
            </label>
          </div>
          <div class="form-group">
            <label>E-mail *</label>
            <input type="email" v-model="form.email" class="form-control">
          </div>
          <div class="form-group">
            <label>{{langs[lang]['комментарий']}}</label>
            <textarea class="form-control" v-model="form.text"></textarea>
          </div>
        </form>
      </div>

      <div class="step4" v-if="step == 4">
        <h3>Спасибо! <br> Мы с вами свяжемся</h3>
      </div>

      <div class="alert alert-danger" v-if="formError">{{langs[lang]['error']}}</div>

      <div class="btns"  v-if="step < 4">
        <button class="btn" @click="step = step - 1" v-if="step > 1">Назад</button>
        <button
          class="btn"
          v-if="step < 3"
          :class="{'btn-def':btnActive}"
          @click="next"
        >{{langs[lang]['далее']}}</button>
        <button
          class="btn"
          v-if="step  == 3"
          :class="{'btn-def':btnActive}"
          @click="send"
        >{{langs[lang]['записаться']}}</button>
      </div>
    </div>
  </div>
</template>

<script>
import FunctionalCalendar from "vue-functional-calendar";
import Inputmask from "inputmask";

let langs = {
  ru: {
    Запись: "Запись на просмотр",
    "Просмотр-время":
      "Просмотры по предварительной записи проводятся в такое время",
    "Выберете-день": "Выберите, пожалуйста, удобный для вас день",
    "Выберете-время": "Выберите, пожалуйста, удобное для вас время в",
    утро: "Утро",
    обед: "Обед",
    вечер: "Вечер",
    далее: "Далее",
    вс: "Вс",
    "Пожалуйста, внесите свои данные":
      "Пожалуйста, внесите свои данные, чтобы мы смогли сообщить вам, что запись пидтерджено",
    "Ваше имя": "Ваше имя и фамилия",
    комментарий: "Дополнительный комментарий",
    записаться: "Записаться",
    Напомните: "Напомните звонком за день до встречи",
    "вы-выбрали": "Вы выбрали",
    error: "Заполните все поля со звездочкой!"
  },
  ua: {
    Запись: "Запис на перегляд",
    "Просмотр-время": "Перегляди за попереднім записом проводяться в такий час",
    "Выберете-день": "Оберіть, будь ласка, зручний для вас день",
    "Выберете-время": "Оберіть, будь ласка, зручний для вас час у",
    утро: "Ранок",
    обед: "Обід",
    вечер: "Вечір",
    далее: "Далі",
    вс: "Нд",
    "Пожалуйста, внесите свои данные":
      "Будь ласка, внесіть свої дані, щоб ми змогли повідомити вам, що запис підтерджено",
    "Ваше имя": "Ваше ім'я та прізвище",
    комментарий: "Додатковий коментар",
    записаться: "Записатися",
    Напомните: "Нагадайте дзвінком за день до зустрічі",
    "вы-выбрали": "Ви обрали",
    error: "Заповніть всі поля із зірочкою!"
  }
};

let monthNames = {
  ru: [
    "Январь",
    "Февраль",
    "Март",
    "Апрель",
    "Май",
    "Июнь",
    "Июль",
    "Август",
    "Сентябрь",
    "Октябрь",
    "Ноябрь",
    "Декабрь"
  ],
  ua: [
    "Січень",
    "Лютий",
    "Березень",
    "Квітень",
    "Травень",
    "Червень",
    "Липень",
    "Серпень",
    "Вересень",
    "Жовтень",
    "Листопад",
    "Грудень"
  ]
};

let monthNames2 = {
  ru: [
    "января",
    "февраля",
    "марта",
    "апреля",
    "мая",
    "июня",
    "июля",
    "августа",
    "сентября",
    "октября",
    "ноября",
    "декабря"
  ],
  ua: [
    "січеня",
    "лютого",
    "березня",
    "квітня",
    "травня",
    "червня",
    "липня",
    "серпня",
    "вересня",
    "жовтня",
    "листопада",
    "грудня"
  ]
};

let dayNames = {
  ru: ["Пн", "Вт", "Ср", "Чт", "Пт", "Сб", "Вс"],
  ua: ["Пн", "Вт", "Ср", "Чт", "Пт", "Сб", "Нд"]
};

if (!lang) {
  let lang = "ru";
}

export default {
  name: "app",
  data() {
    return {
      step: 1,
      lang: lang,
      langs: langs,
      dayNamesFull: {
        ru: [
          "понедельник",
          "вторник",
          "среду",
          "четверг",
          "пятницу",
          "субботу",
          "воскресенье"
        ],
        ua: [
          "понеділок",
          "вівторок",
          "середу",
          "четвер",
          "п’ятницю",
          "суботу",
          "неділю"
        ]
      },
      calendarConfigs: {
        sundayStart: false,
        isDatePicker: true,
        isDateRange: false,
        isMultiple: false,
        calendarsCount: 1,
        changeMonthFunction: false,
        changeYearFunction: false,
        markDateMore: [
          { date: "2017/11/20", className: "wh_chose_day wh_chose_day2" }
        ],
        //  agoDayHide: Math.floor(new Date().getTime()/1000) - 100000,
        agoDayHide: Math.floor(new Date().getTime() / 1000),
        futureDayHide: Math.floor(new Date().getTime() / 1000) + 2000000,
        dayNames: dayNames[lang],
        monthNames: monthNames[lang],
        isModal: false,
        applyStylesheet: true
      },
      day: {
        d1: ["8:30", "9:00", "9:30", "10:00", "10:30", "11:00", "11:30"],
        d2: [
          "12:00",
          "12:30",
          "13:00",
          "13:30",
          "14:00",
          "14:30",
          "15:00",
          "15:30"
        ],
        d3: [
          "16:00",
          "16:30",
          "17:00",
          "17:30",
          "18:00",
          "18:30",
          "19:00",
          "19:03",
          "20:00"
        ]
      },
      form: {
        date: null,
        time: null,
        phone: null,
        email: null,
        name: null,
        text: null,
        callback: false,
        type: "record"
      },
      formError: false
    };
  },
  components: {
    FunctionalCalendar
  },
  mounted() {},
  watch: {
    step() {
      if (this.step == 3) {
        setTimeout(() => {
          let im = new Inputmask({
            mask: "+38(999)-99-99-999",
            clearIncomplete: true,
            onincomplete: () => {
              this.form.phone = null;
            }
          });
          im.mask(document.getElementById("phone"));
        }, 100);
      }
    }
  },
  computed: {
    btnActive() {
      if (this.step == 1 && this.form.date != null) {
        return true;
      } else if (this.step == 2 && this.form.time != null) {
        return true;
      } else if (this.step == 3 && this.formValid) {
        return true;
      } else {
        return false;
      }
    },

    formValid() {
      let check = true;
      for (let item in this.form) {
        if (item != "text" && item != "callback") {
          if (!this.form[item]) {
            check = false;
          }
        }
      }
      if (!this.isAddress(this.form.email)) {
        check = false;
      }
      return check;
    },

    cDay() {
      let day = new Date(this.form.date);
      let numDay = day.getDay() - 1;
      if (numDay < 0) numDay = 6;
      return numDay;
    },

    hours() {
      if (this.cDay < 6) {
        return this.day;
      } else {
        let day = {
          d1: ["10:00", "10:30", "11:00", "11:30"],
          d2: this.day.d2,
          d3: ["16:00", "16:30", "17:00", "17:30", "18:00"]
        };
        return day;
      }
    },

    nameDay() {
      let day = new Date(this.form.date);
      let name =
        this.dayNamesFull[lang][this.cDay] +
        " " +
        day.getDate() +
        " " +
        monthNames2[lang][day.getMonth()];
      return name;
    }
  },
  methods: {
    setTime(item) {
      console.log(item);
    },
    isAddress(email) {
      let pattern = /^([A-Za-z0-9_\-.+])+@([A-Za-z0-9_\-.])+\.([A-Za-z]{2,})$/;
      if (pattern.test(email)) {
        return true;
      }
      return false;
    },
    clickDay(data) {
      $(".wh_chose_day2").removeClass("wh_chose_day");
      this.calendarConfigs.markDateMore[0].date = "2017/11/20";
      this.form.date = data;
    },
    next() {
      if (this.step < 4 && this.btnActive) {
        this.calendarConfigs.markDateMore[0].date = this.form.date;
        this.step++;
      }
    },
    send() {
      if (!this.btnActive) {
        this.formError = true;
        setTimeout(() => {
          this.formError = false;
        }, 3000);
        return;
      }

   //   this.step = 4;
//      return;

      $(".calendar-wrap form").addClass("loader");
      $.post(
        "https://greenside.com.ua/assets/plugins/requests.php",
        this.form,
        response => {
          console.log(response);
          if (response.type == "success") {
            $(".calendar-wrap form").removeClass("loader");
            this.step = 4;
            ga("send", "event", "record_form", "push");
          }
        },
        "json"
      );
    }
  }
};
</script>

<style lang="scss">

.loader{
  position: relative;
  &:after{
    content: "" !important;
    position: absolute;
    top: 0;
    bottom: 0;
    right: 0;
    left: 0;
    z-index: 100;
    background: rgba(255,255,255,.3);
    display: block !important;
  }
}
.step4{
  min-height: 300px;
  display: flex;
  align-items: center;
  justify-content: center;
  h3{
    text-align: center;
    line-height: 1.3;
  }
}
.calendar-wrap {
  max-width: 600px;
  margin: 30px auto;
  padding: 30px;
  background: rgb(250, 250, 250);

  .form-group {
    margin-bottom: 20px;
    label {
      display: inline-block;
      margin-bottom: 7px;
      opacity: 0.8;
    }
    .form-control {
      display: block;
      width: 100%;
      height: 40px;
      padding: 0.375rem 0.75rem;
      font-size: 1rem;
      line-height: 1.5;
      color: #495057;
      border-radius: 3px;
      background-color: #fff;
      background-clip: padding-box;
      border: 2px solid #ced4da;
      transition: border-color 0.15s ease-in-out, box-shadow 0.15s ease-in-out;
      &:focus {
        box-shadow: 0 0 0 0.2rem rgba(255, 166, 0, 0.25);
        border-color: rgba(255, 166, 0, 0.25);
      }
    }
    textarea.form-control {
      height: inherit;
      min-height: 70px;
    }
  }
  .main-container {
    margin: 0 auto !important;
  }
  .step-list {
    display: flex;
    justify-content: center;
    margin-bottom: 30px;
    .item {
      width: 8px;
      height: 8px;
      background-color: #e0e0e0;
      transition: background-color 0.3s ease-in-out;
      margin: 0 8px;
      &.active {
        background: #2e9130;
      }
    }
  }
  *:before,
  *:after {
    content: none;
  }
  h4 {
    margin: 0 0 15px;
  }
  p {
    margin: 0 0 10px;
    display: block;
    font-size: 16px;
  }
  .mark1 {
    //  opacity: .5 !important;
  }
  .btns {
    margin-top: 30px;
    display: flex;
    justify-content: flex-end;
    .btn {
      margin: 0 8px;
    }
  }
  .btn {
    border-radius: 25px;
    padding: 12px 24px;
    font-size: 14px;
    font-weight: 700;
    text-transform: uppercase;
    background: #dddddd;
    line-height: 1.2;
    height: inherit;
  }
  .btn-def {
    background: #ffd500;
    text-transform: uppercase;
    color: #4d4d4d;
  }
  .t-mini {
    opacity: 0.7;
    font-size: 14px;
    margin-bottom: 8px;
  }
  .time-bl {
    margin-bottom: 25px;
  }
  .time-list {
    display: flex;
    flex-wrap: wrap;
    margin-left: -8px;
    margin-right: -8px;
    .item {
      margin: 4px;
      font-size: 14px;
      padding: 8px 16px;
      background: #ececec;
      border-radius: 16px;
      cursor: pointer;
      transition: all 0.3s ease;
      &:hover {
        background: #dddddd;
      }
      &.active {
        background: #7c7c7c;
        color: #fff;
      }
    }
  }
  .alert {
    position: relative;
    padding: 0.75rem 1.25rem;
    margin-bottom: 1rem;
    border: 1px solid transparent;
    border-radius: 0.25rem;
    text-align: center;
  }
  .alert-danger {
    color: #721c24;
    background-color: #f8d7da;
    border-color: #f5c6cb;
  }
}
.appointments-timetable {
  margin: 8px auto 16px;
}
.appointments-timetable .small {
  line-height: 1.6;
  font-size: 13px;
  color: grey;
  text-align: right;
}
.appointments-timetable td {
  padding: 4px;
}

.cus-check {
  cursor: pointer;
  display: inline-flex;
  align-items: center;
  position: relative;
  font-size: 14px;
  &.big {
    font-size: 18px;
    .ch {
      width: 26px;
      height: 26px;
      margin-right: 12px;
    }
    input:checked + .ch {
      &:before {
        width: 8px;
        height: 11px;
        margin-top: -7px;
        margin-left: -4px;
      }
    }
  }
  input {
    position: absolute;
    opacity: 0;
    &:checked + .ch {
      border-color: #ffd500;
      background: #ffd500;
      &:before {
        content: "";
        position: absolute;
        width: 6px;
        height: 9px;
        left: 50%;
        top: 50%;
        margin-top: -6px;
        margin-left: -3px;
        border-right: 2px solid #fff;
        border-bottom: 2px solid #fff;
        transform: rotate(40deg);
      }
    }

    &:disabled ~ * {
      opacity: 0.6;
      cursor: not-allowed;
    }
  }
  .ch {
    width: 20px;
    height: 20px;
    border-radius: 3px;
    border: 2px solid #ced4da;
    display: inline-block;
    margin-right: 8px;
    margin-bottom: -1px;
    transition: all 0.3s ease;
    position: relative;
  }
}
</style>
