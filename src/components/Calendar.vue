<template>
  <div class="calendar">
    <collapse2 title="カレンダー" :isOpen='true'>
      <template v-slot:header>
        <el-button @click='goThisMonth()' circle size="mini" style="float:right;"><fa icon="home"/></el-button>
      </template>
      <el-button @click='addMonth(-1)' circle size="mini" class="middle"><fa icon="arrow-left"/></el-button>
      <template v-for='(calendar, ci) in calendars'>
        <table :key='ci' class="calendar">
          <tr>
            <th colspan="7" class="HEAD">{{calendar.year}}/{{calendar.month}}</th>
          </tr>
          <tr>
            <template v-for='(head, hi) in heads'>
              <th :key='hi' :class='heads[(hi + dayOfWeekHead) % 7]'>{{heads[(hi + dayOfWeekHead) % 7]}}</th>
            </template>
          </tr>
          <template v-for='(week, wi) in calendar.weeks'>
            <tr :key='wi'>
              <template v-for='(day, di) in week.days'>
                <template v-if='day.inMonth'>
                  <td :key='di' :class='day.class' :title='day.holiday'>
                    {{day.date}}
                  </td>
                </template>
                <template v-else>
                  <td :key='di' class="NONE">&nbsp;</td>
                </template>
              </template>
            </tr>
          </template>
        </table>
      </template>
      <el-button @click='addMonth(+1)' circle size="mini" class="middle"><fa icon="arrow-right"/></el-button>
    </collapse2>
  </div>
</template>

<script>
import Collapse2 from './Collapse2'

export default {
  name: 'calendar',
  components: { Collapse2 },
  data: function () {
    return {
      calendars: [],
      diffMonth: 0,
      dayOfWeekHead: 0, // 0:日曜始まり, 1:月曜始まり
      heads: [ 'SUN', 'MON', 'TUE', 'WED', 'THU', 'FRI', 'SAT' ],
      holidays: {
        "2019/1/1": "元日",
        "2019/1/14": "成人の日",
        "2019/2/11": "建国記念の日",
        "2019/3/21": "春分の日",
        "2019/4/29": "昭和の日",
        "2019/4/30": "休日",
        "2019/5/1": "休日（祝日扱い）",
        "2019/5/2": "休日",
        "2019/5/3": "憲法記念日",
        "2019/5/4": "みどりの日",
        "2019/5/5": "こどもの日",
        "2019/5/6": "休日",
        "2019/7/15": "海の日",
        "2019/8/11": "山の日",
        "2019/8/12": "休日",
        "2019/9/16": "敬老の日",
        "2019/9/23": "秋分の日",
        "2019/10/14": "体育の日（スポーツの日）",
        "2019/10/22": "休日（祝日扱い）",
        "2019/11/3": "文化の日",
        "2019/11/4": "休日",
        "2019/11/23": "勤労感謝の日",
        "2020/1/1": "元日",
        "2020/1/13": "成人の日",
        "2020/2/11": "建国記念の日",
        "2020/2/23": "天皇誕生日",
        "2020/2/24": "休日",
        "2020/3/20": "春分の日",
        "2020/4/29": "昭和の日",
        "2020/5/3": "憲法記念日",
        "2020/5/4": "みどりの日",
        "2020/5/5": "こどもの日",
        "2020/5/6": "休日",
        "2020/7/23": "海の日",
        "2020/7/24": "スポーツの日",
        "2020/8/10": "山の日",
        "2020/9/21": "敬老の日",
        "2020/9/22": "秋分の日",
        "2020/11/3": "文化の日",
        "2020/11/23": "勤労感謝の日"
      }
    }
  },
  mounted: function () {
    this.updateCalender()
  },
  methods: {
    goThisMonth: function () {
      this.diffMonth = 0
      this.updateCalender()
    },
    addMonth: function (diff) {
      this.diffMonth += diff
      this.updateCalender()
    },
    updateCalender: function () {
      const today = new Date()
      this.calendars.splice(0, this.calendars.length)
      const thisMonth = new Date(today.getFullYear(), today.getMonth() + this.diffMonth, 1)
      this.addCalendar(thisMonth.getFullYear(), thisMonth.getMonth() + 1)
      const nextMonth = new Date(thisMonth.getFullYear(), thisMonth.getMonth() + 1, 1)
      this.addCalendar(nextMonth.getFullYear(), nextMonth.getMonth() + 1)
    },
    addCalendar: function (year, month) {
      const today = new Date()
      const calendar = {
        year: year,
        month: month,
        weeks: []
      }
      const date = new Date(calendar.year, calendar.month - 1, 1)
      const firstDayOfWeek = date.getDay() - this.dayOfWeekHead
      date.setTime(date.getTime() - firstDayOfWeek * 24 * 3600 * 1000)
      for (let w = 0; w < 6; w++) {
        const week = {
          week: w,
          days: []
        }
        for (let d = 0; d < 7; d++) {
          const ymd = date.getFullYear() + '/' + (date.getMonth() + 1) + '/' + date.getDate()
          const day = {
            ymd: ymd,
            holiday: this.holidays[ymd],
            dateObject: new Date(date.getTime()),
            dayOfWeek: date.getDay(),
            date: date.getDate(),
            inMonth: calendar.month === date.getMonth() + 1,
            class: {}
          }
          day.class['TODAY'] = date.getFullYear() === today.getFullYear() && date.getMonth() === today.getMonth() && date.getDate() === today.getDate()
          day.class[this.heads[day.dayOfWeek]] = true
          day.class['HOLIDAY'] = day.holiday !== undefined
          date.setTime(date.getTime() + 24 * 3600 * 1000)
          week.days.push(day)
        }
        calendar.weeks.push(week)
      }
      this.calendars.push(calendar)
    },
    n2 : function (n) {
      return n > 10 ? '' : '0' + n
    }
  }
}
</script>

