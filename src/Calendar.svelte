<script>
  import { days, months } from "./data.js";

  export let startOnMonday = false;
  export let selectedDate = new Date();
  export let events = () => {};

  selectedDate = selectedDate.toDateString();

  const numOfCell = 42;
  const shiftDays = startOnMonday ? 1 : 0;
  const daysOfWeek = startOnMonday ? shiftDaysOfWeek(days) : days;

  let now = new Date();

  $: month = now.getMonth();
  $: year = now.getFullYear();
  $: dates = getDates(month, year);

  function shiftDaysOfWeek(days) {
    const arr = [...days];
    const day = arr.shift();
    arr.push(day);
    return arr;
  }

  function getDates(month, year) {
    const days = [];

    const sDay = new Date(year, month, 1).getDay() - shiftDays;
    const daysInLastMonth = new Date(year, month, 0).getDate();

    let startDay = daysInLastMonth - sDay + 1;

    for (let i = 0; i < numOfCell; i++) {
      const fullDate = new Date(year, month - 1, startDay);
      const date = fullDate.getDate();
      const m = fullDate.getMonth();
      const d = fullDate.getDay();
      const dateToString = fullDate.toDateString();

      const cls = ["date"];

      m !== month ? cls.push("is-hidden") : null;
      d == 0 || d == 6 ? cls.push("is-weekend") : null;

      const eventCls = events(fullDate);
      if (eventCls) cls.push(eventCls);

      days.push({
        fullDate,
        dateToString,
        date,
        classes: cls,
        selected: dateToString == selectedDate
      });

      startDay++;
    }

    return days;
  }

  function changeMonth(action) {
    switch (action) {
      case "up":
        month++;
        break;

      case "down":
        month--;
        break;

      default:
        break;
    }

    if (month > 11) {
      month = 0;
      year++;
    }

    if (month < 0) {
      month = 11;
      year--;
    }
  }

  function changeDate(e) {
    const ind = e.target.getAttribute("data-index");
    if (!ind) return;
    dates = dates.map((date, index) => {
      date.selected = index == ind;
      return date;
    });
    selectedDate = dates[ind].dateToString;
  }
</script>

<style>
  .calendar {
    box-sizing: border-box;
    width: 300px;
    margin: 0 auto;
    margin-bottom: 20px;
    padding: 10px;
    border: 1px solid #ccc;
  }

  .control {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  .arrow {
    padding: 10px;
    cursor: pointer;
    user-select: none;
  }

  .arrow:hover {
    color: red;
  }

  .days {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    grid-template-rows: 30px;
  }

  .day {
    align-self: center;
    text-align: center;
  }

  .dates {
    display: grid;
    grid-template-columns: repeat(7, auto);
    grid-template-rows: repeat(6, 40px);
  }

  .date {
    align-self: center;
    text-align: center;
    user-select: none;
  }

  .is-hidden {
    color: #ccc;
  }

  .is-weekend {
    color: red;
  }

  .highlight {
    display: inline-block;
    width: 25px;
    height: 25px;
    border-radius: 50%;
    cursor: pointer;
  }

  .highlight:hover {
    color: #fff;
    background-color: red;
  }

  .is-selected,
  .is-selected:hover {
    color: #fff;
    background-color: blue;
  }
</style>

<div class="calendar">

  <div class="control">
    <div class="arrow" on:click={() => changeMonth('down')}>&#60</div>
    <div>{months[month]} {year}</div>
    <div class="arrow" on:click={() => changeMonth('up')}>&#62</div>
  </div>

  <div class="days">
    {#each daysOfWeek as day}
      <div class="day">{day}</div>
    {/each}
  </div>

  <div class="dates" on:click={e => changeDate(e)}>
    {#each dates as date, index}
      <div class={date.classes.join(' ')}>

        <span
          data-index={index}
          class="highlight {date.selected ? 'is-selected' : ''}">
          {date.date}
        </span>

      </div>
    {/each}
  </div>

</div>