<style scoped>
.el-button.top {
  position: absolute;
}
.el-button.middle {
  position: relative;
  top: 5em;
}
table.calendar {
  vertical-align: top;
  margin: 0 0.3em;
  display: inline-block;
}
th.HEAD {
  color: #82ab2c;
  font-size: small;
}
th.SUN, th.MON, th.TUE, th.WED, th.THU, th.FRI, th.SAT {
  font-size: xx-small;
  line-height: 100%;
}
th.SUN {
  color: #a02;
}
th.MON {
  color: #a40;
}
th.TUE {
  color: #9a0;
}
th.WED {
  color: #2a0;
}
th.THU {
  color: #0a4;
}
th.FRI {
  color: #09a;
}
th.SAT {
  color: #02a;
}
td.SUN, td.MON, td.TUE, td.WED, td.THU, td.FRI, td.SAT, td.NONE {
  text-align: center;
  font-weight: bold;
  padding: 0;
  font-size: small;
  line-height: 110%;
  border-style: solid;
  border-width: 0 0 2px 0;
  border-color: transparent;
}
td.SUN {
  color: #a02;
}
td.MON {
  color: #a40;
}
td.TUE {
  color: #9a0;
}
td.WED {
  color: #2a0;
}
td.THU {
  color: #0a4;
}
td.FRI {
  color: #09a;
}
td.SAT {
  color: #02a;
}
td.HOLIDAY {
  border-color: red;
}
td.HOLIDAY.SAT, td.HOLIDAY.SUN {
  border-color: transparent;
}
td.SUN.TODAY {
  color: #fff;
  background: #b84536;
}
td.MON.TODAY {
  color: #fff;
  background: #b86936;
}
td.TUE.TODAY {
  color: #fff;
  background: #c29f28;
}
td.WED.TODAY {
  color: #fff;
  background: #82ab2c;
}
td.THU.TODAY {
  color: #fff;
  background: #28c25e;
}
td.FRI.TODAY {
  color: #fff;
  background: #2695b8;
}
td.SAT.TODAY {
  color: #fff;
  background: #3257b8;
}
</style>